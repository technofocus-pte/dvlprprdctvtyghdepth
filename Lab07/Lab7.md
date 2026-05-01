# **Lab 1:** Building a task management REST API with GitHub Copilot as an AI pair programmer

**Scenario :**

You have just joined **ZavaTask Inc.**, a mid-size SaaS company. Your
team lead has assigned you to build a **Task Management REST API** from
scratch — a backend microservice that lets users create, retrieve,
update, and delete tasks. The codebase is an **empty repository** with
no business logic. You are expected to go from zero to a working,
tested, and documented service in a single sprint.

Your manager says: *"Use GitHub Copilot to accelerate, but you own the
quality. Every line of code ships under your name."*

This lab simulates that real-world scenario. You will scaffold, build,
test, and document the service — using GitHub Copilot as your AI pair
programmer at every step — while learning **when to accept, when to
refine, and when to reject** its suggestions.

**Objectives (Copilot-Focused)**

By the end of this lab, you will be able to:

1.  Use **natural language comments** to drive Copilot's inline code
    suggestions for project scaffolding.

2.  Craft **intent-driven prompts** in Copilot Chat to generate
    application features faster.

3.  Evaluate, accept, or refine Copilot suggestions to
    implement **business logic** while retaining developer ownership.

4.  Use Copilot's **/tests** slash command and chat prompts
    to **generate unit tests**.

5.  Use Copilot's **/doc** command and natural language prompts to
    generate **documentation and comments**.

6.  Apply **responsible AI principles** — always validate, never blindly
    trust.

**Prerequisites**

**Environment**

[TABLE]

**Pre-Lab Setup Steps**

1.  **Create a new empty folder** - Lab07 in C folder

![](./media/image1.png)

2.  Open the folder in Visual Studio code.

![](./media/image2.png)

3.  Open Terminal and select Git Bash and run below command to create a
    folder

> mkdir cloudtask-api && cd cloudtask-api

![](./media/image3.png)

4.  **Initialize a Git repository:**

> git init

![](./media/image4.png)

5.  **Create a Python virtual environment:**

python -m venv venv

> source venv/Scripts/activate

![](./media/image5.png)

6.  Make sure GitHub Copilot is enabled.

##  Task 1: Understand the Problem (Human Reasoning — No Copilot Yet)

Before touching Copilot, take a moment to reason about the architecture.

Answer these questions in your OneNote/Notebook

1.  What endpoints does a Task Management API need?

    - GET /tasks — List all tasks

    - POST /tasks — Create a new task

    - GET /tasks/\<id\> — Get a single task

    - PUT /tasks/\<id\> — Update a task

    - DELETE /tasks/\<id\> — Delete a task

2.  What data fields does a "Task" have?

    - id (auto-generated)

    - title (string, required)

    - description (string, optional)

    - status (string: "pending", "in-progress", "completed")

    - created_at (datetime)

3.  What framework will you use?

    - **Flask** (lightweight, well-known)

4.  What testing framework?

    - **pytest**

Note: This task is intentionally Copilot-free. The goal is to establish
that developers must *think first*. A prompt is a request that you make
to GitHub Copilot. In addition to your prompt, Copilot uses additional
context, like the code in your current file and the chat history, to
generate a response.​ The *quality* of that context starts with the
developer's understanding of the problem.

## Task 2: Scaffold the Project Using Natural Language Comments

Now let's use Copilot to scaffold the project structure.

1.  Open Copilot Chat (Ctrl+Shift+I / Cmd+Shift+I) and type the
    following prompt.

**Prompt:**

I'm building a Task Management REST API using Python and Flask.I need a
requirements.txt file with Flask, pytest, and any essential dependencies
for a production-ready Flask REST API.

![](./media/image6.png)

2.   Copilot should suggest a requirements.txt with packages such y
    ys flask, pytest, flask-cors, and possibly python-dotenv.

> ![](./media/image7.png)

3.  Review the list. Accept packages you recognize.**Remove** any
    package you don't understand or don't need yet.Create the
    file requirements.txt and paste the accepted content or selecting
    Agent mode will create the file and add the content

> ![](./media/image7.png)

4.  Run the requirements file to install

pip install -r requirements.txt

![](./media/image8.png)

Note :  Follow good coding practices. If you aren't getting the
responses you want when you ask Copilot for suggestions or explanations
in your codebase, make sure that your existing code follows best
practices and is easy to
read.​[![](./media/image9.gif)](https://docs.github.com/en/copilot/concepts/prompting/prompt-engineering)​
Starting with a clean requirements.txt sets the foundation.

## Task 2 — Create app.py with comment-driven scaffolding

1.  Create a new file called **app.py** in the root folder. **Type** the
    following comment block **exactly** — then **pause** and let Copilot
    suggest code:

\# Task Management REST API

\# Built with Flask

\# This application provides CRUD endpoints for managing tasks

\# Each task has: id, title, description, status, created_at

\# Status can be: pending, in-progress, completed

\# Data is stored in-memory using a Python list for simplicity from
flask import Flask, jsonify, request

![](./media/image10.png)

2.   After typing the import, Copilot should begin suggesting the Flask
    app initialization (app = Flask(\_\_name\_\_)), an in-memory data
    store, and possibly the first route.

3.  Press **Tab** to accept the app = Flask(\_\_name\_\_) line.If
    Copilot suggests a full data store (e.g., tasks = \[\]), accept it.

> ![](./media/image11.png)
>
> ![](./media/image12.png)
>
> ![](./media/image13.png)

4.  **STOP here.** Do not accept any route definitions yet.

**Note:** Copilot offers dimmed ghost text suggestions as you type:
sometimes the completion of the current line, sometimes a whole new
block of code.​ You can notice that Copilot may try to generate
everything at once. The discipline here is to **accept incrementally** —
one logical block at a time. Let GitHub Copilot generate the code after
each step, rather than asking it to generate a bunch of code all at
once.

## Task 3 — Add a Helper Function via Comment Prompt

1.  Below the data store, type the following comment and wait for
    Copilot. Press Tab to accept the suggestions.

\# Helper function to find a task by its ID from the tasks list

\# Returns the task dict if found, or None if not found

![](./media/image14.png)

![](./media/image15.png)

![](./media/image16.png)

2.   Copilot should suggest a function like:

def find_task(task_id):

for task in tasks:

return next((task for task in tasks if task\['id'\] == task_id), None)

![](./media/image16.png)

3.  Review the suggestion. Does it use a for loop or next() with a
    generator? Either is acceptable. imports should always be at the top
    of the file.Move them up

> ![](./media/image17.png)

4.  If Copilot suggests both a
    loop **and** next() redundantly, **reject** and retype a cleaner
    comment:

5.  \# Find and return a task by ID using next() with a generator
    expression, return None if not found

**Note:**  If you don't get the result that you want, iterate on your
prompt and try again.​Refining the comment gives better results.

## Task 4: Build Application Features Using Intent-Driven Prompts

Now we build the core CRUD endpoints. We will alternate between **inline
comment prompts** and **Copilot Chat prompts** to demonstrate both
surfaces.

**Create Task Endpoint (Inline Comment)**

1.  In **app.py**, below the helper function, type manually below inline
    comments to create task endpoint.

\# POST /tasks - Create a new task

\# Accept JSON body with 'title' (required) and 'description' (optional)

\# Auto-generate 'id' using uuid4, set 'status' to 'pending', set
'created_at' to current UTC time

\# Return the created task with 201 status code

\# If 'title' is missing, return a 400 error with a message

![](./media/image18.png)

![](./media/image19.png)

2.   Copilot should generate a full @app.route('/tasks',
    methods=\['POST'\]) function.

3.  Review the generated code line-by-line:

    - Does it import uuid and datetime (or use them)?

    - Does it validate that title is present?

    - Does it return 201 status code?

    - If it doesn't validate title, **reject** and add: # Validate that
      'title' is present in the request body

    &nbsp;

    - Accept the function if it meets requirements.

**Note :**  Copilot may generate code using uuid4 and datetime but
may **not** add the import statements at the top of the file. **This is
intentional.** Ask learners:

4.  Add the missing imports manually:

from uuid import uuid4

from datetime import datetime, timezone

## Task 5 — Get All Tasks Endpoint (Copilot Chat)

1.  Open Copilot Chat(agent mode) and enter the below prompt

@workspace Add a GET /tasks endpoint to app.py that returns all tasks as
JSON with a 200 status code. Follow the existing code patterns in the
file.

![](./media/image20.png)

![](./media/image21.png)

2.   Copilot should generate a GET /tasks route that
    returns jsonify(tasks).

3.  Review the suggestion in the Chat panel.Click **Keep** to accept the
    change

> ![](./media/image22.png)

4.  Verify it follows the same style (decorator pattern, response
    format) as the POST endpoint.

**Note:**  Using @workspace allows Copilot to have a broader context
window to analyze your full project to generate tests.​ This applies to
feature code too — @workspace helps Copilot match existing patterns.

## Task 6 — Get Single Task Endpoint (Inline Comment)

1.  Type below comments manually in app.py:

\# GET /tasks/\<task_id\> - Get a single task by ID

\# Use the find_task helper function

\# Return 404 with error message if task not found

![](./media/image23.png)  
![](./media/image24.png)

![](./media/image25.png)

![](./media/image26.png)

2.  Let Copilot generate the function. Accept if correct.

## Task 7 : — Update Task Endpoint 

1.  Enter below prompt in Copilot chat

Write a PUT /tasks/\<task_id\> endpoint for app.py.

![](./media/image27.png)

2.  It should:

\- Accept JSON with optional fields: title, description, status

\- Only update fields that are provided in the request body

\- Validate that 'status' is one of: pending, in-progress, completed

\- Return 404 if task not found

\- Return 400 if invalid status value

\- Return the updated task with 200 status

Follow the coding patterns already in the file.

![](./media/image28.png)

3.   A complete PUT route with field-level updates and
    validation.**Critically review** the status validation logic. Does
    Copilot use an if check? A set? An enum?

- If Copilot hardcodes the valid statuses as a list inside the function,
  consider refactoring:

- VALID_STATUSES = {"pending", "in-progress", "completed"}

> ![](./media/image29.png)

4.  Move this constant to the top of the file. This
    is **your** architectural decision, not Copilot's.

**Note:** This is the "Balancing speed, ownership, and code quality"
objective in action. Start general, then get specific. When writing a
prompt for Copilot, first give Copilot a broad description of the goal
or scenario. Then list any specific requirements.​ Notice how the
detailed prompt produced a more accurate result than a vague one would.

## Task 8 — Delete Task Endpoint **(Task for students)**

1.   Write the comment-driven prompt yourself for a DELETE
    /tasks/\<task_id\> endpoint.

Requirements:

- Remove the task from the in-memory list

- Return 404 if not found

- Return 204 (No Content) on success

Write your own comment block in app.py and let Copilot generate the
code. Review before accepting.

**Hint — Example Comment**

\# DELETE /tasks/\<task_id\> - Delete a task by ID

\# Use the find_task helper to locate the task

\# Remove the task from the tasks list

\# Return 404 if task not found

\# Return 204 No Content on successful deletion

## Task 9— Add the Application Entry Point

1.  Type at the bottom of app.py. Accept Copilot's suggestion for the if
    \_\_name\_\_ == '\_\_main\_\_': block.

\# Run the Flask app in debug mode on port 5000

![](./media/image30.png)

![](./media/image31.png)

2.  Update the code at line \#50 from 200 to 201

3.  Save the file and open terminal and run the App:

cd cloudtask-api/

python app.py

![](./media/image32.png)

4.  Open a second terminal (ro duplicate workspace) and run below
    command to test

curl -X POST http://localhost:5000/tasks \\

-H "Content-Type: application/json" \\

-d '{"title": "My first task", "description": "Testing the API"}'

curl <http://localhost:5000/tasks>

![](./media/image33.png)

![](./media/image34.png)

## ![](./media/image35.png)Task 10 - Review and Refine Copilot Output — Refactoring

Now let's use Copilot to improve what we've built.

1.  Select all the code in app.py, then open Copilot Chat and enter
    below prompt

**Prompt:**

Review this Flask application for code quality improvements.

Suggest refactoring for:

1\. Extracting repeated validation logic into helper functions

2\. Consistent error response formatting

3\. Improving readability and maintainability

\- Do not change the API contract or endpoints.

![](./media/image36.png)

2.  Copilot should suggest:

- A make_error_response(message, status_code) helper

- Possibly extracting validation into a validate_task_data() function

- Consistent JSON error format like {"error": "message"}

> ![](./media/image37.png)

3.  Accept helpers that **reduce duplication**.**Reject** any suggestion
    that changes the API contract (e.g., changing route paths or HTTP
    methods).

4.  Apply changes incrementally — one refactor at a time.

## Task 11 — Use /fix for Any Issues

If you notice any linter warnings or bugs, highlight the code and type
in Copilot Chat:

**Copilot Chat Prompt:**

/fix

**Note:** The /fix command tells Copilot to analyze the selected code
for bugs and propose corrections. Always review the fix before applying
— Copilot may "fix" something that was intentional.

## Task 12: : Generate Unit Tests with Copilot

1.  Create a new file **test_app.py** in root folder. Type the following
    comment manually at the top. Pause and observe Copilot's
    suggestions. It may try to generate all tests at once.

\# Unit tests for the Task Management REST API

\# Using pytest and Flask's test client

\# Test all CRUD operations: create, read, update, delete

\# Include edge cases: missing title, invalid status, task not found

![](./media/image38.png)

![](./media/image39.png)

![](./media/image40.png)

![](./media/image41.png)

2.   Do **NOT** accept the full block. Instead, proceed with one test at
    a time.

## Task 13 — Generate Tests Using Copilot Chat /tests

1.  Open app.py, select the **Create Task** endpoint function. Then in
    Copilot Chat:

**Prompt:**

/tests Generate pytest unit tests for the selected create_task function.

Include tests for:

\- Successfully creating a task with title and description

\- Creating a task with only a title (no description)

\- Attempting to create a task without a title (expect 400)

Use Flask's test client.

![](./media/image42.png)

2.   Copilot should generate a test file with:

- A client fixture using app.test_client()

- Three test functions following the Arrange-Act-Assert pattern

The prompt file generates focused unit tests for specific functions or
methods, emphasizing practical test cases and maintainable code.​

![](./media/image43.png)

3.  Review each test:

    - Does the fixture correctly import app from app.py?

    - Are assertions checking the right status codes (201, 400)?

    - Are response bodies validated (not just status codes)?

    - Copy accepted tests into test_app.py.

![](./media/image44.png)

**Note:** Review suggestions carefully. Just like with human-generated
code, never trust any tests Copilot generates without going through your
normal review process. Review the output yourself, run it through
linters, and check the code.​

## Task 14 — Generate Remaining Tests (Exercise for you)

1.   Use Copilot Chat to generate tests for:

[TABLE]

Use prompts like:

/tests Generate pytest tests for the GET /tasks endpoint. Include a test
for an empty task list and a test after creating two tasks.

![](./media/image45.png)

2.  Run test with command **pytest test_app.py -v.** All tests pass.

![](./media/image46.png)

**If tests fail:**

- Read the error message.

- Highlight the failing test in VS Code.

- Ask Copilot Chat:

- /fix This test is failing with the following error: \[paste
  error\].The test is for the create_task endpoint. Fix the test.

- Review the fix, apply it, re-run.

**Note:** Be flexible and iterative. At the end of the day, unit tests
are code that effectively describe code. The first iteration of
generated tests, for instance, may not necessarily be exactly what
you're looking for. Don't be afraid to reframe your prompt or question.
