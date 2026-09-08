> [!INFO]
> The model you accept from the client does not have to be the model you send back

Response Models are data objects that the server sends back to the client. Assuming we have a request for an application coming in
```python
class ApplicationCreate(BaseModel):
    id: UUID
    company: str
    position: str
    location: str | None
```

This request goes through the create endpoint and is successfully created. Now, assuming the server needs to send back the status of creation as well as the created time. But our model does not have those fields.
Hence, we can define a response model as such
```python
class ApplicationResponse(BaseModel):
    id: UUID
    company: str
    position: str
    location: str | None
    # Additional Fields to be added with the response
    status: str
    created_at: datetime
```

### One Model Architecture
Instead of repeating the fields as we did above, we can instead create a subclass as the response model
```python
class Application(BaseModel):
    id: UUID
    company: str
    position: str
    location: str | None

class ApplicationResponse(Application):
    status: str
    created_at: datetime
```

## Validation through Response Models
Response Models actually validate your outputs.
```python nums {13-15}
# models/application.py
class ApplicationResponse(BaseModel):
    id: UUID
    company: str
    position: str

# routers/application.py
@app.get(
    "/applications/{application_id}",
    response_model=ApplicationResponse
)
def get_application(application_id: UUID):
    return {
        "id": application_id,
        "company": "Google"
    }
```

The corresponding output is missing the field `position` from the `ApplicationResponse` model; hence, FastAPI throws out a `ResponseValidationError`

## Response Models help with security
Assuming we run a DB query for a particular application and get the following response
```json nums
{
    "id": "...",
    "company": "Google",
    "position": "Software Engineer",
    "user_id": "...",
    "internal_notes": "Recruiter contacted manually",
    "created_at": "..."
}
```

Going with the `ApplicationResponse` model defined above, FastAPI will only serialize the fields present in the response. This way
- It saves internal/private data
- Reduces computation to remove the internal/private data

## Function Return Type vs Response Model

```python nums
# routers/application.py
@app.get(
    "/applications/{application_id}",
    response_model=ApplicationResponse
)
def get_application(application_id: UUID):
    return {
        "id": application_id,
        "company": "Google"
    }


@app.get(
    "/applications/{application_id}",
)
def get_application_1(application_id: UUID) -> ApplicationResponse:
    return {
        "id": application_id,
        "company": "Google"
    }
```

Response Models can also be specified via function return types. However, it is better to add the `response_model` variable, as it makes the API contract obvious when reading routes.
