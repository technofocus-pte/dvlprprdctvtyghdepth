---
lab:
  title: Lab 12 — Building a Flask customer health dashboard using GitHub Copilot (Optional)
  description: 'By completing this lab, you will learn how to:'
  duration: 94 minutes
  level: 100
  islab: true
  primarytopics:
    - GitHub
---

# Lab 12 — Building a Flask customer health dashboard using GitHub Copilot (Optional)

### Scenario 

You are a developer on a Customer Success team. Your product manager has
requested a quick internal prototype: a **Customer Health
Dashboard** that displays 10 mock customer accounts with their health
scores and risk levels. The dashboard must be a simple, server-rendered
Flask app with Bootstrap 5 styling — no database, no REST API, no
frontend framework. Your goal is to use **GitHub Copilot** to scaffold
and build this prototype as fast as possible while maintaining code
quality and ownership.

### Objectives

By completing this lab, you will learn how to:

1.  Use **intent-driven prompts** (comments and chat) to scaffold a
    Flask application with Copilot

2.  Generate structured **mock data modules** using Copilot inline
    suggestions

3.  Prompt Copilot to produce **server-rendered HTML templates** with
    Bootstrap 5

4.  Evaluate and **refine Copilot-generated code** before running it

5.  Balance **speed, ownership, and code quality** when accepting AI
    suggestions

## Task 1: Understand the Problem (Human Reasoning)

1.  Create a new project folder Lab12 in your C:/drive and open it in
    Visual Studio code .Open Terminal->GitBash and run below commands

    +++mkdir customer-health-dashboard && cd customer-health-dashboard+++
    
    +++python -m venv venv+++
    
    +++source venv/Scripts/activate+++
    
    +++pip install flask++
    
    ![](./media/image1.png)

2.  Before touching any code, think through the architecture:


    **Key design decisions (developer-owned, NOT Copilot's job):**
    
    - No database — all data lives in a Python list
    
    - No REST API — the route returns rendered HTML directly
    
    - Risk levels map to Bootstrap badge colors: healthy → green, at-risk →
      warning/yellow, critical → danger/red
    
    **Note :** Instead of relying on Copilot to provide suggestions, you can
    provide hints about what code you expect by using code
    comments.​[![](./media/image2.gif)](https://code.visualstudio.com/docs/copilot/ai-powered-suggestions)​
    Defining your architecture first ensures your prompts are precise and
    your review is informed.

## Task 2: Use GitHub Copilot to Scaffold the Mock Data Module 

1.  Create a new file: **data.py** in the root folder

2.  Type the following **intent-driven comment** at the top of the file  and press **Enter**:

+++# Mock customer data for a Customer Health Dashboard prototype.+++

+++# Each customer has: name (str), industry (str), health_score (int 0-100),and risk_level (one of "healthy", "at-risk", "critical").+++

+++# Generate a list of exactly 10 diverse customers across different industries.+++

3.  **Pause and observe.** Copilot offers dimmed ghost text suggestions
    as you type: sometimes the completion of the current line, sometimes
    a whole new block of code.​

4.  Press **Tab** to accept the suggestion. Copilot should generate a
    list of 10 customer dictionaries.

    ![](./media/image3.png)
    
    ![](./media/image4.png)
    
    ![](./media/image5.png)

5.  Do NOT blindly accept. Verify:

    - Exactly **10** customers are present

    - Each has all 4 required
      fields: name, industry, health_score, risk_level

    - health_score values are integers between 0–100

    - risk_level values are strictly one
      of: "healthy", "at-risk", "critical"

    - The risk_level logically correlates with health_score (e.g., a
      score of 25 shouldn't say "healthy")

    - Industries are diverse (not all "Technology")

    ![](./media/image6.png)

6.  If Copilot's output is incomplete or has inconsistencies, **refine
    using Copilot Chat**. Press **Ctrl+I**  and type:

7.  Fix this customer list: ensure health_score and risk_level are
    consistent.

8.  Scores 0-40 should be "critical", 41-70 should be "at-risk", 71-100
    should be "healthy".

9.  Ensure exactly 10 customers with diverse industries.

10. Your final data.py should look similar to this (Copilot's output
    will vary):

    ![](./media/image7.png)

**Note:** Copilot generates plausible data, but it doesn't understand
your business rules. A health score of 90 labeled "critical" would
mislead stakeholders. **You own the data contract.**

## Task 3: Use Copilot to Generate the Flask Application (app.py)

1.  Create a new file: **app.py** in the root folder

2.  Type the following comment block and let Copilot suggest the
    implementation:

    +++# Flask application for Customer Health Dashboard+++
    
    +++# - Import customers from data.py+++
    
    +++# - Single route "/" renders dashboard.html with the customer list+++
    
    +++# - Server-rendered HTML only, no REST API+++
    
    +++# - Run on port 5000 in debug mode+++
    
    ![](./media/image8.png)
    
    ![](./media/image9.png)

3.  On the next line, type the comment # Route to render the
    dashboard and press **Enter**. Then type @ and let Copilot suggest
    the route decorator and function:

    ![](./media/image10.png)

4.  Verify:

    - render_template is imported (not jsonify — we're NOT building an
      API)

    - Template name is "dashboard.html" (must match the file we'll
      create next)

    - The customers variable is passed to the template context

    - Debug mode is True (acceptable for a prototype, never for
      production)

    ![](./media/image11.png)

5.  Your final app.py should look like:

    ![](./media/image10.png)

Note**:** Having related files open in VS Code while using Copilot helps
set this context and lets Copilot get a bigger picture of your project. ​
Keep data.py open in a tab while building app.py — Copilot will
cross-reference field names and structure.

## Task 4: Use Copilot Chat to Generate the Dashboard Template (dashboard.html)

This step uses **Copilot Chat** for a larger, multi-concern generation
(HTML + Bootstrap + Jinja2 logic).

1.  Create the folder structure in the root folder

    +++templates/+++

2.  Create a new file: **templates/dashboard.html**

    ![](./media/image12.png)

3.  Open **Copilot Chat** (click the Chat icon in the sidebar or
    press **Ctrl+Shift+I** 

4.  Enter the following **detailed prompt** in Agent mode.

     ```Generate a Jinja2 HTML template called dashboard.html for a Flask app.
     Requirements:
     - Use Bootstrap 5 via CDN (no local files)
     - Page title: "Customer Health Dashboard"
     - Display a responsive Bootstrap table with columns: \#, CustomerName, Industry, Health Score, Risk Level
     - Iterate over a \`customers\` list passed from Flask
     - Each customer dict has keys: name, industry, health_score,> risk_level
     - Color-code the Risk Level column using Bootstrap badges:
     - "healthy" → badge bg-success
     - "at-risk" → badge bg-warning text-dark
     - "critical" → badge bg-danger
     - Add a container with margin-top, a heading, and a brief subtitle
     - Use loop.index for the row number
     - Clean, production-quality HTML
     ```

    ![](./media/image13.png)

5.  Review the Copilot Chat output carefully before pasting it into your
    file. Click on Keep if it matches to your requirements.

    - Bootstrap 5 CDN link is present in \<head\> (not Bootstrap 4)

    - Jinja2 {% for customer in customers %} loop is correct

    - Badge classes match the specification exactly

    - The text-dark class is applied to the bg-warning badge (yellow
      badges need dark text for readability)

    - {{ loop.index }} is used for row numbering (not loop.index0)

    - No hardcoded customer data — everything comes from the template
      variable

    ![](./media/image14.png)

6.  Your final templates/dashboard.html should look similar to:

    ![](./media/image14.png)

**Note:** The template involves multiple concerns (HTML structure,
Bootstrap classes, Jinja2 logic, conditional rendering). Copilot is now
a suite of tools built for every step of the workflow.​ Copilot Chat
excels at multi-line, multi-concern generation where a single comment
prompt would be insufficient.

## Task 5: Validate Results — Run the Application

1.  Open the VS Code **integrated terminal** (Ctrl+\`\` or **Terminal → New Terminal**) -\> GitBash.

2.  Ensure your virtual environment is activated, then run:

    +++python app.py+++

3.  You should see output similar to:

    ![](./media/image15.png)

4.  Open your browser and navigate to +++http://127.0.0.1:5000+++

    ![](./media/image2.gif)

    ![](./media/image16.png)

5.  **Validation Checklist:**

    |Check|Expected Result|
    |--|--|
    |Page loads without errors|HTTP 200, no stack traces|
    |Title displays|"Customer Health Dashboard" in heading|
    |Table shows 10 rows|Exactly 10 customer rows rendered|
    |All columns populated|#, Name, Industry, Score, Risk Level filled in|
    |Healthy badge|Green (bg-success) badge appears|
    |At-Risk badge|Yellow (bg-warning) badge with dark text|
    |Critical badge|Red (bg-danger) badge appears|
    |Responsive layout|Table adjusts on browser resize|

7.  **If errors occur**, use Copilot to debug. Select the error in the
    terminal, press **Ctrl+I**, and type:

    +++/fix Explain this Flask error and suggest a fix+++

    Copilot works even better if you give it an error message or highlightthe part of the code that's broken.​

## Summary:

After completing this lab, you will have a **running Flask web
application** that:

- Serves a Bootstrap 5-styled HTML dashboard at http://127.0.0.1:3000

- Displays 10 mock customer records in a responsive table

- Color-codes risk levels using Bootstrap badges (green/yellow/red)

- Uses no database, no REST API, and no frontend framework

All three files were scaffolded with significant Copilot assistance, but
every output was **reviewed and validated by the developer**.
