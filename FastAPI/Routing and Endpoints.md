## Routers

### Creating endpoints
```python
@app.get("/")
def root():
    return {"message": "Welcome to ApplyFlow"}


@app.get("/healthCheck")
def health_check():
    return {"status": "healthy"}
```

### Creating routers in different classes

Assuming we have a Python file, `routers/application.py`, we can initialize a router there as
```python
# routers/application.py

from fastapi import APIRouter

router = APIRouter(prefix="/applications", tags=["applications"])
```

Then, use the `router` variable to create endpoints specific to `/applications`
```python
# routers/application.py

@router.get("/all", status_code=status.HTTP_200_OK)
def get_applications():
    return list(applications.values())


@router.post("/add", status_code=status.HTTP_201_CREATED)
def add_application(application: ApplicationCreate):
    return create_application(application)
```

In order to have this router recognized by the server, we need to link it in `main.py`
```python
# main.py

app.include_router(application.router)
```

## Using Endpoints

### `get`

```python
@router.get("/all", status_code=status.HTTP_200_OK)
def get_applications():
    return list(applications.values())
```

### `post`
```python
@router.post("/bulk", status_code=status.HTTP_201_CREATED)
def add_applications(new_applications: list[ApplicationCreate]):
    return [create_application(application) for application in new_applications]
```



## Next ...

[[Path and Query Parameters]]
[[HTTP Status Codes and Error Handling]]
[[Response Models]]