# Lab 2- Diagnosing and fixing Java application errors using GitHub Copilot

## Overview

In this lab, you will focus on **troubleshooting, debugging, and
validating a Java Spring Boot application** with the assistance of
GitHub Copilot.  
Rather than writing new functionality, the emphasis is on
**understanding failures, identifying root causes, and applying fixes
responsibly**.

Modern developers spend a significant portion of their time debugging
broken builds, failing tests, and incorrect application behavior. This
lab demonstrates how **GitHub Copilot can act as a debugging
partner**—helping developers analyze errors, explain failures, and
propose fixes—while **developers retain decision‑making authority**.

## Objectives

By completing this lab, you will be able to:

- Analyze and document an existing Java REST API

- Use GitHub Copilot to understand compilation and runtime errors

- Troubleshoot and fix build‑time and runtime issues

- Identify and fix functional API defects

- Validate fixes using curl and automated tests

- Improve test reliability using Copilot recommendations

- Apply Copilot responsibly during debugging workflows

## Prerequisites

Before starting this lab, ensure the following are available:

- Visual Studio Code (or another Copilot‑supported IDE)

- GitHub Copilot extension (authenticated)

- Java Development Kit (JDK) 17 or higher

- Apache Maven (or Maven wrapper mvnw)

- GitHub account with Copilot access

## Task 1 – Understand the API (Reinforcement)

Before debugging or fixing anything, developers must understand **what
the application is supposed to do**. Debugging without understanding
expected behavior leads to incorrect fixes.

This task reinforces API comprehension using **GitHub Copilot as a code
understanding assistant**.

Note: Some API behaviors and documentation issues are intentionally
incorrect.

Use GitHub Copilot (/explain, /fix) and your own reasoning to identify
and correct them.

1.  Navigate to
    **lab-02-debugging-\>java/src/main/java/com/example/demo/controllers/**
    and open **EmployeeController.java**

![](./media/image1.png)

2.  Use Copilot’s /explain to understand endpoints

3.  Identify:

    - Base URL

    - Endpoints

    - Expected input/output

4.  Open Copilot chat and enter /explain .Read the response and
    understand the api to prepare md file.

![](./media/image2.png)

![](./media/image3.png)

5.  Create or update API documentation and update with Base
    URL,Endpoints and Expected input/output.Sample given for your
    reference but suggest to prepare on your own.

> **\# Employee Rest API document**
>
> **\## Base URL :**
>
> /api/employees
>
> **\## End point**
>
> **\## Get all employees**
>
> **\*\*HTTP Method:\*\*** GET
>
> **\*\*End Point:\*\*** /api/employees
>
> **\### Retrieve the list of all the employees in the system**
>
> **\# Sample curl command**
>
> curl -X GET "http://localhost:8080/api/employees
>
> **\## Expected behavious**
>
> \-   HTTP status : 200 Ok
>
> \-    Retrun JSON array of employees
>
> \[
>
>     {
>
>         "id": 1,
>
>         "name": "John",
>
>         "surname":"ton",
>
>         "email": "jont@test.com"  
>
>          }
>
> \]
>
> **\# Get employee ID**
>
> **\*\*HTTPS MEthod:\*\*** GET
>
> **\*\*End Point:\*\***  /api/employees/{id}
>
> **\## get employee details**
>
> **\### sample URL**
>
> curl -X GET "http://localhost:8080/api/employees/1"
>
> **\## Expected behaviour**
>
> \-   HTTP Status : 200 OK
>
> \-   Returns an employee object as JSON
>
> **\# Get Employee with employee email**
>
> **\*\*HTTP Method:\*\*** GET
>
> **\*\*End point:\*\*** /api/employees/email/{email}
>
> **\## Sample curl**
>
> curl -X GET
> "http://localhost:8080//api/employees/email/john.doe%40example.com
>
> **\## Expected behaviour**
>
> \-   HTTP Status : 200 OK
>
> \-   Returns an employee object as JSON
>
> **\# Create employee**
>
> **\*\*HTTP Method:\*\*** POST
>
> **\*\*End point:\*\*** /api/employees
>
> **\## sample curl command**
>
> curl -X POST "http://localhost:8080/api/employees" \\
>
> -H "Content-type:application/json" \\
>
> -d \`{
>
>     "name": "alan",
>
>     "surname": "Brown",
>
>     "email":"elanb@test.com"
>
> }\`
>
> **\## Expected behaviour**
>
> \-   HTTP Status : 200 ok
>
> \-   Returns newly created employee with generated ID
>
> **\# Update employee**
>
> **\*\*HTTP Method:\*\*** PUT
>
> **\*\*End point:\*\*** /api/employees/{id}
>
> **\## sample curl method**
>
> culr -X PUT "http:///localhost:8080/api/employees/1" \\
>
> -H "Content-type:application/json" \\
>
> -d \`{
>
>     "name": "alan",
>
>     "surname": "brown",
>
>     "email": "alanb@teststw.com"
>
> }
>
> **\## Expected behaviour**
>
> \-   HTTP Status : 200 ok
>
> \-   Returns updated employee object json
>
> **\# Delete employee**
>
> **\*\*HTTP Method:\*\*** DELETE
>
> **\*\*ENd point:\*\*** api/employees/1
>
> **\## sample curl**
>
> curl -X DELETE "http://localhost:8080/api/employees/1"
>
> **\## Expected behaviour**
>
> \-   HTTP Status : 200 ok
>
> \-   No response body
>
> **\# Get externalEmployees**
>
> **\*\*HTTP Method:\*\*** GET
>
> **\*\*End point:\*\*** /api/employees/GetExternalEmployees
>
> **\# sample curl command**
>
> curl - X GET
> "http://localhost:8080/api/employees/GetExternalEmployees"
>
> **\## Expected behaviour**
>
> \-   HTTP Status: 200 OK
>
> \-   Returns external employee list
>
> ![](./media/image4.png)

## Task 2: Debug and Solve Compile Errors Using GitHub Copilot

In real-world development, code often **fails to compile** due to syntax
errors, incorrect method signatures, mismatched annotations, or package
inconsistencies. These errors block progress completely and must be
resolved before any testing or validation can occur.

This task focuses on using **GitHub Copilot as a troubleshooting
assistant** to:

- Interpret Java and Maven compilation errors

- Identify the **root cause** of failures

- Propose **correct and minimal fixes**

- Validate fixes through a successful build

- Recognize common Java and Spring Boot compile-time errors

- Understand Maven error output

- Use GitHub Copilot (/explain and /fix) to analyze failures

- Validate Copilot’s suggestions before applying fixes

- Confirm a clean build using Maven

1.  Open terminal and navigate to the lab2 folder with below command.
    The build fails

Maven prints one or more **COMPILATION ERROR** messages .Tests do not
start running. Do not immediately try to “fix by guessing”. First,
understand the error.

> cd “lab-02-debugging\java”
>
> mvn clean test
>
> ![](./media/image5.png)
>
> ![](./media/image6.png)

2.  Select the Maven error message,open Copilot chat and enter the
    command /explain ( or you can also copy the maven error message and
    ask Copilot in chat to explain the error )

![](./media/image7.png)

3.  To fix the error, you can Copilot to fix it by entering the command
    /fix . Copilot provide fix and also give extra suggestions. Review
    the response ![](./media/image8.png)

> ![](./media/image9.png)

4.  GitHub Copilot does not automatically fix compiler errors.
    Developers must first identify the compilation issue and explicitly
    ask Copilot for assistance if needed.

5.  Open DemoApplicaiton.java file and add ; at the end of line 10 ,save
    the file and run mvn test again.Now you will see different error

> ![](./media/image10.png)

6.  Analyze the error and take Copilot help now. Enter / explain to
    understand why are you getting the error, enter /fix to fix the
    error or enter /refactor -Fixes the constructor ,Ensures
    getters/setters match field types , Cleans up redundant code and
    Keeps API intact

> /explain:
>
> ![](./media/image11.png)
>
> Run /Fix
>
> ![](./media/image12.png)

Copilot found the error and fixed the issue email field from long to
string on setEmail and getEmail methods in **Employee.java** class.You
can press keep to accept the fix . lets not accept it for now and lets
try /refactor capability for better understanding.

![](./media/image13.png)

![](./media/image14.png)

![](./media/image14.png)

7.  If you undo then Copilot revert changes back in the class file

![](./media/image15.png)

8.  Accept the fix or add **private String email;** to the **Employee**
    class and save the file

![](./media/image16.png)

9.  No run again mvn clean test. tests failed with error. Take Copilot
    help to fix

![](./media/image17.png)

10. Copilot suggest to compile with the command -mvn clean compile . run
    the command

> ![](./media/image18.png)
>
> 11.the build is successful now
>
> ![](./media/image19.png)

11. To see functional errors, run the application with the command - mvn
    spring-boot:run . application will start

![](./media/image20.png)

![](./media/image21.png)

10\. Open a browser and navigate to <http://localhost:8080> . You can
see functional error and its not a crash ,it is **missing root
endpoint**.

![](./media/image22.png)

12. Ask copilot the Whitelable error with the prompt . Copilot suggest
    valid endpoints (this is only to show how copilot helps).Review the
    response

> Why am I getting a Whitelabel Error Page when accessing
> http://localhost:8080 ?
>
> Check this Spring Boot project and explain.
>
> ![](./media/image23.png)
>
> ![](./media/image24.png)

13. Review the suggested fix and make necessary changes and save the
    file.

![](./media/image25.png)

14. Now run the application again with the command - mvn spring-boot:run

> ![](./media/image26.png)

15. Now open a browser and enter
    [http://localhost:8080/api/employees](http://localhost:8080/api/employees%20)
    . Your application is running and endpoint mapped correctly.

![](./media/image27.png)

16. Now lets validate the tests. Run **mvn clean test .**Build fail and
    lets fix issues with the hep of copilot .

![](./media/image28.png)

17. Open **EmployeeControllerTest,java** class and review the tests and
    ask copilot /fix. Copilot found bug and fix it. Review the fix

![](./media/image29.png)

18. Run test with **mvn clean test** again and take copilot help to fix
    the issues

![](./media/image29.png)

![](./media/image30.png)

19. Review the fix and keep the fix

![](./media/image31.png)

20. Now ,re-run the mvn clean test . The build is successful now and all
    tests passed.

![](./media/image32.png)

21. Run the application now with - mvn spring-boot:run . Application
    will start up and running

![](./media/image33.png)

22. On visual studio , go File -\>Duplicate workspace .

![](./media/image34.png)

23. Open Git Bash from terminal .Run below command in the terminal and
    run below commands to create a new employee

> cd “02-debugging\java”

curl -X POST http://localhost:8080/api/employees \\

-H "Content-Type: application/json" \\

-d '{

  "name": "John",

  "surname": "Doe",

  "email": "john.doe@example.com"

}'

![](./media/image35.png)

24. Add another employee record

> curl -X POST http://localhost:8080/api/employees \\
>
> -H "Content-Type: application/json" \\
>
> -d '{
>
>   "name": "Alan",
>
>   "surname": "Tom",
>
>   "email": "Alant@example.com"
>
> }'
>
> ![](./media/image36.png)

25. Run below command to get all employees

curl -X GET http://localhost:8080/api/employees

> ![](./media/image37.png)

26. Run below command to update the employee

> curl -X PUT http://localhost:8080/api/employees/{2} \\
>
> -H "Content-Type: application/json" \\
>
> -d '{
>
>   "name": "Jane",
>
>   "surname": "Doe",
>
>   "email": "jane.doe@example.com"
>
> }'
>
> ![](./media/image38.png)

27. Run below command to get employee details by id

> curl -X GET http://localhost:8080/api/employees/{2}

![](./media/image39.png)

28. Run below command to show all employees

curl -X GET http://localhost:8080/api/employees

![](./media/image40.png)

20\. Run below command to delete the employee record

curl -X DELETE http://localhost:8080/api/employees/{2}

![](./media/image41.png)

## Summary :

> In this lab, you practiced **troubleshooting and debugging a Java
> Spring Boot application** with intentionally seeded issues, focusing
> on understanding problems before applying fixes. You learned how to
> analyze API behavior, interpret Maven and Java compilation errors, and
> identify the true root causes of failures.
>
> You used **GitHub Copilot as a debugging assistant** to explain
> errors, suggest targeted fixes, and review test cases, while retaining
> full control over what changes to apply. You also learned how to
> manage Copilot’s scope, accepting only fixes relevant to the task.
>
> Finally, you validated fixes using **automated tests and curl**,
> reinforcing the importance of testing and responsible AI‑assisted
> debugging in real‑world development workflows.
