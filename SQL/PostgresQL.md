## Setting up DB

1. Install via Homebrew
```bash
brew install postgres@18
```

2. Connect to the CLI
```bash
psql postgres
```


## Creating/Connecting to a DB
Once you see the prompt `postgres=# ` on the screen, it means that postgres is ready to run. We then move on to create a database
```sql
CREATE DATABASE applyflow
```

You can view all databases with the psql command - `\l`

## Creating a table
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

