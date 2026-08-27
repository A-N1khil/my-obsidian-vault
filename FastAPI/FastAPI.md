## Phase 1 — FastAPI Fundamentals & Pydantic

- [x] FastAPI Setup and Uvicorn
- [x] HTTP Methods and Route Operations
- [ ] Path and Query Parameters
- [ ] Request Bodies
- [ ] HTTP Status Codes and Error Handling
- [ ] Swagger and OpenAPI Documentation
- [ ] Pydantic Models and Field Validation
- [ ] Request and Response Models
- [ ] Nested Models and Serialization
- [ ] In-Memory CRUD API

## Phase 2 — FastAPI Architecture & Project Structure

- [ ] APIRouter and Route Organization
- [ ] Dependency Injection with Depends
- [ ] Service Layer and Business Logic
- [ ] Project Structure and Module Organization
- [ ] Configuration and Environment Variables

## Phase 3 — SQLAlchemy & Database Integration

- [ ] SQLAlchemy ORM Fundamentals
- [ ] Database Engine and Sessions
- [ ] SQLAlchemy Models and Columns
- [ ] Database CRUD Operations
- [ ] Pydantic and SQLAlchemy Integration
- [ ] Foreign Keys and Relationships
- [ ] Alembic and Database Migrations

## Phase 4 — Async FastAPI

- [ ] Python Async and Event Loop Fundamentals
- [ ] Async FastAPI and Database Operations

## Phase 5 — Testing with Pytest

- [ ] Pytest and FastAPI TestClient
- [ ] Endpoint and Validation Testing
- [ ] Dependency Mocking and Overrides
- [ ] Database and Service Testing
- [ ] Final FastAPI Project


## Setup

```bash
pip install "fastapi[all]"
```

## Creating the server

```python
from fastapi import FastAPI
app = FastAPI()
```

## Next...

1. [[Routing and Endpoints]]