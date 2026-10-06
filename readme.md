Learning chapters

Chapter

	

Concept

	

What we'll build and execute




1

	

Project setup

	

Python environment, FastAPI installation, MySQL Workbench database




2

	

FastAPI basics

	

First API, GET, POST, path and query parameters




3

	

MySQL connection

	

Async SQLAlchemy engine, sessions and lifespan




4

	

Employee model

	

SQLAlchemy model, columns, primary keys and table creation




5

	

Pydantic schemas

	

Request validation, response_model, field constraints




6

	

Employee CRUD

	

Create, read, update and delete employee records




7

	

Dependencies

	

Depends(), reusable database sessions and common dependencies




8

	

Departments

	

Department model, foreign keys and relationships




9

	

Salary

	

Salary records, calculations and validation




10

	

Attendance

	

Attendance records, dates, status and constraints




11

	

SQL queries

	

JOIN, WHERE, GROUP BY, HAVING, COUNT, SUM and AVG




12

	

Reports

	

Employee salary, department and attendance reports




13

	

Authentication

	

Registration, password hashing and login




14

	

OAuth2 and JWT

	

Access tokens, token verification and protected APIs




15

	

Authorization

	

Admin and employee roles, API key security




16

	

Advanced FastAPI

	

Middleware, CORS, events, cookies, headers, forms, file uploads and background tasks




17

	

Testing and deployment

	

Pytest, API testing, error handling, production configuration and deployment

We'll introduce concepts progressively, including small tests after each feature, rather than waiting until the end to test the whole application.

Final project folder structure

We'll create this structure gradually. You don't need to create every file now.
employee_management_api/
│
├── venv/
├── .env
├── .gitignore
├── requirements.txt
├── main.py
├── database.py
├── security.py
├── dependencies.py
│
├── models/
│   ├── __init__.py
│   ├── employee.py
│   ├── department.py
│   ├── salary.py
│   ├── attendance.py
│   └── user.py
│
├── schemas/
│   ├── __init__.py
│   ├── employee.py
│   ├── department.py
│   ├── salary.py
│   ├── attendance.py
│   └── user.py
│
├── routers/
│   ├── __init__.py
│   ├── employees.py
│   ├── departments.py
│   ├── salaries.py
│   ├── attendance.py
│   ├── reports.py
│   └── auth.py
│
├── services/
│   ├── __init__.py
│   └── employee_service.py
│
├── tests/
│   ├── conftest.py
│   ├── test_employees.py
│   ├── test_auth.py
│   └── test_reports.py
│
└── uploads/

pip install fastapi, uvicorn , python -m venv venv , .\venv\Scripts\Activate.ps1,

python -m pip install -r requirements.txt

The services folder will contain reusable business logic. The routers folder will handle HTTP requests, while models and schemas will separate database structure from API input and output.

>> Create a file named requirements.txt in the project root.

Definition: requirements.txt lists the Python packages required to run our project.
Purpose: Anyone can install the project's dependencies using one command:
python -m pip install -r requirements.txt

step1::
Create the first FastAPI application.

Explanation of main.py

from fastapi import FastAPI ----Imports the FastAPI class from the installed package.
app = FastAPI(...) --------- This creates the FastAPI application instance. The title and version appear in the API documentation.
@app.get("/") ---- A route decorator that maps HTTP GET requests to the root URL.
async def home() ---- Defines an asynchronous Python function to handle the request. We'll use async def for endpoints involving asynchronous I/O.
return {...} ---- FastAPI converts this Python dictionary into JSON and sends it to the client.
@app.get("/health") ----- Creates a health-check endpoint to verify that the application is responding.

>>  For simple endpoints without I/O, a regular def is also valid.

What is async def in FastAPI?

async def defines an asynchronous function in Python. It allows FastAPI to work with asynchronous operations, such as database queries and HTTP requests, using await, without blocking the event loop while waiting for those operations.
>>>  async def----  Asynchronous API endpoints and database I/O(implementation) -----  Swagger requests and concurrent requests(test)

For example, later we'll use:

result = await db.execute(query)

This waits for the database operation asynchronously.
------------------------------------------------------
uvicorn main:app --reload

Understand this command:

uvicorn — starts the ASGI server.

main — refers to the main.py file.

app — refers to the FastAPI instance named app.

--reload — automatically reloads the application when you save code changes. This is intended for development.
{employee_id} is the dynamic portion of the URL.

employee_id: int tells FastAPI to parse and validate the path parameter as an integer.

get_employee() is the function that handles the request.
--------------------------------------------------

>>>  connect  the database   to the fastapi  using   sqlalchemy .

Definition:
MySQL Workbench
is a graphical tool used to connect to MySQL, execute SQL queries, create databases and inspect tables.

message provides a descriptive response.

len(employees) counts the employees in the list.

data returns all employee records.

FastAPI automatically converts the returned dictionary into JSON.

--------------------------------------------------
>>>>  from fastapi import FastAPI, HTTPException

Definition: HTTPException is a FastAPI exception used to return an HTTP error response with a status code and detail message.

Purpose: If the requested employee ID doesn't exist, we should return 404 Not Found instead of an empty or misleading success response.

-------------------------------------------
Definition: A for loop iterates through each item in a collection.

Purpose: We need to search the employee list to find a record matching the requested ID.
------------------------
Handle a missing employee
raise HTTPException(
    status_code=404,
    detail="Employee not found"
)

If the loop finishes without finding a matching ID, FastAPI returns an HTTP 404 error.

Important: raise stops the function and raises the exception. It isn't the same as return. The 404 status indicates that the requested employee resource wasn't found.
----------------------------
n the previous lessons, we created:

GET /employees/ — retrieve all employees.

GET /employees/{employee_id} — retrieve one employee using their ID.

Now we'll create:

- GET /employees/filter?department=IT — retrieve employees belonging to the IT department.

----  Here, department: str is a required query parameter. FastAPI reads it from the URL and passes its value to the function.
--------------------------------
This is called a list comprehension in Python.

Let's break it into parts:

Expression

	

Purpose




for employee in employees

	

Iterate over every employee




employee["department"]

	

Read that employee's department




.lower()

	

Convert the department to lowercase for case-insensitive comparison




department.strip()

	

Remove leading and trailing spaces from the requested department




if ... == ...

	

Keep only matching employees




[...]

	

Build a new list containing the matching records

For example, if the requested department is "IT", the list comprehension checks each employee and returns only Lakshmi and Arjun.
---------------------
message gives the client a descriptive response.

department shows the requested department.

total_employees counts matching records.

data contains the filtered employee list.

If no employees match, the endpoint returns an empty list with a count of zero. This is a successful filter operation, so we'll return 200 OK, not 404.
--------------------------------
EmployeeCreate validates incoming employee information.

EmployeeResponse validates and filters the returned employee data.

This decorator registers the POST endpoint.

Parameter ----Purpose
"/employees/" ---- URL for creating employees
response_model=EmployeeResponse ------ Validates and filters the outgoing response
status_code=status.HTTP_201_CREATED ----  Returns HTTP 201 when an employee is successfully created.

--------------------------
Step 3 — Receive the request body
async def create_employee(employee: EmployeeCreate):

Here, employee is the request body parameter, and EmployeeCreate is its Pydantic schema.

FastAPI automatically:

Reads the incoming JSON.

Parses it into a Python object.

Validates its fields using EmployeeCreate.

Returns a 422 validation error if the submitted data is invalid.

Calls the endpoint function if validation succeeds.

>>>  This finds the highest existing employee ID and adds one.
>>> default=0 handles an empty employee list.
new_id = max(
    (item["employee_id"] for item in employees),
    default=0
) + 1 
>>> We create a new dictionary using the generated ID and validated fields from the Pydantic object.
>> append() adds the new employee dictionary to the end of the existing Python list.
-------------------------------------------------
BaseModel: Provides data validation using Pydantic.
EmployeeUpdate: Model name for the updated employee details.
All  fields are required in this model. This is appropriate for a full update using PUT.

Here are two important concepts:
- employee.update(...): Updates a dictionary using the key-value pairs from another dictionary.
- raise HTTPException(...): Stops normal execution and sends an HTTP error response.
status_code=404 means the requested employee does not exist.
employee_data.model_dump() converts the validated Pydantic model into a Python dictionary.
enumerate() gives us two values:
- index: The position of the employee in the list.
- employee: The employee dictionary itself.


