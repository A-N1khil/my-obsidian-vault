## Foreign Keys

**Foreign Keys** create a database constraint. In a SQLAlchemy model, a foreign key is marked.

```mermaid
---
title: DB Setup
---
erDiagram
    USERS ||--o{ APPLICATIONS : has

    USERS {
        UUID id PK
        email varchar
    }

    APPLICATIONS {
        UUID id PK
        UUID user_id FK
    }
```

```python
# models/user_model.py

class User(Base):
    __tablename__ = "users"

    id: Mapped[UUID] = mapped_column(
        UUID(as_uuid=True),
        primary_key=True
    )

    email: Mapped[str] = mapped_column(
        String(255),
        unique=True,
        nullable=False
    )

# models/application.py

class Application(Base):
    __tablename__ = "applications"

    id: Mapped[UUID] = mapped_column(
        UUID(as_uuid=True),
        primary_key=True
    )

    user_id: Mapped[UUID] = mapped_column(
        ForeignKey("users.id"),
        nullable=False
    )
```

The database now guarantees that an `applications.user_id` must reference an existing user.
> [!NOTE] Do not exchange...
> When using `ForeignKey`, we need to specify the `<table_name>.<column_name>` as it would be present in the DB, and not the Python class and attribute name

## `relationships()`

A `relationship()` in SQLAlchemy is just an ORM convenience that lets you access the related models without having to run additional queries. Looking at our `Application` model above, in order to fetch the user that has submitted the application, we'd have to run another query to fetch the records
But, with...
```python
class Application(Base):
    __tablename__ = "applications"

    id: Mapped[UUID] = mapped_column(primary_key=True)

    user_id: Mapped[UUID] = mapped_column(
        ForeignKey("users.id")
    )

    user: Mapped["User"] = relationship()

# services/some_service.py
print(application.user.email) # returns user.email
```

### Bi-directional relationships

From the previous example, we can now access the `User` object from the `Application`, but what if we need to extract the `Application` model from the `User`? Suppose we need to fetch all applications done by a particular user

```python
# models/user_model.py
class User(Base):
    __tablename__ = "users"

    id: Mapped[UUID] = mapped_column(primary_key=True)

    applications: Mapped[list["Application"]] = relationship(
        back_populates="user"
    )

# models/application_model.py
class Application(Base):
    __tablename__ = "applications"

    id: Mapped[UUID] = mapped_column(primary_key=True)

    user_id: Mapped[UUID] = mapped_column(
        ForeignKey("users.id")
    )

    user: Mapped["User"] = relationship(
        back_populates="applications"
    )
```

> [!NOTE] A `ForeignKey` is required to establish relationships
> SQLAlchemy needs an attribute marked as `ForeignKey` to establish a relationship. If no FKs are found, then SQLAlchemy treats both as mutually exclusive tables


