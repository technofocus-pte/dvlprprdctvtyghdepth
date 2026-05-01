# Lab 5: Exploring GitHub Copilot modes — Autocomplete, Chat, Agent, and Custom Prompts

In this lab, you will learn how to use **GitHub Copilot** effectively by
working through a series of hands‑on challenges. You will practice using
different Copilot modes and features to:

- Write code using inline suggestions (Autocomplete)

- Ask questions and debug code using Copilot Chat

- Perform multi‑file changes using Copilot Agent mode

- Create reusable prompt files for explaining code, reviewing code, and
  generating tests

This lab focuses on **developer productivity** and **learning how to
guide Copilot**, not just accepting suggestions

### Prerequisites

Before starting this lab, make sure you have:

- Visual Studio Code installed

- GitHub Copilot extension installed and signed in

- Java 17 or higher installed

- Maven installed

- Basic understanding of Java and Spring Boot concepts

## Task 1 : Basic Copilot Autocomplete

Learn how Copilot generates code using **comments as intent**.

1.  Navigate to lab lab-05-agents\java/src\main\java\com\example\demo
    and open the file **EmployeeController.java.**

![](./media/image1.png)

2.  Scroll to an empty line inside the class. ype the following comment.
    Press **Enter** and pause for a moment.

//Method to check if a number is prime

![](./media/image2.png)

3.  Observe Copilot’s inline suggestion. Press **Tab** to accept the
    suggestion.

![](./media/image3.png)

4.  Try another function, for instance.Accept the suggested method. Edit
    the generated code and see if more relevant suggestions appear.

// Method to compute factorial

![](./media/image4.png)

## Task 2 : Copilot Chat

Use Copilot Chat to ask questions, learn concepts, and debug issues.

1.  Open Copilot Chat (you can also press Ctrl+Shift+' in VS Code).

2.  Ask(Select ask mode) the following questions **one by one** and read
    the responses carefully. Copilot should provide clear explanations
    and example code.

How can I inject a service into this controller?

Why am I getting a NullPointerException on line 25?

Show me how to read a file line by line in Java.

![](./media/image5.png)

![](./media/image6.png)

![](./media/image7.png)

5.  Open EmployeeService.java and **set an object to null** and then try
    to use it.  
    This causes a **NullPointerException**, a very common Java runtime
    error.

Modify getAllEmployees() method and save the file

( we are introducing bug and checking how copilot helps us to fix this )

public List\<Employee\> getAllEmployees() {

    EmployeeRepository repo = null;   // intentionally introduced bug

    return repo.findAll();             // this will cause
NullPointerException

}

![](./media/image8.png)

Note : We created a variable (repo) and explicitly set it to null .When
we try to call a method on it then this will fail

6.  Open terminal and run the app with below commands. Application will
    run

> cd “lab-05-agents\java\\ “
>
> mvn spring-boot:run
>
> ![](./media/image9.png)

7.  Open a browser and navigate to [http://localhost:8080/api/employees
    .you](http://localhost:8080/api/employees%20.you) can see a **500
    Internal Server Error** in the browser

> ![](./media/image10.png)

8.  Switch back to terminal and **stack trace** in the terminal contains
    NullPointerException

> ![](./media/image11.png)

9.  Select the error message in the terminal or select the buggy method
    in EmployeeService.java and open Copilot Chat , enter /explain

![](./media/image12.png)

10. Copilot explains ,what a NullPointerException is,Which object is
    null, why repo.findAll() fails and where the issue originates

11. Select the agent mode and ask Copilot to fix it with the command
    /terminalfix

![](./media/image13.png)

12. Review and accept the fix and then re-run the application to check
    the fix

> mvn spring-boot:run
>
> ![](./media/image14.png)

13. Open a browser and navigate to <http://localhost:8080/api/employees>

> ![](./media/image15.png)

## Task 3 : Copilot Agent Mode

1.  Open the Command Palette: Ctrl + Shift + P .Search for **“Copilot
    Agent”** and activate it.(You can select Agent mode also)

![](./media/image16.png)

![](./media/image17.png)

2.  Assign a high‑level task, for example. Review the **plan** proposed
    by the Agent.

> Approve the plan to let Copilot apply changes across files.
>
> *Refactor all controllers to use constructor dependency injection.*
>
> ![](./media/image18.png)

3.  Ask follow‑up questions such as:

> *Explain why this refactor was necessary.*
>
> ![](./media/image19.png)

4.  Try another Agent task:

> *Generate unit tests for all public methods in the service package.*
>
> ![](./media/image20.png)

## Task 4: Custom Prompt Files/Instructions 

Learn how to create reusable Markdown files (\*.prompt.md) that define
prompts for repetitive tasks such as explaining code, generating tests,
or reviewing PRs. These files are typically stored
in .github/prompts/ within your repository. In VS Code, you can open
Copilot Chat and execute the slash command /\<prompt-name\> that matches
the file name (without extension). Prompts can request inputs
using ${input:key:label} and run in agent mode.

1.  Navigate to lab-05-agents/java/github and create a folder
    **prompts**

![](./media/image21.png)

2.  Create a new file with the name **explain-java.prompt.md** and paste
    the below prompt

---

mode: 'agent'

description: 'Explain a Java method in a simple and structured way'

---

Please explain the following Java code clearly for the selected
audience.

☕ \*\*Java code to explain\*\*:

${input:code:Paste the Java code here}

🎯 \*\*Target audience\*\*:

${input:audience:Who is this for? (beginner/intermediate/advanced)}

Your explanation must include:

\- A short summary of what the code does

\- A step-by-step breakdown

\- Explanation of key Java concepts involved

\- One simple usage example

\- Common pitfalls or edge cases

![](./media/image22.png)

3.  Open Copilot Chat and type - /explain-java . Copilot recognize it as
    a custom prompt

![](./media/image23.png)

4.  Paste this example code when asked and Set the audience to
    "beginner".

> public int fibonacci(int n) {
>
> return n \<= 1 ? n : fibonacci(n-1) + fibonacci(n-2);
>
> }

![](./media/image24.png)

5.  Create another file name **review-java.prompt.md** and paste the
    below content

---

mode: 'agent'

description: 'Perform a structured code review for Java code'

---

Perform a technical review of the following Java code.

☕ \*\*Code to review\*\*:

${input:code:Paste your Java code here}

⚖️ \*\*Focus areas\*\*:

${input:criteria:readability, performance, security, maintainability,
testing}

Please respond with:

\- Findings grouped by each selected area

\- Potential risks and how to fix them

\- Recommended refactors or Java best practice improvements

\- Quick wins with priority levels (high/medium/low)

![](./media/image25.png)

6.  Go back to Copilot Chat and run: /review-java

![](./media/image26.png)

![](./media/image27.png)

7.  Paste this code snippet (which has a common Java issue). Copilot
    should flag potential issues like modifying the input parameter and
    suggest best practices.

public List\<Integer\> addItems(List\<Integer\> items) {

if (items == null) {

items = new ArrayList\<\>();

}

for (int i = 0; i \< 10; i++) {

items.add(i);

}

return items;

}

![](./media/image28.png)

8.  Create a file called .github/prompts/generate-tests-java.prompt.md
    and paste the below content

> ---
>
> description: 'Generate JUnit unit tests for a given Java method'
>
> ---
>
> Write a JUnit test suite for the following Java code.
>
> 🧩 \*\*Code under test\*\*:
>
> ${input:code:Paste the Java method or class here}
>
> 🧠 \*\*Test strategy\*\*:
>
> ${input:matrix:Describe the edge cases, invalid inputs, and expected
> failures}
>
> Requirements:
>
> \- Use JUnit 5 conventions
>
> \- Cover success, edge, and failure scenarios
>
> \- Use clear and descriptive test names
>
> \- Include minimal setup and teardown (@BeforeEach, @AfterEach if
> needed)
>
> \- Highlight any missing test coverage areas
>
> ![](./media/image29.png)

9.  Run in Copilot Chat: **/generate-tests-java**

![](./media/image30.png)

10. Paste the method as

> public double divide(double a, double b) {
>
> return a / b;
>
> }
>
> ![](./media/image31.png)

11. Enter test matrix – zero division, negative numbers, large numbers

![](./media/image32.png)

12. Create a **DivideTest.java in src/test/java** and save the above
    results

![](./media/image33.png)

13. Open terminal and run tests with below commabds

cd lab-05-agents\java

mvn test

> ![](./media/image34.png)

14. If you see any compilations error ask Copilot to fix with
    /terminalfix command.Review and accept the fix

> ![](./media/image35.png)

15. Save the file and run test again mvn test.Test run successfully.

![](./media/image36.png)

## Summary :

> This lab explores GitHub Copilot modes—Autocomplete, Chat, Agent, and
> Custom Prompts—to boost developer productivity. Learners practice
> writing code, debugging, performing multi-file refactors, and creating
> reusable prompt files. The focus is on guiding Copilot effectively,
> not blindly accepting suggestions.
