### 1. System Variables
```python
import os

database_url = os.getenv("DATABASE_URL")
```

### 2. `.env` file

During local development, it's inconvenient to export everything manually, so you typically create
```bash
APP_NAME=ApplyFlow
DEBUG=true
DATABASE_URL=postgresql://localhost:5432/applyflow
JWT_SECRET=some-local-secret
```

`pydantic-settings` is a framework that reads the local `.env` file and extracts the variable information from it. `pip install pydantic-settings`

```python nums {9-11}
from pydantic_settings import BaseSettings, SettingsConfigDict

class Settings(BaseSettings):
    app_name: str
    debug: bool
    database_url: str
    jwt_secret: str

    model_config = SettingsConfigDict(
        env_file=".env"
    )
```

