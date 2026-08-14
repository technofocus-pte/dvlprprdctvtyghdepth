---
lab:
  title: Lab1-Using GitHub Copilot to Build and Improve Automated Tests for a Java REST API
  description: In this lab, you explored how GitHub Copilot can significantly improve developer productivity — when used thoughtfully and responsibly.
  duration: 112 minutes
  level: 100
  islab: true
  primarytopics:
    - GitHub
---

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

- Analyse an existing Java REST API

- Document API endpoints using sample curl commands

- Use GitHub Copilot to create unit tests

- Write tests for repository, service, and controller layers

- Use mocks correctly for isolated unit testing

- Customise GitHub Copilot behaviour using custom instructions

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

## Task 0: Activate and Configure GitHub Copilot Subscription

Firstly, you will activate GitHub Copilot and configure it within
Visual Studio Code to enable AI-assisted software development. You will
sign in with your GitHub account, authenticate your access, and install
the GitHub Copilot Chat extension in VS Code. This setup prepares your
development environment for upcoming labs focused on code generation,
productivity, security, and DevOps automation using GitHub Copilot.

**If you still do not have an active Copilot license, a 30-day trial can
be requested with the following steps. Make sure to cancel your license
before the trial ends to avoid getting billed**.

**Note:** This task is intended only for users who have not yet activated
or configured GitHub Copilot. If your setup is already complete, you may
skip these steps.

1.  Open a new tab in your browser and go to Signup to GitHub Copilot - +++https://github.com/github-copilot/signup+++

2.  Click on the "**Get access to GitHub Copilot”** button. 

   ![Screenshot](./media/image42.png)

3.  Enter billing information with your personal credit card and
    then click on **Save**. 

   ![Screenshot](./media/image43.png)

4.  Enter your billing information and then click on the **Save payment
    information** button. 

   ![Screenshot](./media/image44.png)
  
   **IMPORTANT:** Make sure to deactivate the account after you complete
   the labs to avoid billing for usage. 

5.  Open Visual Studio Code from the Windows Start menu.
    Click on **Accounts > Backup and Sync Settings** and select **Sign in.** 

   ![Screenshot](./media/image45.png)

6.  Select the **Sign in with GitHub** option.

   ![Screenshot](./media/image46.png)

7.  Select the Browser and Sign in with your Copilot
    enabled Github account. 

   ![Screenshot](./media/image47.png)
  
   ![Screenshot](./media/image48.png)

8.  Authenticate and verify with the code to
    complete Two-factor authentication. 

   ![Screenshot](./media/image49.png)

9.  Click on Visual Studio Code. 

   ![Screenshot](./media/image50.png)

10. Click on Extension from the left navigation menu, search
    for +++GitHub Copilot+++ chat, select it and click on **Install**. 

   ![Screenshot](./media/image51.png)

## Task 1: Understand the API
Before writing any tests, developers must clearly understand **what the application does**. In real-world projects, developers are often asked
to test or enhance code they did not write. This task focuses on using  **GitHub Copilot as a comprehension assistant** to analyze an existing
REST API, identify endpoints, and document expected behavior.

1.  Open Visual Studio Code from the desktop and sign in with your GitHub Copilot license account.Navigate to C:\Labfile and extract the github-copilot-workshops-labs-java file

2.  Click on **File-\> Open Folder-\>C:\Labfiles** and select the  folder - **github-copilot-workshops-labs-java** 

     ![](./media/image1.png)

3.  Open the 01-testing-\> java-\>src->main-\>controller-\> EmployeeController.java api

     ![](./media/image2.png)

4.  Read the controller classes (example - @RestController
    ,@RequestMapping, getMapping, etc.) of the API and identify

    - **Base URL** (common path prefix used by all APIs in this
      controller)

    - **HTTP methods** (HTTP methods describe what you want to do with
      the resource (Employee).

    - **Request Body** (The Request Body is the data sent by the client
      to the server, usually in JSON format.)

    - **Response** (The Response is what the API sends back to the
      client.)

    ![](./media/image3.png)

5.  Below are the base URL, HTTP methods, Request body and response from
    EmployeeController.java api

    Base URL : **/api/employees**

    Http: Methods:

    
    
    |**HTTP Method**|	**Purpose**|	**Endpoint**|
    |--|--|--|
    |GET|Retrieve data|/api/employees|
    |GET|Retrieve one record|/api/employees/{id}|
    |POST|Create new record|/api/employees|
    |PUT|Update existing record|/api/employees/{id}|
    |DELETE|Delete a record|/api/employees/{id}|

    Retrieve all employees - public List<Employee> getAllEmployees()
    Create new employee(PostMapping)- public Employee createEmployee(@RequestBody Employee employee)
    - **Request body:**
    public Employee createEmployee(@RequestBody Employee employee) –(PostMapping)
    public Employee updateEmployee(@PathVariable Long id,@RequestBody Employee employee) –( @PutMapping("/{id}")
    - **Response by Endpoint:**
    Get all employees - public List<Employee> getAllEmployees()—(@GetMapping)


7.  Select the entire EmployeeController.java file . Open **Copilot Chat in Ask mode with Claude sonnet 4.5 model selected** .Paste the following prompt:

    ```
    Explain this Spring Boot REST controller
    Identify:
    - Base URL
    - All endpoints
    - HTTP methods
    - Request bodies
    - Response payloads
    Explain it as if I am preparing to write tests.
    ```
    ![](./media/image4.png)

6.  You can see response similar to below response .Read the Copilot
    response and evaluate it.
    Here's a full breakdown of the EmployeeController, oriented toward test writing:
   - **Base URL**
    Employee Model (Request/Response shape)
    All endpoints consume and produce Employee objects serialized as JSON:
    
    |**Field**|**Type**|**Notes**|
    |--|--|--|
    |id|Long|Auto-generated (DB identity), not sent on create|
    |name|string|Required for meaningful data|
    |Surname|String|Required for meaningful data|
    |email|String|Required for meaningful data|
    **Endpoints**
  - **Get All Employees**
    
    |**Method**|**GET**|
    |--|--|
    |URL|/api/employees|
    |Request body|None|
    |Response|200 OK + JSON array of Employee objects (empty array [] if none exist)|
    - Similarllly prepare for all other endpoints as shown in image


     ![](./media/image5.png)

2.  Select the controller class.Open **Copilot Chat and ask**

3.  For each API endpoint:

    - Create a sample curl command

    - Describe expected behavior

    ![](./media/image6.png)

    ![](./media/image7.png)

- Document “Create Employee”

    ![](./media/image8.png)

    - Delete records

    ![](./media/image9.png)

    - **Deliverable**

    Create a Markdown file (for example, api-docs.md) containing:
    
    - Endpoint descriptions
    
    - Sample curl requests
    
    - Expected responses
    
    Explain this API. What endpoints does it expose, what are the request
    and response formats?

    ![](./media/image10.png)

## Task 2: Create Repository Layer Unit Tests

The repository layer is responsible for data persistence. This task focuses on testing data access logic in isolation, without involving
business logic or REST endpoints.

Write unit tests for the repository layer.

1.  Navigate to src/test/java/com/example/demo and create file +++EmployeeRepositoryTest.java+++ file

    ![](./media/image11.png)
    
    ![](./media/image12.png)

2.  At the top of **EmployeeRepositoryTest.java**, below comment manually.
    Stop typing and wait,Copilot will start suggesting:

    - @DataJpaTest
    
    - Autowired repository
    
    - Sample save and find tests
      
    ```
    // Write JUnit tests for EmployeeRepository using @DataJpaTest.
    ```
    ![](./media/image13.png)

5. Open chat,select agent mode and Claude sonnet 4.5 model and then enter below prompt

    ```
    Create JUnit 5 tests for EmployeeRepository
    Requirements:
    - Use @DataJpaTest
    - Test basic CRUD operations (save, findAll, findById, delete)
    - Use an in-memory database
    - Follow Spring Boot testing best practices
    ```

    ![](./media/image14.png)

3.  Copilot generate and update your test file .

    ![](./media/image15.png)

4.  Review unit tests and click on **Keep** to accept tests.

    ![](./media/image16.png)
    
    ![](./media/image17.png)

5.  Open Terminal->Git Bash and run below commands
   +++cd "github-copilot-workshops-labs-java/lab-01-testing/java"+++
   +++export MAVEN_HOME="/c/Users/Admin/Documents/maven-mvnd-1.0.5-windows-amd64/maven-mvnd-1.0.5-windows-amd64"+++
   +++export PATH="$MAVEN_HOME/bin:$PATH"+++
7.  Run test with mvn command

    +++mvn test+++

    ![](./media/image18.png)
    
    ![](./media/image19.png)

## Task 3: Create Service Layer Unit Tests

The service layer contains business logic and coordinates interactions
with the repository. This task teaches how to test logic independently
of infrastructure by mocking dependencies.

1.  Navigate to **src/test/java/com/example/demo** and create file with
    the name +++EmployeeServiceTest.java+++ and enter the below prompt in Copilot chat Agent mode.
    
    ```
    Create unit tests for EmployeeService
     Requirements:
     - Use Mockito
     - Mock EmployeeRepository
     - Use @Mock and @InjectMocks
    - Include positive and negative scenarios
     - Follow JUnit 5 best practices
    ```

    ![](./media/image20.png)

    ![](./media/image22.png)

2.  Review and validate the generated tests and click on **Keep**

    ```
    When GitHub Copilot generates unit tests
    - Always verify package declarations
    - Always verify import statements
    - Ensure imported classes exist in src/main/java
    - Fix any mismatches before running tests
    ```
    Copilot suggestions must be reviewed before execution.
    ![](./media/image23.png)

    ![](./media/image24.png)

4.  Open the terminal and navigate to the path suggested byb Copilot and
    run +++mvn test+++

     ![](./media/image25.png)

     ![](./media/image26.png)

## Task 4 : Create Controller Layer Unit Tests

**The controller layer exposes the REST API. This task focuses on
verifying HTTP behavior without starting the full application.**

Test the REST API endpoints without starting the full application.


1.  Navigate to src/test/java/com/example/demo and create a test class
    named +++EmployeeControllerTest.java+++

    ![](./media/image27.png)

2.  Open Copilot and enter below prompt

       ```
       Create unit tests for EmployeeController
       Use:
       - @WebMvcTest
       - MockMvc
       - Mock EmployeeService
       Test:
       - GET /api/employees
       - GET /api/employees/{id}
       - POST /api/employees
       Validate:
       - HTTP status codes
       - JSON response content
       ```

    ![](./media/image28.png)

3.  Review the tests and click on Keep to add the controller class.

    ![](./media/image29.png)

4.  When GitHub Copilot generates controller tests:

    1. Verify the test's `package` declaration
    2. It MUST match the main application package

    For example:
    - Main application: `com.example.demo`
    - Test class MUST also be in: `com.example.demo`

    If packages do not match, Spring Boot will fail to locate
    `@SpringBootApplication` and tests will not start.


    ![](./media/image30.png)

5.  Run below command to test – +++mvn test+++ ( it will fail if package
    deflation is not matching)

    ![](./media/image31.png)

6.  Select the test class and ask copilot in Agent mode to fix the error with the command  - +++/fix+++

    ![](./media/image32.png)

7.  GitHub Copilot may suggest improvements beyond fixing test failures,

     such as recommending better REST semantics (e.g., returning 404
     instead of 200).These suggestions are advisory.Only apply them if the
     lab explicitly asks for API refactoring.

     Developers must decide scope and intent.

    ![](./media/image33.png)

8.  Change the package from **package com.example;** to +++package com.example.demo;+++ and then run +++mvn test+++

     ![](./media/image34.png)

## Task 5 : Add a New API Operation

**This task simulates a real development scenario: extending an existing
application with a new feature. You will add a new operation to find an
employee by email and ensure it is properly tested at every layer.**

Add a new feature to the API:**Find employee by email**

1.  Open Copilot chat and enter below prompt
   ```
    Adda new features to find an employee by email
    Requirements:
    -	Add a repository method to find employee by email
    -	Add a corresponding service method
    -	Add a REST endpoint to fetch employee by email
    -	Generate unit tests for repository service and controller layers
    -	Follow existing coding style
    ```

    ![](./media/image35.png)

3.  Review the response and accept by clicking on keep

    ![](./media/image36.png)

4.  **Review and accept the tests**

    ![](./media/image37.png)

5.  Review and accept the code changes to repository and service classes

    ![](./media/image38.png)

    ![](./media/image39.png)

6.  Now run the command +++mvn clean test+++ to clean the build

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
