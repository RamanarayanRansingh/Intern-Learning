# Day 2: Deep Dive into FastAPI and SQLAlchemy

## Core Concepts in Detail

### 1. SQLAlchemy Engine
```python
from sqlalchemy import create_engine
engine = create_engine("sqlite:///todooo.db")
```
**What is Engine?**
- It's the starting point and core of any SQLAlchemy application
- Acts as a connection factory
- Maintains a pool of database connections for reuse
- Handles the actual communication with the database

**Why is Engine Used?**
1. Connection Management:
   - Creates new connections when needed
   - Reuses existing connections when possible
   - Manages connection pooling for better performance
2. Database Dialect:
   - Handles database-specific SQL differences
   - Translates Python code into appropriate SQL for your database

### 2. Declarative Base
```python
from sqlalchemy.ext.declarative import declarative_base
Base = declarative_base()
```
**What is Declarative Base?**
- It's a factory function that creates a base class for all your ORM models
- Provides a system for mapping Python classes to database tables

**Why is Declarative Base Used?**
1. Class to Table Mapping:
   - Allows you to define tables as Python classes
   - Automatically creates table schemas from your class definitions
2. Inheritance Support:
   - All your model classes inherit from this base
   - Provides common functionality to all models
3. Metadata Management:
   - Keeps track of all tables and their relationships
   - Handles table creation and schema management

### 3. Session Management
```python
from sqlalchemy.orm import Session
session = Session(bind=engine, expire_on_commit=False)
```
**What is a Session?**
- Acts as a "holding zone" for all objects you want to track
- Represents an ongoing conversation with the database
- Provides the interface for persisting and loading data

**Why is Session Used?**
1. Unit of Work Pattern:
   - Tracks changes to objects
   - Maintains a cache of objects loaded
   - Coordinates writing of changes to the database
2. Transaction Management:
   - Groups multiple operations into transactions
   - Ensures database consistency
   - Handles commit and rollback operations
3. Object State Management:
   - Keeps track of object states (new, modified, deleted)
   - Synchronizes objects with database
   - Manages object lifecycle

**Session States Example:**
```python
# Creating a new todo
new_todo = ToDo(task="Learn SQLAlchemy")     # Object is in transient state
session.add(new_todo)                        # Object moves to pending state
session.commit()                             # Object moves to persistent state
session.close()                              # Object moves to detached state
```

## Practical Implementation Examples

### 1. Creating and Managing Database Tables
```python
class ToDo(Base):
    __tablename__ = 'todos'
    id = Column(Integer, primary_key=True)
    task = Column(String(256))

# Create all tables
Base.metadata.create_all(bind=engine)
```
**What's Happening Here?**
1. `__tablename__` defines the actual table name in database
2. `Column` defines table columns with their types and constraints
3. `create_all()` creates tables based on all model definitions

### 2. CRUD Operations with Session

#### Create Operation
```python
@app.post("/todo")
async def create_todo(todo: ToDoRequest):
    session = Session(bind=engine, expire_on_commit=False)
    try:
        tododb = ToDo(task=todo.task)
        session.add(tododb)        # Stage changes
        session.commit()           # Persist changes
        return tododb.id
    finally:
        session.close()           # Always close session
```
**Key Points:**
- Session tracks the new object
- `commit()` writes changes to database
- `finally` ensures session is always closed

#### Read Operation
```python
@app.get("/todo/{id}")
async def read_todo(id: int):
    session = Session(bind=engine, expire_on_commit=False)
    try:
        todo = session.query(ToDo).get(id)  # Query specific record
        if not todo:
            raise HTTPException(status_code=404)
        return todo
    finally:
        session.close()
```
**Key Points:**
- `query()` creates a query object
- `get()` retrieves by primary key
- Error handling for non-existent records

## Important Session Management Patterns

### 1. Session Lifecycle
```python
# Correct pattern
session = Session(bind=engine, expire_on_commit=False)
try:
    # Do database operations
    session.commit()
finally:
    session.close()
```

### 2. Common Session Operations
```python
# Query all records
all_todos = session.query(ToDo).all()

# Filter records
specific_todos = session.query(ToDo).filter(ToDo.task.like('%learn%')).all()

# Update record
todo = session.query(ToDo).get(1)
todo.task = "Updated task"
session.commit()

# Delete record
session.delete(todo)
session.commit()
```

## Challenges and Solutions

### 1. Session Management Challenges
- Problem: Sessions not being closed properly
- Solution: Always use try-finally blocks
- Impact: Prevents resource leaks and connection pool exhaustion

### 2. Database Connection Issues
- Problem: Multiple threads trying to access SQLite
- Solution: Use `connect_args={"check_same_thread": False}`
- Impact: Allows FastAPI to handle multiple requests

### 3. State Management
- Problem: Accessing objects after session close
- Solution: Use `expire_on_commit=False`
- Impact: Keeps objects usable after commit

## Best Practices Learned

1. **Session Management**
   - Always close sessions
   - Use context managers or try-finally blocks
   - Don't share sessions across requests

2. **Error Handling**
   - Handle database exceptions
   - Provide meaningful error messages
   - Use appropriate HTTP status codes

3. **Data Validation**
   - Use Pydantic models for input validation
   - Validate data before database operations
   - Handle edge cases appropriately

## Testing and Verification
- Used FastAPI's Swagger UI (`/docs`) for testing endpoints
- Verified CRUD operations in SQLite database
- Tested error handling scenarios
- Checked session management patterns

## Next Steps for Learning
1. Study database migrations with Alembic
2. Learn about relationships in SQLAlchemy
3. Implement authentication and authorization
4. Study database indexing and optimization
5. Learn about database transactions and isolation levels

Would you like me to expand on any of these concepts further?
