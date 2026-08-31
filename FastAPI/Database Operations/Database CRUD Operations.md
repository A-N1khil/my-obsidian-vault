```python
class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(100))
    email: Mapped[str] = mapped_column(String(255), unique=True)
```

## Creation
```python
user = User(
    name="Nikhil",
    email="nikhil@example.com"
)

db.add(user)
db.commit()
db.refresh(user)
```

`db.add(...)` does not add the user directly into the database. It keeps the entry in the current [[Sessions and Transactions|transaction]]. `db.commit()` then adds all the operations held in the current transaction to the database.

Instead of `commit`, one can also use `flush` to push the changes to the database and still keep the transaction running.

## Fetching from DB

### Fetching a single row via PK
```python
user = db.get(User, 1)
```

### Fetching via `select()`
```python
from sqlalchemy import select

stmt = select(User).where(User.email == "nikhil@example.com")

user = db.scalar(stmt)
```

The function `scalar()` is used when we are fetching only one row from the database.

### Fetching multiple rows

Scalars are used to return multiple rows from the database
```python
stmt = (
    select(User)
    .where(User.name == "Nikhil")
)

users = db.scalars(stmt).all()
```

We can also combine multiple conditions when fetching rows
```python
stmt = (
    select(User)
    .where(
        User.name == "Nikhil",
        User.email.endswith("@example.com"),
    )
)
```


## Updating entries

Entries in SQLAlchemy are updated on an object basis. SQLAlchemy detects changes in the object and generates the update statement

```python nums {6}
user = db.get(User, 1)
print(user.name) # "John Doe"

user.name = "Nikhil Anand"

db.commit()
db.refresh(user)
```

When the code reaches line 6, SQLAlchemy automatically generates the [[SQL Basics#UPDATE|UPDATE]] statement and performs the operation
```sql
UPDATE users
SET name = 'Nikhil Anand'
WHERE id = 1;
```

## Deleting entries
```python
user = db.get(User, 1)

db.delete(user)
db.commit()
```

## `flush` vs `commit` and where to use them?

- `flush` and `commit` both make changes to the database
- `flush` keeps the transaction open. It is better used inside DB services that perform the changes. Since a single service can be called multiple times during a request, it is better to keep the current transaction open
- `commit` closes the current transaction. It is better used inside business services that can call upon the DB Service multiple times to make changes and then close the transaction before returning the response or control back to the router

```python
# services/db/user_db_service.py

from sqlalchemy import select
from sqlalchemy.orm import Session

from app.models.user import User


class UserDBService:

    def __init__(self, db: Session):
        self.db = db

    def create(self, user: User) -> User:
        self.db.add(user)
        self.db.flush()
        return user

    def get_by_id(self, user_id: int) -> User | None:
        return self.db.get(User, user_id)

    def get_all(self) -> list[User]:
        stmt = select(User)
        return list(self.db.scalars(stmt).all())

    def get_by_email(self, email: str) -> User | None:
        stmt = select(User).where(User.email == email)
        return self.db.scalar(stmt)

    def delete(self, user: User) -> None:
        self.db.delete(user)
```

```python
# services/user_service.py

class UserService:

    def __init__(
        self,
        db: Session,
        user_db: UserDBService,
    ):
        self.db = db
        self.user_db = user_db

    def create_user(
        self,
        name: str,
        email: str,
    ) -> User:

        user = User(
            name=name,
            email=email,
        )

        self.user_db.create(user)

        self.db.commit()
        self.db.refresh(user)

        return user
```