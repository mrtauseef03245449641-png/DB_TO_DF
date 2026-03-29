# Database-to-DataFrame Pipeline

A production-ready data ingestion pipeline that connects PostgreSQL with Pandas, demonstrating three connection approaches and all Pandas database functions.

## Overview

This project demonstrates how to:
- Load data from CSV files into PostgreSQL database
- Read data from PostgreSQL into Pandas DataFrames
- Use three different database connectors (psycopg2, psycopg3, ADBC)
- Utilize all Pandas database functions

## Prerequisites

1. **PostgreSQL** installed with pgAdmin (or any PostgreSQL client)
2. **Python 3.11+**
3. **Jupyter Notebook**

## Installation

### 1. Create Virtual Environment

```bash
python -m venv .venv
```

### 2. Activate Virtual Environment

```bash
# Windows
.venv\Scripts\activate

# Linux/Mac
source .venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install pandas sqlalchemy psycopg2-binary psycopg[binary] pyarrow python-dotenv ipykernel
```

Or use `uv`:
```bash
uv pip install pandas sqlalchemy psycopg2-binary psycopg[binary] pyarrow python-dotenv ipykernel
```

### 4. Configure PostgreSQL

- Open pgAdmin or psql
- Default connection: `localhost:5432`
- Default user: `postgres`
- Default password: `****`(or your password)

## Step-by-Step Project Guide

### Step 1: Set Up Project Structure

```
DB_TO_DF/
├── .venv/                 # Virtual environment
├── notebook.ipynb         # Main project code
├── pyproject.toml         # Project dependencies
├── uv.lock               # Lock file
└── README.md             # This file
```

### Step 2: Configure Database Connection

In the notebook, update the `DB_CONFIG` dictionary:

```python
DB_CONFIG = {
    "host": "localhost",
    "port": "5432",
    "database": "olist_db",
    "user": "postgres",
    "password": "****"  # Your PostgreSQL password
}
```

### Step 3: Prepare CSV Data

Place your CSV files in a folder (e.g., `C:/Users/Info/Downloads/archive/`):

- olist_customers_dataset.csv
- olist_orders_dataset.csv
- olist_order_items_dataset.csv
- olist_order_payments_dataset.csv
- olist_order_reviews_dataset.csv
- olist_products_dataset.csv
- olist_sellers_dataset.csv
- olist_geolocation_dataset.csv
- product_category_name_translation.csv

### Step 4: Run the Notebook

```bash
jupyter notebook notebook.ipynb
```

Execute cells in order:

1. **Cell 1-2**: Import libraries and configure settings
2. **Cell 3**: Set up database configuration
3. **Cell 4**: Ensure database exists (creates `olist_db` if not exists)
4. **Cell 5**: Load all CSV files into DataFrames
5. **Cell 6**: Import data to PostgreSQL using psycopg2
6. **Cell 7-8**: Demonstrate all Pandas database functions:
   - `read_sql` - Generic SQL read
   - `read_sql_query` - Read SQL query
   - `read_sql_table` - Read entire table
   - `to_sql` - Write DataFrame to database
   - Chunked reading for large datasets
   - Dtype mapping
   - Parameterized queries
   - Date parsing
7. **Cell 9**: Test psycopg3 (modern approach)
8. **Cell 10**: Test ADBC (Apache Arrow - 5-10x faster)
9. **Cell 11**: Performance comparison
10. **Cell 12-14**: Example queries (joins, aggregations, date filters)

## Database Connection Approaches

### 1. Legacy: psycopg2 + SQLAlchemy

```python
from sqlalchemy import create_engine

engine = create_engine(
    "postgresql+psycopg2://user:password@localhost:5432/database"
)
```

### 2. Modern: psycopg3 + SQLAlchemy

```python
engine = create_engine(
    "postgresql+psycopg://user:password@localhost:5432/database"
)
```

### 3. Cutting-edge: ADBC (Apache Arrow)

```python
engine = create_engine(
    "postgresql+adbc://user:password@localhost:5432/database"
)
```

ADBC provides 5-10x faster data transfer using Apache Arrow format.

## Pandas Database Functions

### Reading Data

```python
# Method 1: read_sql (generic - works with query or table)
df = pd.read_sql("SELECT * FROM customers", engine)

# Method 2: read_sql_query (for SQL queries)
df = pd.read_sql_query("SELECT * FROM customers", engine)

# Method 3: read_sql_table (for entire table)
df = pd.read_sql_table("customers", engine)

# Chunked reading for large datasets
for chunk in pd.read_sql("SELECT * FROM large_table", engine, chunksize=10000):
    process(chunk)

# With dtype mapping
df = pd.read_sql("SELECT * FROM customers", engine, dtype={'id': 'string'})

# With date parsing
df = pd.read_sql("SELECT * FROM orders", engine, parse_dates=['order_date'])
```

### Writing Data

```python
# Write DataFrame to SQL
df.to_sql("table_name", engine, if_exists="replace", index=False)

# With dtype mapping
df.to_sql("table_name", engine, dtype={'column': sqlalchemy.String(50)})
```

### Parameterized Queries (SQL Injection Prevention)

```python
query = "SELECT * FROM table WHERE id = :id"
df = pd.read_sql(text(query), engine, params={'id': 1})
```

## Project Structure

| File | Description |
|------|-------------|
| `notebook.ipynb` | Main project with all code |
| `pyproject.toml` | Dependencies configuration |
| `README.md` | This guide |

## Troubleshooting

### PostgreSQL Connection Error

```
FATAL: database "olist_db" does not exist
```

The notebook automatically creates the database. If issues persist, create manually:

```sql
CREATE DATABASE olist_db;
```

### ADBC Not Available

```
Can't load plugin: sqlalchemy.dialects:postgresql.adbc
```

Install ADBC driver:
```bash
uv pip install adbc-driver-postgresql
```

### Port Already in Use

Check PostgreSQL is running on port 5432:
```bash
# Windows
netstat -ano | findstr 5432
```

## Expected Output

After running the notebook:
- All CSV files imported to PostgreSQL
- 99,441+ customer records loaded
- Sample queries executed successfully
- Performance comparison completed

## Summary

This project demonstrates:
- Three PostgreSQL connection methods (psycopg2, psycopg3, ADBC)
- All Pandas database read/write functions
- Production-ready data ingestion pipeline
- SQL queries, aggregations, and joins
- Parameterized queries for security
- Chunked reading for large datasets
- Date parsing and dtype mapping