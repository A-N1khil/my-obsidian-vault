## Path Parameters
A path parameter identifies a specific resource through the URL itself.

Example endpoint
```python
@router.get("/{application_id}")
def get_application(application_id: int):
    application = applications.get(application_id)
    if application is None:
        raise HTTPException(status_code=404, detail="Application not found")
    return application
```

- If we call this endpoint with `localhost:8000/applications/42`, FastAPI will translate 42 as the `application_id`
- Since the variable `application_id` is typed to be an Integer, only Integer values will be accepted
	- Values like "Hello" or a UUID will result in an error code of 422
- Path parameters can also be strings or a UUID

```python
def get_application(application_id: str) # Accepts a string
```

```python
from uuid import UUID

def get_application(application_id: UUID) # Accepts a UUID
```

### Path parameter validation

```python
from fastapi import Path

@app.get("/applications/{application_id}")
def get_application(
    application_id: int = Path(gt=0)
):
    return {"application_id": application_id}
```

This endpoint will only allow positive integers $>0$ to pass through. Other validations include
- `ge` - greater than or equal to
- `le` - less than or equal to
- `lt` - less than

### Multiple Path Parameters

```python
@app.get("/companies/{company_id}/applications/{application_id}")
def get_application(
    company_id: int,
    application_id: int
):
    return {
        "company_id": company_id,
        "application_id": application_id
    }
```

Supports the URL `GET /companies/10/applications/42`

## Query Parameters

`GET /applications?status=INTERVIEW`

Thus, `status` becomes one of the query parameters

```python
@router.post("/post", status_code=status.HTTP_201_CREATED)
def add_company(company: CompanyCreate):
    return create_company(company)
```

### Optional Query Parameters

Marking the parameter as `None` makes it optional.
```python
@app.get("/applications")
def get_applications(status: str | None = None):
```

### Query Parameters Validation

Using the `Query` class from FastAPI, we can perform validation checks on the Query parameters
```python
from fastapi import Query

@app.get("/applications")
def get_applications(
    company: str | None = Query(
        default=None,
        min_length=2,
        max_length=100,
        description = "Filter applications by company name" # goes into Swagger docs
    )
):
    return {"company": company}
```

## Path vs Query parameters

- Path parameters are used to identify a particular resource
- Query parameters are mostly used to filter out resources based on conditions

## Request Bodies
Before understanding a request body, we need to understand [[Pydantic Models]]
Once we have understood how Pydantic models work, all we need to do is create an endpoint

```python
# models/application.py
class ApplicationCreate(BaseModel):
    company: str
    role: str
    location: str | None = None

# routers/application.py
@app.post("/applications")
def create_application(application: ApplicationCreate):
    return application
```

Assuming we call this endpoint with a JSON
```json
{
  "company": "Anthropic",
  "role": "Software Engineer",
  "location": "San Francisco",
}
```

FastAPI automatically serializes the JSON to produce the `ApplicationCreate` object, thus providing useful functionality of accessing the object directly, rather than working with raw dict data
```python
application.company
application.role
```

## Using all three at once?

Yes, we can use all of these at once. FastAPI does the job of figuring out which is which. Consider the URL `PUT /applications/42?notify=true` with the request body of
```json
{
  "company": "Anthropic",
  "role": "Software Engineer"
}
```

We can handle it all together via one function
```python
@app.put("/applications/{application_id}")
def update_application(
    application_id: int,
    application: ApplicationCreate,
    notify: bool = False
):
    pass
```
