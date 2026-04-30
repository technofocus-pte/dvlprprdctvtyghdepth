# Lab 3 – Creating and Improving Documentation with GitHub Copilot 

### Overview

In this lab, you will focus on **documentation as a core developer
skill**, using GitHub Copilot to assist with creating, reviewing, and
improving documentation for a Java Spring Boot application.

Good documentation is essential for:

- Understanding APIs

- Onboarding new developers

- Maintaining code over time

- Reducing defects caused by incorrect assumptions

Rather than writing documentation manually from scratch, this lab
demonstrates how **GitHub Copilot can accelerate documentation tasks**
while developers remain responsible for accuracy, clarity, and
correctness.

### Objectives

By the end of this lab, you will be able to:

- Understand and document an existing REST API using GitHub Copilot

- Generate Markdown API documentation with curl examples

- Add Swagger/OpenAPI documentation to a Spring Boot application

- Document classes, methods, and test code using JavaDoc

- Create functional and technical documentation as separate artifacts

- Apply GitHub Copilot best practices when writing documentation

### Prerequisites

Before starting this lab, ensure the following:

- Visual Studio Code (or another Copilot‑supported IDE)

- GitHub Copilot extension installed and authenticated

- Java Development Kit (JDK) 17 or higher

- Apache Maven (or Maven Wrapper mvnw)

- Project successfully builds and runs (from previous labs)

## Task 1 – Understand and Document the API

Before generating documentation, you must understand **what the API
does**.  
This task reinforces API comprehension using **GitHub Copilot as a code
understanding assistant**, not a replacement for developer reasoning.

1.  Open Visual studio code and navigate to folder
    -**lab-03-documentation/java/
    src/main/java/com/example/demo/controller**,and open
    **EmployeeController.java** class

> ![](./media/image1.png)

2.  Ask Copilot to explain with the /explain and create
    api-functional.md file with below values(refer Lab 1/Lab2 for
    similar on how to prepare the md file)

> **Base URL**
>
> **Endpoint descriptions**
>
> **HTTP methods**
>
> **Sample curl commands**
>
> **Expected responses**
>
> ![](./media/image2.png)

## Task 2 – Add Swagger (OpenAPI) Documentation

Swagger (OpenAPI) provides **interactive API documentation**, making it
easier to explore and test endpoints without external tools.

This task demonstrates how Copilot assists in **framework specific
documentation setup**.

1.  Open Copilot chat and ask - Add Swagger/OpenAPI support to this
    Spring Boot project using springdoc-openapi.

![](./media/image3.png)

2.  Copilot edit the pom.xml, review the changes and click on keep to
    accept the dependency or manally add it to pom.xml and save the
    file.

![](./media/image4.png)

![](./media/image5.png)

3.  Open the Terminal and open Git Bash and run below command to
    navigate to the folder.

cd lab-03-documentation/java

![](./media/image6.png)

4.  After making changes to the poml.xml, reload the maven with the
    command

> mvn clean compile
>
> ![](./media/image7.png)
>
> ![](./media/image8.png)

5.  Now run the application - mvn spring-boot:run

![](./media/image9.png)

6.  Open the browser and enter - http://localhost:8080/swagger-ui.html

![](./media/image10.png)

Note : GitHub Copilot suggests changes but does not automatically apply
them.Always verify files such as pom.xml and accept or apply changes
explicitly.

## Task 3 – Add Code Documentation to classes with GitHub Copilot help

Code documentation explains **how the system works internally**, not
just what the API does.

In real projects:

- APIs are used by consumers

- Code is maintained by developers

- Test code explains expected behavior

This task shows how **GitHub Copilot helps generate high‑quality
JavaDoc**, while developers validate accuracy.

1.  Open the file EmployeeController.java.Select the **entire class**
    and open **GitHub Copilot Chat**

> Generate JavaDoc for this controller class and all its public methods.
>
> Explain the purpose of each endpoint, parameters, and return values.
>
> ![](./media/image11.png)

2.  Copilot Add a class‑level JavaDoc ,Add method‑level documentation
    and Describe endpoints in developer language.

> ![](./media/image12.png)

3.  Before accepting check below checklist and accept or make changes
    manually fi required.

- Does the JavaDoc match the actual endpoint?

- Are parameter names correct?

- Does it avoid claiming behavior that doesn’t exist (e.g., 404
  handling)?

> ![](./media/image12.png)

4.  Repeat above step for other classes as well

- **EmployeeService.java**

- **Employee.java**

- **EmployeeRepository.java**

> Prompt :
>
> **Add clear JavaDoc explaining the responsibility of this class and
> its methods.Keep the documentation technical and concise.**
>
> ![](./media/image13.png)
>
> ![](./media/image14.png)
>
> ![](./media/image15.png)

5.  Repeat above steps to add documentation to test class with the
    prompt

EmployeeControllerTest.java

EmployeeRepositoryTest.java

EmployeeServiceTest.java

> Generate JavaDoc for this test class.Explain what behavior is being
> validated and why.

![](./media/image16.png)

![](./media/image17.png)

![](./media/image18.png)

## Task 4 – Update API with Functional and Technical documentation structuring

Update the API documentation with the new information. Generate two
different markdown files on with the functional documentation and
another with the technical documentation.

1.  Go to the root folder and create two md file - docs/api-technical.md

![](./media/image19.png)

2.  Open api-functional.md, select the content of the file and ask
    copilot.reveiw and accept the changes

> Convert the existing API documentation into functional documentation.
>
> Focus only on endpoints, requests, responses, and usage examples.

![](./media/image20.png)

3.  Open api-technicala.md file and ask Copilot.Review the response and
    accept the changes

> Generate technical documentation describing the internal architecture
> of this project.
>
> Explain the responsibility of each layer and how components interact.

![](./media/image21.png)

## Task 5 : Testing Employee API with curl

1.  Open terminal and run below command to run the app.(use /fix and fix
    if you encounter any errors)

> mvn spring-boot:run

![](./media/image22.png)

2.  Duplicate the workspace(File-\> Duplicate Workspace) and run below
    command in Git Bash

curl -X GET <http://localhost:8080/api/employees>

![](./media/image23.png)

3.  **Run below curl command to add new Employee**

curl -X POST http://localhost:8080/api/employees -H "Content-Type:
application/json" -d '{

"name": "John",

"surname": "Doe",

"email": "john.doe@example.com"

}'

![](./media/image24.png)

4.  Run below command to get Employee by ID. Replace {id} with the
    actual employee ID.

curl -X GET http://localhost:8080/api/employees/{id}

![](./media/image25.png)

5.  Run below command to update the employee record. Replace {id} with
    the actual employee ID.

curl -X PUT http://localhost:8080/api/employees/{id} -H "Content-Type:
application/json" -d '{

"name": "Jane",

"surname": "Doe",

"email": "jane.doe@example.com"

}'

![](./media/image26.png)

6.  Run below curl command to delete employee. Replace {id} with the
    actual employee ID.

curl -X DELETE http://localhost:8080/api/employees/{id}

![](./media/image27.png)

## Summary :

In this lab, you learned how to use GitHub Copilot to efficiently
create, improve, and validate documentation for a Java Spring Boot
application. You began by understanding the REST API and generating
functional API documentation with endpoints, request and response
examples, and curl commands. You then used Copilot to add Swagger
(OpenAPI) support, enabling interactive API exploration. The lab also
focused on documenting source code using JavaDoc, including controllers,
services, models, repositories, and test classes, helping future
developers understand system behavior. You structured documentation into
functional and technical views for different audiences and validated API
behavior using curl. Throughout the lab, you experienced how GitHub
Copilot accelerates documentation tasks while developers remain
responsible for accuracy, clarity, and design decisions.
