DECORATOR HTTP  POST   API  request  URL ROUTE ENDPOINT 
1.1 — What is FastAPI?

Definition:
FastAPI
is a Python web framework used to build APIs. It supports asynchronous endpoints, automatic interactive documentation, type hints and request validation.

Purpose: We'll use FastAPI to build APIs that allow an administrator or employee to create, view, update and manage employee information.

Why do we need it?

Imagine an employee management application:

The frontend sends employee information to the backend.

FastAPI receives the HTTP request.

Pydantic validates the incoming data.

SQLAlchemy communicates with MySQL.

FastAPI returns a JSON response.

python --version --- Python 3.12.10.


1.4 — Create a virtual environment

Definition: A virtual environment is an isolated Python environment that keeps a project's installed packages separate from other Python projects.

Purpose: We can install FastAPI and other dependencies for this project without changing packages in your other projects.

Syntax:

python -m venv venv

Activate it in Windows Command Prompt:

venv\Scripts\activate

Or in PowerShell:

.\venv\Scripts\Activate.ps1.

Install FastAPI and Uvicorn

What is Uvicorn?

Uvicorn is an ASGI server that runs our FastAPI application and handles incoming HTTP requests.

Verify the installation:

python -m pip show fastapi
python -m pip show uvicorn
-------------------------------------------
Definition: API stands for Application Programming Interface. It allows two software applications to communicate with each other.

Purpose: Our Employee Management API will allow a frontend application, Swagger UI or another client to send requests to FastAPI and receive employee information.

--- Our API will provide endpoints to retrieve, add, update and delete these records.
HTTP methods specify what action a client wants to perform on a resource.

Understand HTTP methods --- HTTP methods define the operation a client wants to perform on a server resource. GET retrieves data, POST creates data, PUT replaces or updates a resource, and DELETE removes a resource.
-------------------------------
@app.get() is a decorator that registers an HTTP GET endpoint.

>>> {employee_id} is the dynamic portion of the URL.

/employees/ is the API path.

>>> employee_id: int tells FastAPI to parse and validate the path parameter as an integer.

async def defines an asynchronous endpoint function.

get_employees() is the function that executes when the endpoint receives a GET request.


get_employee() is the function that handles the request.
------------------------
1. What is a path parameter?

Definition: A path parameter is a variable included in the URL path that allows an API to identify a specific resource.
Purpose: Instead of retrieving every employee, we can retrieve only the employee whose ID the client requests.

Code   ----  Explanation
@app.get() ---- Defines an HTTP GET endpoint.
/employees/{employee_id}---  URL path containing a variable.
{employee_id} ---- Path parameter placeholder.
employee_id: int ----  Declares the parameter as an integer.
async def ---  Defines an asynchronous endpoint function.
return -----  Sends the response to the client.
---------------------------------
Q1. What is a path parameter in FastAPI?

A path parameter is a variable in the URL used to identify a particular resource. For example, /employees/2 uses 2 as the employee ID to retrieve a specific employee.

Q2. How do you define a path parameter in FastAPI?

We define it using curly braces in the route path and declare it as a function parameter with a type annotation.

@app.get("/employees/{employee_id}")
async def get_employee(employee_id: int):
    return {"employee_id": employee_id}

Q3. What happens if the employee ID doesn't exist?

We raise an HTTPException with status code 404 and the detail message Employee not found.

Q4. What happens if we pass a string instead of an integer?

FastAPI validates the path parameter based on its type annotation. If we pass a non-integer value, it automatically returns a 422 validation error.

Q5. What is the difference between a path parameter and a query parameter?

A path parameter identifies a resource, while a query parameter is commonly used to filter, sort or paginate results.

Path parameter:
/employees/2

Query parameter:
/employees/?department=IT
--------------------------------------
1. What is a query parameter?

Definition: A query parameter is a key-value pair passed in the URL after a question mark (?). It provides additional information to an API, commonly for filtering, searching, sorting or pagination.

Purpose: Query parameters allow clients to request specific subsets of data without creating a separate endpoint for every filter.

Example URL

/employees/filter?department=IT

>>>  /employees/filter ---- API endpoint
>>>   ?  ----- Begins the query parameters
>>> department --- Query parameter name
>>>  = --- Separates the name and value
>> IT  ---- Query parameter value

-------------------------------
"I use list comprehension to iterate through the employee list and filter employees based on their department. I use lower() to ignore case differences and strip() to remove extra spaces from the input. Only employees whose department matches the requested department are included in the new list."     "I use the built-in len() function to count the number of employees in the filtered list and return that count in the API response."     "In this code, I created a GET API endpoint in FastAPI to filter employees by department. I accept the department as a query parameter. Then I use list comprehension to iterate through the employee list and select employees whose department matches the requested department.
I use strip() to remove extra spaces and lower() to make the comparison case-insensitive. Finally, I return a dictionary containing a success message, the requested department, the total number of matching employees and the filtered employee data."
--------------------------------
It is called list comprehension.
First, what is list comprehension?
List comprehension is a shorter way to create a new list from an existing iterable, such as a list, based on a condition.
Using list comprehension:
numbers = [10, 20, 30, 40, 50]even_numbers = [    num for num in numbers if num % 20 == 0]print(even_numbers)



Output:
[20, 40]
---------------------------------------
2. employee
employee
This is the value that will be added to the new list whenever the condition is true.
Each employee represents one dictionary from the employees list.

>>>  if employee["department"].lower() == department.strip().lower()

This is the filtering condition. It decides which employee records should be included in the result.
Using .lower() makes the comparison case-insensitive.
== is the equality comparison operator.
------
Code ----	Meaning
@app.get()------	Defines a GET API route
async def-----	Defines an asynchronous function
department: str -----	Required string query parameter
filtered_employees ----	Stores matching employee records
for employee in employees -----	Iterates through employee records
if	Checks a condition
employee["department"] ------ Accesses the employee's department
.lower() ----	Converts text to lowercase
.strip() -----	Removes leading and trailing whitespace
== --------	Compares two values for equality
len() -----	Counts the items in a collection
return ------- 	Returns the API response

-------------------------------------------
Q1. What is a query parameter in FastAPI?

A query parameter is a key-value pair passed in the URL after a question mark. It is commonly used for filtering, searching, sorting and pagination.

Q2. How do you create a query parameter in FastAPI?

We define a parameter in the endpoint function with a type annotation. FastAPI automatically recognizes it as a query parameter if it isn't declared in the route path.

@app.get("/employees/filter")
async def filter_employees(department: str):
    return {"department": department}

Q3. What is the difference between a path parameter and a query parameter?

A path parameter identifies a specific resource, such as /employees/2. A query parameter filters or modifies the result, such as /employees/filter?department=IT.

Q4. What happens when no employees match the requested department?

The API returns a successful 200 OK response with an empty data list and a count of zero. No matching records doesn't necessarily mean the requested API resource is missing.

Q5. Why do we use async def?

async def allows us to write asynchronous endpoints. When we later connect to MySQL using an asynchronous driver, we'll use await for database I/O operations.
--------------------------------------------------
1. What is a POST request?

Definition: POST is an HTTP method used to submit data to a server, commonly to create a new resource.

Purpose: In our Employee Management System, POST allows an administrator to register a new employee by sending their name, department, salary and other details to the API.

The client sends employee information in the request body, and the API validates and processes it.

2. What is a request body?

Definition: A request body is the data sent by the client to the server as part of an HTTP request.

3. What is JSON?

Definition: JSON (JavaScript Object Notation) is a lightweight data format used to exchange structured information between applications.
JSON is a lightweight, text-based data interchange format used to transfer structured data between a client and server. FastAPI automatically parses JSON request bodies into Python data using Pydantic models.
-----------------------
JSON element ---- Meaning
{}---- JSON object
"name"---- Key
"Suresh"--- String value
"salary" ---- Key
60000 ----Numeric value
: ---- Separates a key from its value
,---- Separates object properties
---------------------------
4. What is Pydantic?

Definition:
Pydantic
is a Python library used to validate, parse and serialize data using type annotations.

Purpose: When someone creates an employee, we need to make sure the submitted information is valid.

For example:

Employee name should not be empty.

Department should be provided.

Salary should be a positive number.

Invalid data should produce a clear validation error.

Without validation, incorrect data could enter our application and cause problems later.
5. What is a Pydantic schema?

A Pydantic schema is a class that defines the expected structure and validation rules for data.

Request schema vs response schema

Schema ------  Purpose
EmployeeCreate ----- Defines and validates the data submitted by the client
EmployeeResponse----  Defines the data returned by the API
----------------------------------------------
Q1. What is a POST request in FastAPI?

A POST request is used to submit data to the server, commonly to create a new resource. In our Employee Management API, we use POST /employees/ to register a new employee.

Q2. What is a request body?

A request body contains the data sent by a client to the server. In our API, the request body contains the employee's name, department and salary in JSON format.

Q3. What is a Pydantic schema?

A Pydantic schema is a class based on BaseModel that defines the expected structure and validation rules for data. FastAPI uses it to validate request bodies and generate API documentation.

Q4. What is the difference between EmployeeCreate and EmployeeResponse?

EmployeeCreate defines the input fields required to create an employee, whereas EmployeeResponse defines the fields returned by the API, including the generated employee ID.

Q5. What is response_model in FastAPI?

response_model specifies the expected structure of the API response. FastAPI validates the returned data, serializes it and filters out fields not declared in the response schema.

Q6. Why does POST return status code 201?

HTTP 201 Created indicates that the request successfully created a resource. It is the appropriate status code for our successful employee creation endpoint.

Q7. Where is the employee data stored right now?

Currently, the employee records are stored in a Python list in memory. They are lost when the application restarts. In the database chapter, we'll use MySQL with SQLAlchemy to store employee data persistently.
--------------------------------
Q1. What is the purpose of the PUT method in FastAPI?
PUT is used to update an existing resource. In FastAPI, we can define a PUT endpoint using @app.put().
Q2. What is the purpose of the DELETE method?
DELETE is used to remove a resource. We can define a DELETE endpoint using @app.delete().
Q3. What is a 404 error?
404 Not Found is an HTTP status code that indicates the requested resource does not exist.
Q4. Why do we use Pydantic in update APIs?
Pydantic validates incoming data against the defined model. It helps ensure the update request contains the expected fields and data types.
Q5. What is the difference between pop() and remove()?
- pop(index) removes an item by its position and returns the removed item.
- remove(value) removes the first matching value from a list and does not return the removed item.
Q6. How do you verify whether an update or deletion was successful?
Call a GET endpoint after the operation. The GET response should reflect the updated or deleted employee record.
-------------------------------------------------
5. PUT vs PATCH
PUT	PATCH
Usually replaces the resource's complete editable representation	Updates only specified fields
Typically requires all fields in the update model	Can accept partial fields
Example: update name, age, department and salary together	Example: update only salary
For example, if you only want to change salary, a PATCH request might use:

{
  "salary": 45000
}
------------------------------------
