## The basic model

Modern SQLAlchemy 2.x uses `DeclarativeBase`, `Mapped`, and `mapped_column`.
```python
from sqlalchemy import String
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column


class Base(DeclarativeBase):
    pass


class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(100))
    email: Mapped[str] = mapped_column(String(255), unique=True)
```

This maps roughly to:
```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE
);
```

If you're thinking in JPA:

```java
@Entity
@Table(name = "users")
public class User {

    @Id
    private Integer id;

    private String name;
}
```


### `DeclarativeBase`

We first create:
```python
class Base(DeclarativeBase):
    pass
```

Then every ORM model inherits it:
```python
class User(Base):
    ...

class Application(Base):
    ...

class Company(Base):
    ...
```
`Base` maintains SQLAlchemy's **metadata** describing all registered tables.

That's why something like:
```python
Base.metadata.create_all(engine)
```
knows which tables exist in your application.

Later, Alembic also uses this metadata to detect schema changes.

### `__tablename__`

```python
class User(Base):
    __tablename__ = "users"
```

This specifies the actual database table.

## Understanding `Mapped`

This syntax can look strange initially:
```python
name: Mapped[str]
```

There are actually two pieces here:
```python
name: Mapped[str] = mapped_column(String(100))
      ─────┬─────   ───────────┬──────────────
           │                    │
     Python/ORM typing      DB column config
```

`Mapped[str]` tells SQLAlchemy:
> `name` is an ORM-mapped attribute whose Python value is a string.
`mapped_column(String(100))` provides database-specific configuration.

This distinction becomes clearer here:
```python
age: Mapped[int]
```

SQLAlchemy can infer the database type:

```text
Python       SQL
-------------------------
str       → VARCHAR
int       → INTEGER
float     → FLOAT
bool      → BOOLEAN
datetime  → DATETIME
```

Therefore, this is valid:
```python
age: Mapped[int] = mapped_column()
```
And even:
```python
age: Mapped[int]
```

SQLAlchemy can infer quite a bit from the type annotation.

## Primary Keys

Simple integer ID:
```python
id: Mapped[int] = mapped_column(primary_key=True)
```
For PostgreSQL, SQLAlchemy/database machinery can generate the value automatically.

You don't normally do:
```python
user = User(
    id=123,
    name="Nikhil"
)
```

You do:
```python
user = User(name="Nikhil")

db.add(user)
db.flush()

print(user.id)
```

---

### UUID primary keys

```python
import uuid

from sqlalchemy import Uuid


id: Mapped[uuid.UUID] = mapped_column(
    Uuid,
    primary_key=True,
    default=uuid.uuid4,
)
```

Then:
```python
user = User(name="Nikhil")
```
gets something like:
```text
550e8400-e29b-41d4-a716-446655440000
```

Since PostgreSQL supports UUIDs natively, you can also use PostgreSQL-specific UUID functionality when appropriate.

## Nullability

This is one of the nice parts of SQLAlchemy 2's typing.

```python
name: Mapped[str]
```

means non-nullable by default:

```sql
name VARCHAR NOT NULL
```

But:

```python
bio: Mapped[str | None]
```

means:

```sql
bio VARCHAR NULL
```

So Python typing and database constraints align nicely.

Example:
```python
class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(primary_key=True)

    name: Mapped[str] = mapped_column(String(100))

    bio: Mapped[str | None] = mapped_column(String(500))
```

You _can_ explicitly write:
```python
mapped_column(nullable=False)
```
but when `Mapped[...]` already expresses it correctly, that's often redundant.



## `unique`

For an email:

```python
email: Mapped[str] = mapped_column(
    String(255),
    unique=True,
)
```

Database:

```sql
UNIQUE(email)
```

This is a **database constraint**, not merely Python validation.

So two simultaneous requests attempting to insert the same email are ultimately protected by PostgreSQL.

Your service can still check:

```python
if user_exists(email):
    raise ...
```

for a nicer error, but the DB constraint is the authoritative protection.

---

## Indexes

```python
email: Mapped[str] = mapped_column(
    String(255),
    unique=True,
    index=True,
)
```

SQLAlchemy/Alembic can create an index for the column.

Indexes are useful for columns frequently used in:

```sql
WHERE
JOIN
ORDER BY
```

But don't just put:

```python
index=True
```

on everything. Indexes improve reads at the cost of storage and additional work during writes.

---

## Default values

Suppose an application starts as:

```text
APPLIED
```

You could write:

```python
status: Mapped[str] = mapped_column(
    String(30),
    default="APPLIED",
)
```

There's an important distinction we'll encounter repeatedly:

### Python/SQLAlchemy-side default

```python
default="APPLIED"
```

versus a **database-side default**:

```python
server_default="APPLIED"
```

Conceptually:

```text
default
   ↓
SQLAlchemy supplies value
   ↓
INSERT status='APPLIED'
```

versus:

```text
server_default
   ↓
PostgreSQL itself supplies value
```

Neither is universally better. It depends on who should own the default.

---

## Timestamps

Very common model fields:

```python
from datetime import datetime

from sqlalchemy import DateTime, func


created_at: Mapped[datetime] = mapped_column(
    DateTime(timezone=True),
    server_default=func.now(),
)
```

PostgreSQL generates the timestamp.

For an update timestamp:

```python
updated_at: Mapped[datetime] = mapped_column(
    DateTime(timezone=True),
    server_default=func.now(),
    onupdate=func.now(),
)
```

We'll eventually extract these kinds of repeated fields into reusable base/mixin classes.

---

## Enums

Suppose ApplyFlow has:

```text
WISHLIST
APPLIED
INTERVIEW
OFFER
REJECTED
```

Don't scatter magic strings everywhere.

```python
from enum import Enum


class ApplicationStatus(str, Enum):
    WISHLIST = "wishlist"
    APPLIED = "applied"
    INTERVIEW = "interview"
    OFFER = "offer"
    REJECTED = "rejected"
```

Then:

```python
from sqlalchemy import Enum as SQLEnum


status: Mapped[ApplicationStatus] = mapped_column(
    SQLEnum(ApplicationStatus),
    default=ApplicationStatus.WISHLIST,
)
```

Now your Python code becomes:

```python
application.status = ApplicationStatus.INTERVIEW
```

instead of:

```python
application.status = "interveiw"  # 💀 typo reaches production
```

---

## A more realistic User model

Putting several concepts together:

```python
import uuid
from datetime import datetime

from sqlalchemy import DateTime, String, Uuid, func
from sqlalchemy.orm import Mapped, mapped_column

from app.db.base import Base


class User(Base):
    __tablename__ = "users"

    id: Mapped[uuid.UUID] = mapped_column(
        Uuid,
        primary_key=True,
        default=uuid.uuid4,
    )

    name: Mapped[str] = mapped_column(
        String(100)
    )

    email: Mapped[str] = mapped_column(
        String(255),
        unique=True,
        index=True,
    )

    created_at: Mapped[datetime] = mapped_column(
        DateTime(timezone=True),
        server_default=func.now(),
    )
```

You can instantiate it normally:

```python
user = User(
    name="Nikhil",
    email="nikhil@example.com",
)
```

SQLAlchemy handles the mapped fields.

---

### One important separation

Don't confuse:

```python
class User(Base):
```

with:

```python
class UserCreate(BaseModel):
```

They may look similar, but they solve completely different problems.

```text
POST /users
     │
     ▼
UserCreate                 ← Pydantic
API validation
     │
     ▼
UserService
     │
     ▼
User                       ← SQLAlchemy
Database representation
     │
     ▼
PostgreSQL
```

So eventually we might have:

```text
app/
├── models/
│   └── user.py             # SQLAlchemy
│
└── schemas/
    └── user.py             # Pydantic
```

That separation is worth maintaining from day one.