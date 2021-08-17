<p align="center"><img src="docs/orion_logo.jpg" width=500></p>

# Orion

A development repo for Prefect Orion

## Installation

```bash
$ git clone https://github.com/PrefectHQ/orion.git
$ pip install -e "./orion[dev]"
```

## Running the Orion server

### Step 1: Configure the database connection

Orion server works against SQLite and Postgresql. To specify which database to connect to, set the `PREFECT_ORION_DATABASE_CONNECTION_URL` environment variable.

To connect to a SQLite database (easiest/recommended option):

```bash
export PREFECT_ORION_DATABASE_CONNECTION_URL=sqlite+aiosqlite:////tmp/orion.db
```


To connect to a Postgres database, the connection string should look something like this:

```bash
export PREFECT_ORION_DATABASE_CONNECTION_URL=postgresql+asyncpg://<username>:<password>@<hostname>/<dbname>'
```
### Step 2: Ensuring database is up to date

For the time being, there is no framework for migrations. "Migrating" the database consists of creating the correct tables.

To migrate the database, run the following two lines in a python repl

```python
import prefect, asyncio
asyncio.run(prefect.orion.utilities.database.reset_db())
```

Note that for SQLite in-memory databases, models are created automatically.

### Step 3: Running the server

Use the following command to run an Orion server locally:

```bash
uvicorn prefect.orion.api.server:app --reload
```

The `--reload` flag will automatically reload when changes are made the source files.

### Step 4: Interacting with the Orion REST API

The Orion server features interactive documentation, which will **actually make requests against the server and trigger database changes**.

After starting the server, navigate to `localhost:8000/docs`. On this page, you'll find documentation of requests and responses for all endpoints. The docs also pre-populate example data you can use to test out requests.
