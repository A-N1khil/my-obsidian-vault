## Engine
The `Engine` is SQLAlchemy's main interface to the database.

```python
from sqlalchemy import create_engine

engine = create_engine(
    "postgresql+psycopg://postgres:password@localhost:5432/applyflow"
)
```

Break down that URL:

```
postgresql+psycopg://postgres:password@localhost:5432/applyflow
│          │          │        │              │        │
dialect    driver     user    password        host     database
```

SQLAlchemy supports different databases through **dialects**:

```
postgresql
mysql
sqlite
oracle
```

and drivers handle the actual communication:
```
PostgreSQL
    ↓
SQLAlchemy
    ↓
psycopg
    ↓
PostgreSQL server
```

### Does Engine = database connection?
No. This is an important distinction.
```python
engine = create_engine(...)
```
doesn't mean "Open one PostgreSQL connection and keep it forever." The Engine manages a **connection pool**.

Conceptually:
```mermaid
---
config:
 look: handDrawn
---
flowchart TB
	A[Engine] --> B[Pool]
	B --> C[Conn1] & D[Conn2] & E[Conn3]
	C & D & E --> F[PostgreSQL]
```

When SQLAlchemy needs to talk to the database, it checks out a connection from this pool. So in a FastAPI application, you generally create **one Engine for the application**.
```python
# database.py

engine = create_engine(DATABASE_URL)
```

Don't do this inside endpoints; otherwise you'd keep creating new engines and, consequently, new connection pools.
```python
@app.get("/users")
def get_users():
    engine = create_engine(DATABASE_URL)  # ❌
```

## Sessions
The Session sits **above the Engine**.
```
Session
   ↓
Engine
   ↓
Connection Pool
   ↓
Database
```

The Engine deals with database connectivity. The Session deals with your **ORM work**.

For example:
```python
session.add(user)
session.get(User, user_id)
session.delete(user)
session.commit()
```

The Session tracks ORM objects and coordinates the SQL necessary to persist their state.

### Creating Sessions

The simplest version is:
```python
from sqlalchemy.orm import Session
with Session(engine) as session:
    ...
```

But applications normally configure a **session factory**:
```python
from sqlalchemy.orm import sessionmaker

SessionLocal = sessionmaker(
    bind=engine
)
```

Now `session = SessionLocal()` creates a new Session configured to use our Engine.

Notice the distinction:
```
engine
   │
   └── one application-wide object

SessionLocal
   │
   └── factory for creating Sessions

Session
   │
   └── short-lived unit of work
```


### `sessionmaker` is a factory

This trips people up initially.
```python
SessionLocal = sessionmaker(bind=engine)
```
`SessionLocal` is **not the Session itself**.

Think:
```python
SessionLocal()
SessionLocal()
SessionLocal()
```
Each call creates a separate Session.

### Why one Session per request?

Imagine `POST /applications`. Your request might perform several operations:
```python
create_application()
create_status_history()
update_user_statistics()
```

Ideally, these belong to one database transaction:
```
HTTP Request
    │
    ▼
 Session
    │
    ├── INSERT application
    ├── INSERT status_history
    └── UPDATE statistics
    │
    ▼
 COMMIT
```

If something fails, we roll back, then the request finishes and the Session is closed. This gives you a very natural transaction boundary:

```
Request begins
      ↓
Session created
      ↓
Service / DB operations
      ↓
Commit / Rollback
      ↓
Session closed
      ↓
Request ends
```

## FastAPI + Session
```python
# database.py

from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

engine = create_engine(DATABASE_URL)

SessionLocal = sessionmaker(bind=engine)
```

Then we create the DB dependency
```python
from collections.abc import Generator
from sqlalchemy.orm import Session

def get_db() -> Generator[Session, None, None]:
    db = SessionLocal()

    try:
        yield db
    finally:
        db.close()
```

And, finally, at the router level
```python
@router.get("/applications")
def get_applications(
    db: Session = Depends(get_db)
):
    ...
```

### `yeild`

DB connections need to be closed after every operation. Note that, we are not closing the connection to the server, but returning the current connection back to the pool so that other services/requests can utilize it. `yeild` closes this connection when the operation is done - either a success or a failure (Check [[Dependency Injection#Dependencies with `yield`|Dependencies with `yield`]])

```mermaid
flowchart TB
	subgraph S1[Connection Pool]
		P[Pool] --> C1 & C2 & C3
	end
	A[Service] --"1 ask for connection"-->S1
	S1 --> C1 --"2 return connection"--> A
	A --> B[Task] --> C{Success?}
	C --"Yes"--> D[Success response]
	C --"No"--> E[Failure response]
	D & E --> F[yeild] --> A
	A --"3 return connection" --> S1
```

## All together..

```python nums {9,11-15,18-19,22-24}
from collections.abc import Generator

from sqlalchemy import create_engine
from sqlalchemy.orm import DeclarativeBase, Session, sessionmaker

from server.core.config import settings

# Create the engine and sessionmaker for the database connection
engine = create_engine(settings.database_url)

SessionLocal = sessionmaker(
    bind=engine,
    autoflush=False,
    expire_on_commit=False,
)


class Base(DeclarativeBase):
    pass


def get_db() -> Generator[Session, None, None]:
    with SessionLocal() as database_session:
        yield database_session

```

- Line 9 - creates the engine, which manages the connection to the PostgreSQL DB
- Lines 11-15 - create the session
	- `bind = engine` - uses the created engine for all the sessions created with this factory.
	- `autoflush = False` - stops Alchemy from flushing after every query
	- `expire_on_commit = False` - loaded objects remain readable after committing
- Line 18 - Every database model will inherit from `Base`
- Line 22 - `get_db` - the dependency