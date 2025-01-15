# Day 2: FastAPI and SQLAlchemy Deep Dive

## Overview
Today focused on building RESTful APIs using FastAPI and integrating SQLAlchemy for database operations. The main project was creating a Todo API application with full CRUD functionality.

## Key Learning Points
- FastAPI route handlers and status codes
- SQLAlchemy ORM basics and database operations
- Database session management
- API error handling
- Pydantic models for request validation

## Project 1: Basic Database Setup with SQLAlchemy

### Code Implementation
```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base

# Create SQLite engine instance
engine = create_engine("sqlite:///todooo.db")

# Create declarative base meta instance
Base = declarative_base()

class ToDo(Base):
    __tablename__ = 'todos'
    id = Column(Integer, primary_key=True)
    task = Column(String(256))
```

### Key Concepts Learned:
1. Setting up SQLAlchemy engine
2. Creating declarative base for ORM models
3. Defining database models with columns and constraints
4. Understanding table and column configurations

## Project 2: Complete Todo API with FastAPI and SQLAlchemy

### Code Implementation
```python
from fastapi import FastAPI, status, HTTPException
from database import Base, engine, ToDo
from pydantic import BaseModel
from sqlalchemy.orm import Session

# Create database tables
Base.metadata.create_all(engine)

# Initialize FastAPI app
app = FastAPI()

# Pydantic model for request validation
class ToDoRequest(BaseModel):
    task: str

# Route handlers
@app.get("/")
async def root():
    return "todoo"

@app.post("/todo", status_code=status.HTTP_201_CREATED)
async def create_todo(todo: ToDoRequest):
    session = Session(bind=engine, expire_on_commit=False)
    tododb = ToDo(task=todo.task)
    
    session.add(tododb)
    session.commit()
    id = tododb.id
    session.close()
    
    return f"created to_do item with id {id}"

@app.get("/todo/{id}")
async def read_todo(id: int):
    session = Session(bind=engine, expire_on_commit=False)
    todo = session.query(ToDo).get(id)
    session.close()
    
    if not todo:
        raise HTTPException(status_code=404, detail=f"to_do item with id {id} not found")
    
    return todo

@app.get("/todo")
async def read_todo():
    session = Session(bind=engine, expire_on_commit=False)
    todo_list = session.query(ToDo).all()
    session.close()
    return todo_list

@app.put("/todo/{id}")
async def update_todo(id: int, task: str):
    session = Session(bind=engine, expire_on_commit=False)
    todo = session.query(ToDo).get(id)
    
    if todo:
        todo.task = task
        session.commit()
    
    session.close()
    
    if not todo:
        raise HTTPException(status_code=404, detail=f"to_do item with id {id} not found")
    return todo

@app.delete("/todo/{id}", status_code=status.HTTP_204_NO_CONTENT)
async def delete_todo(id: int):
    session = Session(bind=engine, expire_on_commit=False)
    todo = session.query(ToDo).get(id)
    
    if todo:
        session.delete(todo)
        session.commit()
        session.close()
    else:
        raise HTTPException(status_code=404, detail=f"to_do item with id {id} not found")
    
    return None
```

### Key Features Implemented:
1. **CRUD Operations**:
   - Create: POST `/todo`
   - Read: GET `/todo` and GET `/todo/{id}`
   - Update: PUT `/todo/{id}`
   - Delete: DELETE `/todo/{id}`

2. **Database Integration**:
   - SQLAlchemy session management
   - Database operations (query, add, update, delete)
   - Proper session closing

3. **Error Handling**:
   - 404 errors for non-existent items
   - Appropriate status codes for different operations

## Technical Concepts Mastered

### FastAPI
- Route decorators (`@app.get`, `@app.post`, etc.)
- Status codes and HTTP exceptions
- Request/Response models with Pydantic
- Path parameters and request body handling

### SQLAlchemy
- Engine configuration
- Session management
- Basic CRUD operations
- Model definition and table creation

### Best Practices Learned
1. Always close database sessions
2. Use appropriate HTTP status codes
3. Implement proper error handling
4. Validate request data using Pydantic models
5. Structure code for maintainability

## Testing
- Tested all endpoints using FastAPI's automatic interactive docs (Swagger UI)
- Verified CRUD operations with SQLite database
- Confirmed proper error handling and status codes

## Challenges Faced
1. Understanding SQLAlchemy session management
2. Implementing proper error handling
3. Managing database connections efficiently

## Next Steps
- [ ] Implement authentication and authorization
- [ ] Add more complex database relationships
- [ ] Implement database migrations
- [ ] Add input validation and sanitization
- [ ] Implement proper logging

## Resources Used
- FastAPI official documentation
- SQLAlchemy documentation
- Python type hints documentation
- SQLite documentation

## Personal Notes
SQLAlchemy initially seemed complex but became clearer after implementing the Todo API. The combination of FastAPI's modern features with SQLAlchemy's powerful ORM capabilities creates a robust foundation for building APIs. Proper session management and error handling are crucial for production-ready applications.
