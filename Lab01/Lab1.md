# Lab1-Using GitHub Copilot to Build and Improve Automated Tests for a Java REST API

**Lab Overview**

This lab helps developers learn how to **use GitHub Copilot effectively
and responsibly** while working with an existing Java Spring Boot
application. Rather than generating code blindly, learners will practice
using Copilot to understand unfamiliar APIs, create unit tests across
multiple layers, extend functionality, and improve test quality — while
retaining full developer judgment and control.

**Lab Objectives**

By completing this lab, you will be able to:

- Analyze an existing Java REST API

- Document API endpoints using sample curl commands

- Use GitHub Copilot to create unit tests

- Write tests for repository, service, and controller layers

- Use mocks correctly for isolated unit testing

- Customize GitHub Copilot behavior using custom instructions

- Improve test coverage and test quality

**Prerequisites**

Before starting this lab, ensure the following are installed and
configured:

- Visual Studio Code (or another Copilot-supported IDE)

- GitHub Copilot extension (authenticated)

- Java Development Kit (JDK) 17 or higher

- Apache Maven

- GitHub account with Copilot access

**Lab Environment**

- Operating System: Any

- Programming Language: Java

- Framework: Spring Boot

- Build Tool: Maven

- Testing Libraries: JUnit, Mockito

## Task 1: Understand the API

Before writing any tests, developers must clearly understand **what the
application does**. In real-world projects, developers are often asked
to test or enhance code they did not write. This task focuses on using
**GitHub Copilot as a comprehension assistant** to analyze an existing
REST API, identify endpoints, and document expected behavior.

1.  Open Visual Studio code form Desktop and sign in with your GitHub
    Copilot license account

2.  Click on **File-\> Open Folder-\>C:\Labfiles** and select the
    folder - **github-copilot-workshops-labs-java**

> ![](./media/image1.png)

3.  Open the 01-testing-\> java-\>main-\>controller-\>
    EmployeeController.java api

> ![](./media/image2.png)

4.  Read the controller classes (example - @RestController
    ,@RequestMapping ,getMapping etc)) of the API and identify

    - **Base URL** (common path prefix used by all APIs in this
      controller)

    - **HTTP methods** (HTTP methods describe what you want to do with
      the resource (Employee).

    - **Request Body (**The Request Body is the data sent by the client
      to the server, usually in JSON format.)

    - **Response** (The Response is what the API sends back to the
      client.)

![](./media/image3.png)

1.  Below are the base URL, HTTP methods, Request body and response from
    EmployeeController.java api

> Base URL : **/api/employees**
>
> Http: Methods:

[TABLE]

> Retrieve all employees - public List\<Employee\> getAllEmployees()
>
> Create new employee(PostMapping)- public Employee
> createEmployee(@RequestBody Employee employee)
>
> **Request body :**
>
> public Employee createEmployee(@RequestBody Employee employee)
> –(PostMapping)
>
> public Employee updateEmployee(@PathVariable Long id,@RequestBody
> Employee employee) –( @PutMapping("/{id}")

Response by Endpoint :

Get all employees - public List\<Employee\>
getAllEmployees()—(@GetMapping)

**Response JSON**

\[

  {

    "id": 1,

    "name": "John",

    "surname": "Doe",

    "email": "john.doe@example.com"

  },

  {

    "id": 2,

    "name": "Jane",

    "surname": "Smith",

    "email": "jane.smith@example.com"

  }

\]

Get employee ID:

@GetMapping("/{id}")

public Employee getEmployeeById(@PathVariable Long id

Response JSON

{

  "id": 1,

  "name": "John",

  "surname": "Doe",

  "email": "john.doe@example.com"

}

**Create employee:**

@PostMapping

public Employee createEmployee(@RequestBody Employee employee)

Response :

{

  "id": 3,

  "name": "Alice",

  "surname": "Brown",

  "email": "alice.brown@example.com"

}

**Update employee:**

@PutMapping("/{id}")

public Employee updateEmployee(...)

**Response** :

{

  "id": 1,

  "name": "Updated Name",

  "surname": "Updated Surname",

  "email": "updated@email.com"

}

**Delete employee:**

@DeleteMapping("/{id}")

public void deleteEmployee(@PathVariable Long id)

**Response** :

No body

HTTP Status: **200 OK** (or normally **204 No Content**)

[TABLE]

5.  Select the entire EmployeeController.java file . Open **Copilot
    Chat** .Paste the following prompt:

> Explain this Spring Boot REST controller.
>
> Identify:
>
> \- Base URL
>
> \- All endpoints
>
> \- HTTP methods
>
> \- Request bodies
>
> \- Response payloads
>
> Explain it as if I am preparing to write tests.
>
> ![](./media/image4.png)

6.  You can see response similar to below response .Read the Copilot
    response and evaluate it.

> Here's a full breakdown of
> the [EmployeeController](vscode-file://vscode-app/c:/Users/ManjulaChintharla.AzureAD/AppData/Local/Programs/Microsoft%20VS%20Code/560a9dba96/resources/app/out/vs/code/electron-browser/workbench/workbench.html),
> oriented toward test writing:
>
> **Base URL**
>
> **Employee Model (Request/Response shape)**
>
> All endpoints consume and
> produce [Employee](vscode-file://vscode-app/c:/Users/ManjulaChintharla.AzureAD/AppData/Local/Programs/Microsoft%20VS%20Code/560a9dba96/resources/app/out/vs/code/electron-browser/workbench/workbench.html) objects
> serialized as JSON:

[TABLE]

> **Endpoints**
>
> **1. Get All Employees**

[TABLE]

> **Test cases to consider:** empty list, populated list, response
> shape.
>
> **2. Get Employee by ID**

[TABLE]

> **Test cases to consider:** valid ID, non-existent ID (currently
> returns null body with 200), invalid type (e.g. string ID).
>
> **3. Create Employee**

[TABLE]

> **Example request body:**
>
> **Test cases to consider:** valid payload, missing required fields,
> duplicate email (if constrained).
>
> **4. Update Employee**

[TABLE]

> **Fields
> updated:** [name](vscode-file://vscode-app/c:/Users/ManjulaChintharla.AzureAD/AppData/Local/Programs/Microsoft%20VS%20Code/560a9dba96/resources/app/out/vs/code/electron-browser/workbench/workbench.html), [surname](vscode-file://vscode-app/c:/Users/ManjulaChintharla.AzureAD/AppData/Local/Programs/Microsoft%20VS%20Code/560a9dba96/resources/app/out/vs/code/electron-browser/workbench/workbench.html), [email](vscode-file://vscode-app/c:/Users/ManjulaChintharla.AzureAD/AppData/Local/Programs/Microsoft%20VS%20Code/560a9dba96/resources/app/out/vs/code/electron-browser/workbench/workbench.html) —
> the [id](vscode-file://vscode-app/c:/Users/ManjulaChintharla.AzureAD/AppData/Local/Programs/Microsoft%20VS%20Code/560a9dba96/resources/app/out/vs/code/electron-browser/workbench/workbench.html) in
> the body is ignored; the
> path [id](vscode-file://vscode-app/c:/Users/ManjulaChintharla.AzureAD/AppData/Local/Programs/Microsoft%20VS%20Code/560a9dba96/resources/app/out/vs/code/electron-browser/workbench/workbench.html) drives
> the lookup.
>
> **Test cases to consider:** valid update, updating non-existent ID
> (returns null + 200 — a gap), partial fields.
>
> **5. Delete Employee**

[TABLE]

> **Test cases to consider:** delete existing, delete non-existent ID
> (no error currently thrown).
>
> **Key Testing Notes**

- **No HTTP status customization** — every endpoint
  returns 200 regardless of outcome (including not-found cases). Your
  tests should assert both the status code and the response body.

- **No validation annotations** (@NotNull, @Valid)
  on [Employee](vscode-file://vscode-app/c:/Users/ManjulaChintharla.AzureAD/AppData/Local/Programs/Microsoft%20VS%20Code/560a9dba96/resources/app/out/vs/code/electron-browser/workbench/workbench.html) —
  invalid/empty payloads will not be rejected at the controller level.

- **null returns on
  not-found** — [getEmployeeById](vscode-file://vscode-app/c:/Users/ManjulaChintharla.AzureAD/AppData/Local/Programs/Microsoft%20VS%20Code/560a9dba96/resources/app/out/vs/code/electron-browser/workbench/workbench.html) and [updateEmployee](vscode-file://vscode-app/c:/Users/ManjulaChintharla.AzureAD/AppData/Local/Programs/Microsoft%20VS%20Code/560a9dba96/resources/app/out/vs/code/electron-browser/workbench/workbench.html) return null instead
  of a 404. This is a behavioral gap worth covering with negative-path
  tests.

- Good candidates for
  mocking: [EmployeeService](vscode-file://vscode-app/c:/Users/ManjulaChintharla.AzureAD/AppData/Local/Programs/Microsoft%20VS%20Code/560a9dba96/resources/app/out/vs/code/electron-browser/workbench/workbench.html) (via @MockBean in
  Spring Boot tests, or Mockito in unit tests).

> ![](./media/image5.png)

2.  Select the controller class.Open **Copilot Chat and ask**

3.  For each API endpoint:

    - Create a sample curl command

    - Describe expected behavior

![](./media/image6.png)

![](./media/image7.png)

Document “Create Employee”

![](./media/image8.png)

Delete records

![](./media/image9.png)

**Deliverable**

Create a Markdown file (for example, api-docs.md) containing:

- Endpoint descriptions

- Sample curl requests

- Expected responses

Explain this API. What endpoints does it expose, what are the request
and response formats?

![](./media/image10.png)

## Task 2: Create Repository Layer Unit Tests

The repository layer is responsible for data persistence. This task
focuses on testing data access logic in isolation, without involving
business logic or REST endpoints.

Write unit tests for the repository layer.

1.  Navigate to src/test/java/com/example/demo and
    **EmployeeRepositoryTest.java** file

![](./media/image11.png)

![](./media/image12.png)

2.  At the top of EmployeeRepositoryTest.java, below comment manually.
    Stop typing and wait,Copilot will start suggesting:

- @DataJpaTest

- Autowired repository

- Sample save and find tests

// Write JUnit tests for EmployeeRepository using @DataJpaTest.

![](./media/image13.png)5. Open chat and enter below prompt

> Create JUnit 5 tests for EmployeeRepository.
>
> Requirements:
>
> \- Use @DataJpaTest
>
> \- Test basic CRUD operations (save, findAll, findById, delete)
>
> \- Use an in-memory database
>
> \- Follow Spring Boot testing best practices

![](./media/image14.png)

3.  Copilot generate and update your test file .

![](./media/image15.png)

4.  Review unit tests and click on **Keep** to accept tests.

![](./media/image16.png)

![](./media/image17.png)

5.  Run test

mvn test

![](./media/image18.png)

![](./media/image19.png)

## Task 3: Create Service Layer Unit Tests

The service layer contains business logic and coordinates interactions
with the repository. This task teaches how to test logic independently
of infrastructure by mocking dependencies.

1.  Navigate to **src/test/java/com/example/demo** and create file with
    the name **EmployeeServiceTest.java**

> ![](./media/image20.png)

2.  Open GitHub Copilot chat and enter the below prompt

> Create unit tests for EmployeeService.
>
> Requirements:
>
> \- Use Mockito
>
> \- Mock EmployeeRepository
>
> \- Use @Mock and @InjectMocks
>
> \- Include positive and negative scenarios
>
> \- Follow JUnit 5 best practices
>
> ![](./media/image21.png)
>
> ![](./media/image22.png)

3.  Review and validate the generated tests and click on **Keep**

> When GitHub Copilot generates unit tests:
>
> \- Always verify package declarations
>
> \- Always verify import statements
>
> \- Ensure imported classes exist in src/main/java
>
> \- Fix any mismatches before running tests
>
> Copilot suggestions must be reviewed before execution.

![](./media/image23.png)

![](./media/image24.png)

4.  Open the terminal and navigate to the path suggested byb Copilot and
    run **mvn test**

> ![](./media/image25.png)
>
> ![](./media/image26.png)

## Task 4 : Create Controller Layer Unit Tests

**The controller layer exposes the REST API. This task focuses on
verifying HTTP behavior without starting the full application.**

Test the REST API endpoints without starting the full application.

**Tasks**

1.  Navigate to src/test/java/com/example/demo and create a test class
    named **EmployeeControllerTest.java**

![](./media/image27.png)

2.  Open Copilot and enter below prompt

> Create unit tests for EmployeeController.
>
> Use:
>
> \- @WebMvcTest
>
> \- MockMvc
>
> \- Mock EmployeeService
>
> Test:
>
> \- GET /api/employees
>
> \- GET /api/employees/{id}
>
> \- POST /api/employees
>
> Validate:
>
> \- HTTP status codes
>
> \- JSON response content

![](./media/image28.png)

3.  Review the tests and click on Keep to add the controller class.

![](./media/image29.png)

4.  When GitHub Copilot generates controller tests:

> 1\. Verify the test's \`package\` declaration
>
> 2\. It MUST match the main application package
>
> For example:
>
> \- Main application: \`com.example.demo\`
>
> \- Test class MUST also be in: \`com.example.demo\`
>
> If packages do not match, Spring Boot will fail to locate
>
> \`@SpringBootApplication\` and tests will not start.

![](./media/image30.png)

5.  Run below command to test – **mvn test** ( it will fail if package
    deflation is not matching)

![](./media/image31.png)

6.  Select the test class and ask copilot to fix the error - **/fix**

![](./media/image32.png)

7.  GitHub Copilot may suggest improvements beyond fixing test failures,

> such as recommending better REST semantics (e.g., returning 404
> instead of 200).These suggestions are advisory.Only apply them if the
> lab explicitly asks for API refactoring.
>
> Developers must decide scope and intent.

![](./media/image33.png)

8.  Change the package from **package com.example;** to **package
    com.example.demo;** and then run **mvn test**

> ![](./media/image34.png)

## Task 5 : Add a New API Operation

**This task simulates a real development scenario: extending an existing
application with a new feature. You will add a new operation to find an
employee by email and ensure it is properly tested at every layer.**

Add a new feature to the API:**Find employee by email**

1.  Open Copilot chat and enter below prompt

![](./media/image35.png)

2.  Review the response and accept by clicking on keep

![](./media/image36.png)

3.  **Review and accept the test also**

![](./media/image37.png)

4.  **Review and accept the code changes to repository and service
    classes**

![](./media/image38.png)

![](./media/image39.png)

5.  Now run the command mvn clean test to clean the build

![](./media/image40.png)

![](./media/image41.png)

## Summary :

In this lab, you explored how GitHub Copilot can significantly improve
developer productivity — **when used thoughtfully and responsibly**.

You learned how to:

- Use Copilot to understand and document existing APIs

- Write unit tests for repository, service, and controller layers

- Use mocking to isolate logic and avoid brittle tests

- Extend an application with new functionality using Copilot assistance

- Configure Copilot behavior to match team standards

- Analyze and improve test quality and coverage

- Troubleshoot errors and validate Copilot-generated code using
  developer judgment
