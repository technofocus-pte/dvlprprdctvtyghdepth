# Lab 12 — Building a Flask customer health dashboard using GitHub Copilot

**Scenario**

You are a developer on a Customer Success team. Your product manager has
requested a quick internal prototype: a **Customer Health
Dashboard** that displays 10 mock customer accounts with their health
scores and risk levels. The dashboard must be a simple, server-rendered
Flask app with Bootstrap 5 styling — no database, no REST API, no
frontend framework. Your goal is to use **GitHub Copilot** to scaffold
and build this prototype as fast as possible while maintaining code
quality and ownership.

**Learning Objectives**

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

**Environment Setup**

1.  Create a new project folder:

2.  mkdir customer-health-dashboard && cd customer-health-dashboard

3.  Create and activate a virtual environment:

4.  python -m venv venv# Windowsvenv\Scripts\activate# macOS/Linuxsource
    venv/bin/activate

5.  Install Flask:

6.  pip install flask

7.  Open the folder in VS Code:

8.  code .

9.  Verify GitHub Copilot is active — look for the **Copilot icon** in
    the VS Code status bar.

------------------------------------------------------------------------

## Task 1: Understand the Problem (Human Reasoning)

Before touching any code, think through the architecture:

[TABLE]

**Key design decisions (developer-owned, NOT Copilot's job):**

- No database — all data lives in a Python list

- No REST API — the route returns rendered HTML directly

- Risk levels map to Bootstrap badge colors: healthy → green, at-risk →
  warning/yellow, critical → danger/red

**Copilot Tip:** Instead of relying on Copilot to provide suggestions,
you can provide hints about what code you expect by using code
comments.​[**1**![](./media/image1.gif)](https://code.visualstudio.com/docs/copilot/ai-powered-suggestions)​
Defining your architecture first ensures your prompts are precise and
your review is informed.

**Step 2: Use GitHub Copilot to Scaffold the Mock Data Module
(data.py)**

1.  Create a new file: **data.py**

2.  Type the following **intent-driven comment** at the top of the file
    and press **Enter**:

3.  \# Mock customer data for a Customer Health Dashboard prototype.

4.  \# Each customer has: name (str), industry (str), health_score (int
    0-100),

5.  \# and risk_level (one of "healthy", "at-risk", "critical").

6.  \# Generate a list of exactly 10 diverse customers across different
    industries.

7.  

8.  customers = \[

9.  **Pause and observe.** Copilot offers dimmed ghost text suggestions
    as you type: sometimes the completion of the current line, sometimes
    a whole new block of
    code.​[**1**![](./media/image1.gif)](https://code.visualstudio.com/docs/copilot/ai-powered-suggestions)​

10. Press **Tab** to accept the suggestion. Copilot should generate a
    list of 10 customer dictionaries.

11. **⚠️ Developer Review Checkpoint — Do NOT blindly accept.** Verify:

    - Exactly **10** customers are present

    - Each has all 4 required
      fields: name, industry, health_score, risk_level

    - health_score values are integers between 0–100

    - risk_level values are strictly one
      of: "healthy", "at-risk", "critical"

    - The risk_level logically correlates with health_score (e.g., a
      score of 25 shouldn't say "healthy")

    - Industries are diverse (not all "Technology")

12. If Copilot's output is incomplete or has inconsistencies, **refine
    using Copilot Chat**. Press **Ctrl+I** (or **⌘+I** on Mac) and type:

13. Fix this customer list: ensure health_score and risk_level are
    consistent.

14. Scores 0-40 should be "critical", 41-70 should be "at-risk", 71-100
    should be "healthy".

15. Ensure exactly 10 customers with diverse industries.

16. Your final data.py should look similar to this (Copilot's output
    will vary):

17. \# Mock customer data for a Customer Health Dashboard prototype.

18. \# Each customer has: name, industry, health_score (0-100),
    risk_level.

19. \# risk_level rules: 0-40 = critical, 41-70 = at-risk, 71-100 =
    healthy

20. 

21. customers = \[

22. {"name": "Acme Corp", "industry": "Manufacturing", "health_score":
    85, "risk_level": "healthy"},

23. {"name": "Globex Inc", "industry": "Technology", "health_score": 62,
    "risk_level": "at-risk"},

24. {"name": "Initech", "industry": "Financial Services",
    "health_score": 28, "risk_level": "critical"},

25. {"name": "Umbrella Ltd", "industry": "Pharmaceuticals",
    "health_score": 91, "risk_level": "healthy"},

26. {"name": "Stark Industries", "industry": "Defense", "health_score":
    45, "risk_level": "at-risk"},

27. {"name": "Wayne Enterprises", "industry": "Conglomerate",
    "health_score": 73, "risk_level": "healthy"},

28. {"name": "Soylent Corp", "industry": "Food & Beverage",
    "health_score": 34, "risk_level": "critical"},

29. {"name": "Cyberdyne Systems", "industry": "AI & Robotics",
    "health_score": 55, "risk_level": "at-risk"},

30. {"name": "Oscorp", "industry": "Biotechnology", "health_score": 18,
    "risk_level": "critical"},

31. {"name": "Pied Piper", "industry": "SaaS", "health_score": 78,
    "risk_level": "healthy"},

32. \]

📌 **Why review matters:** Copilot generates plausible data, but it
doesn't understand your business rules. A health score of 90 labeled
"critical" would mislead stakeholders. **You own the data contract.**

------------------------------------------------------------------------

**Step 3: Use Copilot to Generate the Flask Application (app.py)**

1.  Create a new file: **app.py**

2.  Type the following comment block and let Copilot suggest the
    implementation:

3.  \# Flask application for Customer Health Dashboard

4.  \# - Import customers from data.py

5.  \# - Single route "/" renders dashboard.html with the customer list

6.  \# - Server-rendered HTML only, no REST API

7.  \# - Run on port 5000 in debug mode

8.  Start typing on the next line:

9.  from flask import Flask, render_template

10. Press **Enter** and continue typing:

11. from data import customers

12. Now type app = and let Copilot autocomplete. It should suggest:

13. app = Flask(\_\_name\_\_)

14. On the next line, type the comment # Route to render the
    dashboard and press **Enter**. Then type @ and let Copilot suggest
    the route decorator and function:

15. @app.route("/")

16. def dashboard():

17. return render_template("dashboard.html", customers=customers)

18. Finally, type if \_\_name\_\_ and let Copilot complete the entry
    point:

19. if \_\_name\_\_ == "\_\_main\_\_":

20. app.run(debug=True)

21. **⚠️ Developer Review Checkpoint:** Verify:

    - render_template is imported (not jsonify — we're NOT building an
      API)

    - Template name is "dashboard.html" (must match the file we'll
      create next)

    - The customers variable is passed to the template context

    - Debug mode is True (acceptable for a prototype, never for
      production)

22. Your final app.py should look like:

23. from flask import Flask, render_template

24. from data import customers

25. 

26. app = Flask(\_\_name\_\_)

27. 

28. @app.route("/")

29. def dashboard():

30. return render_template("dashboard.html", customers=customers)

31. 

32. if \_\_name\_\_ == "\_\_main\_\_":

33. app.run(debug=True)

📌 **Copilot Insight:** Having related files open in VS Code while using
Copilot helps set this context and lets Copilot get a bigger picture of
your
project.​[**1**![](./media/image1.gif)](https://code.visualstudio.com/docs/copilot/ai-powered-suggestions)​
Keep data.py open in a tab while building app.py — Copilot will
cross-reference field names and structure.

------------------------------------------------------------------------

**Step 4: Use Copilot Chat to Generate the Dashboard Template
(dashboard.html)**

This step uses **Copilot Chat** for a larger, multi-concern generation
(HTML + Bootstrap + Jinja2 logic).

1.  Create the folder structure:

2.  templates/

Create a new file: **templates/dashboard.html**

3.  Open **Copilot Chat** (click the Chat icon in the sidebar or
    press **Ctrl+Shift+I** / **⌘+Shift+I**).

4.  Enter the following **detailed prompt**:

5.  Generate a Jinja2 HTML template called dashboard.html for a Flask
    app.

6.  

7.  Requirements:

8.  \- Use Bootstrap 5 via CDN (no local files)

9.  \- Page title: "Customer Health Dashboard"

10. \- Display a responsive Bootstrap table with columns: \#, Customer
    Name, Industry, Health Score, Risk Level

11. \- Iterate over a \`customers\` list passed from Flask

12. \- Each customer dict has keys: name, industry, health_score,
    risk_level

13. \- Color-code the Risk Level column using Bootstrap badges:

14. \- "healthy" → badge bg-success

15. \- "at-risk" → badge bg-warning text-dark

16. \- "critical" → badge bg-danger

17. \- Add a container with margin-top, a heading, and a brief subtitle

18. \- Use loop.index for the row number

19. \- Clean, production-quality HTML

20. **Review the Copilot Chat output carefully** before pasting it into
    your file. Check:

    - Bootstrap 5 CDN link is present in \<head\> (not Bootstrap 4)

    - Jinja2 {% for customer in customers %} loop is correct

    - Badge classes match the specification exactly

    - The text-dark class is applied to the bg-warning badge (yellow
      badges need dark text for readability)

    - {{ loop.index }} is used for row numbering (not loop.index0)

    - No hardcoded customer data — everything comes from the template
      variable

21. Your final templates/dashboard.html should look similar to:

22. \<!DOCTYPE html\>

23. \<html lang="en"\>

24. \<head\>

25. \<meta charset="UTF-8"\>

26. \<meta name="viewport" content="width=device-width,
    initial-scale=1.0"\>

27. \<title\>Customer Health Dashboard\</title\>

28. \<link
    href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css"

29. rel="stylesheet"\>

30. \</head\>

31. \<body\>

32. \<div class="container mt-5"\>

33. \<h1 class="mb-2"\>Customer Health Dashboard\</h1\>

34. \<p class="text-muted mb-4"\>Overview of customer health scores and
    risk levels.\</p\>

35. 

36. \<table class="table table-striped table-hover"\>

37. \<thead class="table-dark"\>

38. \<tr\>

39. \<th\>#\</th\>

40. \<th\>Customer Name\</th\>

41. \<th\>Industry\</th\>

42. \<th\>Health Score\</th\>

43. \<th\>Risk Level\</th\>

44. \</tr\>

45. \</thead\>

46. \<tbody\>

47. {% for customer in customers %}

48. \<tr\>

49. \<td\>{{ loop.index }}\</td\>

50. \<td\>{{ customer.name }}\</td\>

51. \<td\>{{ customer.industry }}\</td\>

52. \<td\>{{ customer.health_score }}\</td\>

53. \<td\>

54. {% if customer.risk_level == "healthy" %}

55. \<span class="badge bg-success"\>Healthy\</span\>

56. {% elif customer.risk_level == "at-risk" %}

57. \<span class="badge bg-warning text-dark"\>At-Risk\</span\>

58. {% elif customer.risk_level == "critical" %}

59. \<span class="badge bg-danger"\>Critical\</span\>

60. {% endif %}

61. \</td\>

62. \</tr\>

63. {% endfor %}

64. \</tbody\>

65. \</table\>

66. \</div\>

67. 

68. \<script
    src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"\>\</script\>

69. \</body\>

70. \</html\>

📌 **Why Chat over inline here?** The template involves multiple
concerns (HTML structure, Bootstrap classes, Jinja2 logic, conditional
rendering). Copilot is now a suite of tools built for every step of the
workflow.​[**2**![](./media/image1.gif)](https://github.blog/ai-and-ml/github-copilot/a-developers-guide-to-writing-debugging-reviewing-and-shipping-code-faster-with-github-copilot/)​
Copilot Chat excels at multi-line, multi-concern generation where a
single comment prompt would be insufficient.

------------------------------------------------------------------------

**Step 5: Validate Results — Run the Application**

1.  Open the VS Code **integrated terminal** (Ctrl+\`\` or **Terminal →
    New Terminal**).

2.  Ensure your virtual environment is activated, then run:

3.  python app.py

4.  You should see output similar to:

5.  \* Serving Flask app 'app'

6.  \* Debug mode: on

7.  \* Running on http://127.0.0.1:5000

8.  Open your browser and navigate
    to **​**[**http://127.0.0.1:5000**![](./media/image1.gif)](http://127.0.0.1:5000/)**​**

9.  **✅ Validation Checklist:**

[TABLE]

10. **If errors occur**, use Copilot to debug. Select the error in the
    terminal, press **Ctrl+I**, and type:

11. /fix Explain this Flask error and suggest a fix

Copilot works even better if you give it an error message or highlight
the part of the code that's
broken.​[**3**![](./media/image1.gif)](https://github.com/mlh/copilot-starter)​

------------------------------------------------------------------------

**Final Project Structure**

customer-health-dashboard/

├── app.py

├── data.py

└── templates/

└── dashboard.html

------------------------------------------------------------------------

**Expected Outcome**

After completing this lab, you will have a **running Flask web
application** that:

- Serves a Bootstrap 5-styled HTML dashboard at http://127.0.0.1:5000

- Displays 10 mock customer records in a responsive table

- Color-codes risk levels using Bootstrap badges (green/yellow/red)

- Uses no database, no REST API, and no frontend framework

All three files were scaffolded with significant Copilot assistance, but
every output was **reviewed and validated by the developer**.

------------------------------------------------------------------------

**Key Takeaways**

[TABLE]

**🧠 Key Principle**

**"Review everything. AI writes code; you approve it. Always check
logic, style, docs before you
ship."**​[**2**![](./media/image1.gif)](https://github.blog/ai-and-ml/github-copilot/a-developers-guide-to-writing-debugging-reviewing-and-shipping-code-faster-with-github-copilot/)​
Copilot accelerated this prototype from 30+ minutes to under 15, but
every design decision — architecture, data contract, badge color mapping
— remained the developer's responsibility.
