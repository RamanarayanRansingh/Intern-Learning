# Day 1: Introduction to APIs and FastAPI

## Core Concepts Learned

### 1. What is an API?
**API (Application Programming Interface)**
- A set of rules and protocols for building and interacting with software applications
- Acts as a bridge between different software systems
- Allows applications to communicate with each other

#### Types of APIs

1. **Web APIs**
   - REST API
   - SOAP API
   - GraphQL
   - RPC
   
2. **Operating System APIs**
   - Windows API
   - Linux Kernel API
   - macOS API

3. **Database APIs**
   - JDBC (Java Database Connectivity)
   - ODBC (Open Database Connectivity)

4. **Hardware APIs**
   - Device drivers
   - Low-level system interfaces

### 2. Deep Dive into REST API

**REST (Representational State Transfer)**
A software architectural style that defines a set of constraints for creating web services.

#### Key Principles of REST

1. **Stateless Communication**
   - Server doesn't store client state
   - Each request contains all information needed
   - Benefits:
     - Better scalability
     - Simpler server implementation
     - More reliable

2. **Client-Server Architecture**
   - Separation of concerns
   - Client handles user interface
   - Server handles data storage
   - Benefits:
     - Independent evolution
     - Better scalability
     - Improved portability

3. **Uniform Interface**
   - Standardized way to communicate
   - Resources identified in requests
   - Self-descriptive messages
   - HATEOAS (Hypertext As The Engine Of Application State)

4. **Resource-Based**
   - Everything is a resource
   - Resources have unique identifiers (URIs)
   - Resources can have multiple representations (JSON, XML, etc.)

#### HTTP Methods in REST
```
GET     - Retrieve resource
POST    - Create new resource
PUT     - Update existing resource
DELETE  - Remove resource
PATCH   - Partial update of resource
```

#### HTTP Status Codes
```
1xx - Informational
2xx - Success (200 OK, 201 Created, 204 No Content)
3xx - Redirection
4xx - Client Errors (400 Bad Request, 404 Not Found)
5xx - Server Errors
```

### 3. FastAPI Framework

**What is FastAPI?**
- Modern, fast web framework for building APIs with Python
- Based on Python 3.6+ type hints
- Built on top of Starlette and Pydantic

#### Key Features

1. **Fast Performance**
   - On par with NodeJS and Go
   - Based on Starlette and Pydantic
   - Async support

2. **Automatic API Documentation**
   - Swagger UI (/docs)
   - ReDoc (/redoc)
   - Based on OpenAPI (formerly Swagger)

3. **Type Hints and Validation**
   - Python type hints for validation
   - Automatic data validation
   - IDE support and completion

## Practical Implementation

### First FastAPI Application
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root():
    return {"message": "Hello World"}

@app.get("/items/{item_id}")
async def read_item(item_id: int):
    return {"item_id": item_id}
```

### Path Parameters
```python
@app.get("/users/{user_id}")
async def read_user(user_id: int):
    return {"user_id": user_id}
```

### Query Parameters
```python
@app.get("/items/")
async def read_items(skip: int = 0, limit: int = 10):
    return {"skip": skip, "limit": limit}
```

### Request Body
```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    description: str = None
    price: float
    tax: float = None

@app.post("/items/")
async def create_item(item: Item):
    return item
```

## Key Concepts Mastered

### 1. API Design Principles
- Resource naming
- Endpoint structure
- HTTP methods usage
- Status code selection

### 2. FastAPI Basics
- Route creation
- Path parameters
- Query parameters
- Request/Response models

### 3. Python Type Hints
- Basic types (int, str, float)
- Optional types
- Pydantic models
- Type validation

## Tools and Environment Setup

1. **Development Environment**
   - Python 3.8+
   - Virtual Environment (venv)
   - IDE/Editor setup

2. **Required Packages**
```bash
pip install fastapi
pip install uvicorn
```

3. **Running the Application**
```bash
uvicorn main:app --reload
```

## Best Practices Learned

1. **API Design**
   - Use nouns for resources
   - Keep URLs simple and logical
   - Use proper HTTP methods
   - Return appropriate status codes

2. **Code Organization**
   - Separate concerns
   - Use proper naming conventions
   - Implement error handling
   - Document code properly

3. **Performance Considerations**
   - Async where appropriate
   - Proper parameter validation
   - Efficient data handling

## Testing

1. **Manual Testing**
   - FastAPI Swagger UI
   - Curl commands
   - Browser testing

2. **Tools Used**
   - FastAPI's interactive docs
   - Browser developer tools
   - API testing tools

## Challenges Faced

1. **Understanding Async Concepts**
   - When to use async/await
   - Handling async operations
   - Understanding coroutines

2. **Type Hints**
   - Proper usage in Python
   - Integration with Pydantic
   - Optional vs Required fields

## Next Steps

1. **Advanced Topics to Explore**
   - Authentication
   - Database integration
   - File handling
   - WebSocket support
   - Background tasks

2. **Skills to Develop**
   - Error handling patterns
   - Testing strategies
   - Documentation practices
   - Security best practices

## Resources Used
- FastAPI Official Documentation
- REST API Design Guidelines
- Python Type Hints Documentation
- Async Python Documentation

Would you like me to expand on any of these sections or add more specific examples?
