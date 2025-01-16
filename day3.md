# Detailed Method & Syntax Explanation - DSA Tracker

## FastAPI Components

### 1. Class Definitions
```python
class ProblemCreate(BaseModel):
    title: str
    difficulty: str
    category: str
    time_taken: float
    notes: str
    code_solution: str
```
**Explanation:**
- `BaseModel`: A Pydantic class that provides data validation
- `title: str`: Defines that 'title' must be a string. The `: str` syntax is Python type hinting
- Each line defines a required field and its type
- This class validates incoming data when creating a new problem

### 2. FastAPI Route Decorators
```python
@app.post("/problems/")
```
**Explanation:**
- `@app`: References your FastAPI application instance
- `.post`: Specifies HTTP method (POST, GET, PUT, DELETE)
- `"/problems/"`: The URL endpoint path
- This decorator tells FastAPI "run this function when someone sends a POST request to /problems/"

### 3. Database Session Management
```python
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```
**Explanation:**
- `SessionLocal()`: Creates new database session
- `yield`: Pauses function and returns session
- `finally`: Always executes after session use
- This ensures database connections are properly closed

### 4. Dependency Injection
```python
def create_problem(problem: ProblemCreate, db: Session = Depends(get_db)):
```
**Explanation:**
- `problem: ProblemCreate`: Parameter must match ProblemCreate model
- `db: Session = Depends(get_db)`: FastAPI will automatically:
  1. Call get_db()
  2. Use the session
  3. Close it after request
- `Depends`: FastAPI dependency injection system

## SQLAlchemy Methods

### 1. Query Operations
```python
db.query(Problem).get(id)
```
**Breakdown:**
- `db`: Database session
- `.query(Problem)`: Start a query on Problem table
- `.get(id)`: Fetch by primary key
- Alternative methods:
  ```python
  db.query(Problem).all()           # Get all records
  db.query(Problem).first()         # Get first record
  db.query(Problem).filter_by(...)  # Filter records
  ```

### 2. Database Operations
```python
db.add(db_problem)      # Add new record
db.commit()             # Save changes
db.refresh(db_problem)  # Refresh instance from database
```
**Explanation:**
- `db.add()`: Stages object for saving
- `db.commit()`: Actually saves to database
- `db.refresh()`: Updates object with database values

## Streamlit Components

### 1. Basic UI Elements
```python
st.title("🎯 DSA Practice Tracker")
st.header("Record New Practice")
```
**Explanation:**
- `st.title()`: Creates large header text
- `st.header()`: Creates smaller section header
- Can include emojis and formatting

### 2. Input Elements
```python
title = st.text_input("Problem Title")
difficulty = st.selectbox("Difficulty", ["Easy", "Medium", "Hard"])
time_taken = st.number_input("Time Taken (minutes)", min_value=1, value=30)
```
**Explanation:**
- `st.text_input()`: Creates text input field
  - First argument: Label
  - Returns: User input as string
- `st.selectbox()`: Creates dropdown
  - First argument: Label
  - Second argument: List of options
- `st.number_input()`: Creates numeric input
  - `min_value`: Minimum allowed value
  - `value`: Default value

### 3. Layout Elements
```python
col1, col2 = st.columns(2)
with col1:
    # content for first column
```
**Explanation:**
- `st.columns(2)`: Creates two equal columns
- `with col1:`: Content inside will be in first column
- Helps create responsive layouts

### 4. Forms
```python
with st.form("practice_form"):
    # form elements
    if st.form_submit_button("Save Practice"):
        # handle submission
```
**Explanation:**
- `st.form()`: Creates a form container
- `st.form_submit_button()`: Creates submit button
- Forms batch all inputs into single submission

### 5. State Management
```python
if 'editing' not in st.session_state:
    st.session_state.editing = None
```
**Explanation:**
- `st.session_state`: Streamlit's state management
- Persists values between reruns
- Used here to track which problem is being edited

## API Integration

### Making API Calls
```python
response = requests.post(f"{API_URL}/problems/", json=practice_data)
```
**Explanation:**
- `requests.post()`: Sends POST request
- `json=practice_data`: Sends data as JSON
- `response.status_code`: Check if request succeeded
- Common status codes:
  - 200: Success
  - 201: Created
  - 404: Not Found
  - 500: Server Error

### Data Processing
```python
df = pd.DataFrame(problems)
df['date_solved'] = pd.to_datetime(df['date_solved'])
```
**Explanation:**
- `pd.DataFrame()`: Creates pandas DataFrame from list
- `pd.to_datetime()`: Converts string to datetime
- Used for data manipulation and visualization

## Common Patterns

### Error Handling
```python
if not db_problem:
    raise HTTPException(status_code=404, detail="Problem not found")
```
**Explanation:**
- `if not db_problem`: Check if object exists
- `raise HTTPException`: Sends error response
- FastAPI converts this to proper HTTP response

### Update Operations
```python
problem_data = problem.model_dump(exclude_unset=True)
for key, value in problem_data.items():
    setattr(db_problem, key, value)
```
**Explanation:**
- `model_dump()`: Converts Pydantic model to dict
- `exclude_unset=True`: Only includes set values
- `setattr()`: Sets attribute on object
- Updates only provided fields

Would you like me to explain any particular aspect in more detail?

# DSA Practice Tracker: Complete Guide and Concepts

## 1. Project Structure
The application is built using three main components:
- **models.py**: Database models and configuration
- **main.py**: FastAPI backend server
- **streamlit_app.py**: Frontend user interface

## 2. Database Concepts (models.py)

### SQLAlchemy Basics
```python
from sqlalchemy import create_engine, Column, Integer, String, DateTime, Float
```
- **SQLAlchemy**: An ORM (Object Relational Mapper) that converts Python classes to database tables
- **create_engine**: Creates database connection
- **Column**: Defines database column types
- **Integer, String, DateTime, Float**: Different data types for columns

### Database Connection
```python
SQLALCHEMY_DATABASE_URL = "sqlite:///./dsa_practice.db"
engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False})
```
- **SQLite**: Lightweight database stored in a file
- **check_same_thread=False**: Allows SQLite to handle multiple requests

### Session Management
```python
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
```
- **sessionmaker**: Creates database sessions
- **autocommit=False**: Changes must be explicitly committed
- **autoflush=False**: Changes aren't automatically written to database

### Model Definition
```python
class Problem(Base):
    __tablename__ = "problems"
    id = Column(Integer, primary_key=True, index=True)
    # ... other columns
```
- **Base**: Parent class for all database models
- **__tablename__**: Specifies table name in database
- **primary_key**: Unique identifier for each row
- **index=True**: Creates database index for faster queries

## 3. FastAPI Backend (main.py)

### Pydantic Models
```python
class ProblemCreate(BaseModel):
    title: str
    difficulty: str
    # ... other fields
```
- **Pydantic**: Data validation library
- **BaseModel**: Parent class for data validation
- **Type hints (str, float)**: Enforce data types

### Dependency Injection
```python
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```
- **Dependency Injection**: Provides database session to routes
- **yield**: Creates a generator function
- **finally**: Ensures database connection is closed

### API Routes
```python
@app.post("/problems/")
def create_problem(problem: ProblemCreate, db: Session = Depends(get_db)):
```
- **@app.post**: HTTP POST decorator
- **Depends(get_db)**: Injects database session
- **response_model**: Defines response data structure

### CRUD Operations
- **CREATE**: `POST /problems/`
- **READ**: `GET /problems/`
- **UPDATE**: `PUT /problems/{problem_id}`
- **DELETE**: `DELETE /problems/{problem_id}`

## 4. Streamlit Frontend (streamlit_app.py)

### Basic Streamlit Concepts
```python
st.title("🎯 DSA Practice Tracker")
tab1, tab2, tab3 = st.tabs(["Add Practice", "View Progress", "Manage Problems"])
```
- **st**: Streamlit module
- **st.title**: Page title
- **st.tabs**: Creates tabbed interface

### Form Handling
```python
with st.form("practice_form"):
    title = st.text_input("Problem Title")
```
- **st.form**: Creates form container
- **st.text_input**: Text input field
- **st.form_submit_button**: Form submission button

### Data Visualization
```python
fig1 = px.pie(df, names='difficulty')
st.plotly_chart(fig1)
```
- **px**: Plotly Express for charts
- **px.pie**: Creates pie chart
- **st.plotly_chart**: Displays Plotly chart

### State Management
```python
if 'editing' not in st.session_state:
    st.session_state.editing = None
```
- **st.session_state**: Persists data between reruns
- **editing**: Tracks which problem is being edited

### API Integration
```python
response = requests.post(f"{API_URL}/problems/", json=practice_data)
```
- **requests**: HTTP client library
- **response.status_code**: HTTP response status
- **json=practice_data**: Sends JSON data

## 5. Common Python Features Used

### List Comprehension
```python
[problem for problem in problems if problem.difficulty == "Easy"]
```
- Creates new list based on condition

### Dictionary Comprehension
```python
{key: value for key, value in data.items()}
```
- Creates dictionary with transformed data

### f-strings
```python
f"Problem {problem.id}"
```
- String interpolation with variables

### Type Hinting
```python
def get_problems() -> List[Problem]:
```
- Indicates expected return types
- Helps with code documentation

## 6. Best Practices Demonstrated

1. **Separation of Concerns**
   - Database models separate from API
   - Frontend separate from backend
   - Clear responsibility for each file

2. **Error Handling**
   - Try/except blocks
   - HTTP error responses
   - User feedback for actions

3. **Code Organization**
   - Logical file structure
   - Related functionality grouped together
   - Clear naming conventions

4. **Data Validation**
   - Pydantic models
   - Type checking
   - Required vs optional fields

5. **User Experience**
   - Intuitive interface
   - Visual feedback
   - Confirmation for deletions

## 7. Common Operations

### Adding a New Problem
1. Navigate to "Add Practice" tab
2. Fill in problem details
3. Click "Save Practice"

### Viewing Progress
1. Go to "View Progress" tab
2. Check metrics and charts
3. Analyze performance

### Editing a Problem
1. Find problem in "Manage Problems"
2. Click "Edit" button
3. Update details
4. Save changes

### Deleting a Problem
1. Locate problem in list
2. Click "Delete"
3. Confirm deletion
