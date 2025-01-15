# SQLAlchemy Deep Dive

## 1. Engine Creation and Configuration
```python
from sqlalchemy import create_engine
engine = create_engine("sqlite:///todooo.db")
```
- `create_engine`: Creates database engine instance
- `"sqlite:///todooo.db"`: URL format for SQLite
  - `sqlite:///` indicates SQLite database
  - `todooo.db` is the database file name
  - For other databases, URLs would be like: `postgresql://user:password@localhost/dbname`

## 2. Declarative Base
```python
from sqlalchemy.ext.declarative import declarative_base
Base = declarative_base()
```
- `declarative_base()`: Creates base class for all ORM models
- All model classes inherit from this `Base`
- Manages metadata about tables, columns, and relationships
- Provides mapping between Python classes and database tables

## 3. Model Definition
```python
class ToDo(Base):
    __tablename__ = 'todos'  # Note: Should use __tablename__ not tablename
    
    id = Column(Integer, primary_key=True)
    task = Column(String(256))
```
### Column Types and Parameters:
- `Integer`: Whole number values
- `String(256)`: Variable-length string with max 256 characters
- Common Parameters:
  - `primary_key=True`: Marks column as primary key
  - `nullable=False`: Column cannot contain NULL
  - `unique=True`: Values must be unique
  - `index=True`: Creates database index
  - `default=value`: Default value if none provided

## 4. Session Management

### Creating Session Factory
```python
from sqlalchemy.orm import sessionmaker
SessionLocal = sessionmaker(bind=engine, expire_on_commit=False)
```
- `sessionmaker`: Creates session factory
- `bind=engine`: Associates session with database engine
- `expire_on_commit=False`: Keeps objects usable after commit

### Session Operations
```python
# Create session
session = SessionLocal()

try:
    # Use session for database operations
    pass
finally:
    # Always close session
    session.close()
```

## 5. Common Session Methods

### Creating Records
```python
# Create new todo
new_todo = ToDo(task="Learn SQLAlchemy")
session.add(new_todo)         # Stage for commit
session.commit()              # Save to database
```

### Querying Records
```python
# Basic queries
todo = session.query(ToDo).get(1)              # Get by primary key
todos = session.query(ToDo).all()              # Get all records
todo = session.query(ToDo).first()             # Get first record

# Filtered queries
todos = session.query(ToDo).filter(ToDo.task == "Learn SQLAlchemy").all()
todo = session.query(ToDo).filter_by(task="Learn SQLAlchemy").first()
```

### Updating Records
```python
todo = session.query(ToDo).get(1)
todo.task = "Updated task"
session.commit()
```

### Deleting Records
```python
todo = session.query(ToDo).get(1)
session.delete(todo)
session.commit()
```

## 6. Query Methods Explained

### Query Building Methods
- `query(Model)`: Starts a query for specified model
- `filter()`: Adds WHERE clause with complex conditions
- `filter_by()`: Adds WHERE clause with simple equality
- `order_by()`: Sorts results
- `limit()`: Limits number of results
- `offset()`: Skips specified number of results

### Query Execution Methods
- `all()`: Returns list of all results
- `first()`: Returns first result or None
- `one()`: Returns single result (errors if 0 or >1 results)
- `get(pk)`: Returns item by primary key
- `count()`: Returns count of results

## 7. Session States and Objects

### Object States
1. **Transient**: Object exists but isn't in session
2. **Pending**: Object added to session but not committed
3. **Persistent**: Object in session and committed
4. **Detached**: Object was in session but removed

### Session Management Best Practices
```python
def get_todo(todo_id: int):
    session = SessionLocal()
    try:
        todo = session.query(ToDo).get(todo_id)
        return todo
    finally:
        session.close()
```
- Always use try/finally or context managers
- Close sessions after use
- Don't share sessions across requests
- Commit or rollback before closing

## 8. Database Creation
```python
# Create all tables
Base.metadata.create_all(bind=engine)
```
- Creates tables for all models that inherit from Base
- Safe to run multiple times (won't recreate existing tables)
- Must be run after defining all models

This comprehensive guide covers the core SQLAlchemy components used in your Todo application. Would you like me to elaborate on any specific part?
