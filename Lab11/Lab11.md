# Lab 11 - Fixing a production incident using GitHub Copilot Agent mode

**Scenario**

It's **9:47 AM on a Monday.** You're an on-call engineer at **ZAVA**
**PayStream Inc.**, a fintech company processing merchant payouts. The
\#incident-critical Slack channel just fired:

 **\[SEV-1\] Payout Processing Service — Multiple Failures**

- Merchants report: payouts not arriving

- Monitoring: 37% of payout requests returning 500 errors

- Logs: TypeError, KeyError, and malformed amounts in database

- QA: 4 of 9 unit tests now failing after Friday's deploy

- Security team flagged: one endpoint may accept negative payout amounts

- **Business impact: $42K/hour in delayed merchant settlements**

Your incident commander says: *"We need this fixed in the next 90
minutes. Use every tool you have."*

You open VS Code. You have GitHub Copilot with Agent Mode. The clock is
ticking.

**Objectives (Copilot-Focused)**

By the end of this capstone, you will be able to:

1.  Use **Copilot Chat (/explain, /fix)** to rapidly diagnose failures
    from logs and error messages.

2.  Use **Agent Mode** to analyze multiple files simultaneously, propose
    multi-step fixes, and iterate autonomously.

3.  Apply a **triage-first approach** — prioritizing fixes by business
    impact.

4.  Generate **missing unit tests** and fix failing ones using Copilot
    under time pressure.

5.  Identify and patch **security vulnerabilities** using
    Copilot-assisted security review.

6.  Apply **Responsible AI checkpoints** — tracking what Agent Mode got
    right, what it got wrong, and what required human override.

**Prerequisites / Setup**

**Environment**

[TABLE]

**Pre-Lab Setup Steps**

1.  **Create the project:**

2.  mkdir paystream-incident && cd paystream-incident

3.  python -m venv venv

4.  source venv/bin/activate \# Windows: venv\Scripts\activate

5.  pip install flask pytest

6.  code .

7.  **Create the following 4 files exactly as written.** Every bug,
    every inconsistency, every security hole is **intentional** — this
    is the broken production codebase you're inheriting at 9:47 AM.

**📄 FILE 1: payout_models.py — Data layer**

import datetime

import uuid

\# PayStream Payout Models - v3.1

active_payouts = \[\]

def create_payout(merchant_id, amount, currency, memo=None):

payout = {

"id": str(uuid.uuid4()),

"merchant_id": merchant_id,

"amt": amount, \# BUG: no validation - negative amounts accepted

"currency": currency,

"memo": memo,

"status": "PENDING",

"created_at": str(datetime.datetime.now()),

"processed_at": None

}

active_payouts.append(payout)

return payout

def get_payout(payout_id):

for p in active_payouts:

if p\["id"\] == payout_id:

return p

return None

def get_merchant_payouts(merchant_id):

result = \[\]

for p in active_payouts:

if p\["merchant_id"\] == merchant_id:

result.append(p)

return result

def process_payout(payout_id):

p = get_payout(payout_id)

if p is None:

return None

\# BUG: processes even if already COMPLETED — no idempotency

fee = calculate_fee(p\["amt"\], p\["currency"\])

p\["fee"\] = fee

p\["net_amount"\] = p\["amt"\] - fee \# BUG: uses 'amt' not 'amount' —
inconsistent key

p\["status"\] = "COMPLETED"

p\["processed_at"\] = str(datetime.datetime.now())

return p

def calculate_fee(amount, currency):

\# Fee schedule: 2.9% + $0.30 for USD, 3.5% + €0.50 for EUR

if currency == "USD":

return round(amount \* 0.029 + 0.30, 2)

elif currency == "EUR":

return round(amount \* 0.035 + 0.50, 2)

\# BUG: no else clause — returns None for unknown currencies

**📄 FILE 2: payout_api.py — Flask API layer**

from flask import Flask, request, jsonify

from payout_models import (

create_payout, get_payout, get_merchant_payouts,

process_payout, active_payouts

)

app = Flask(\_\_name\_\_)

@app.route("/payouts", methods=\["POST"\])

def create():

data = request.get_json()

\# BUG: no validation — missing fields cause KeyError crashes

payout = create_payout(

data\["merchant_id"\],

data\["amount"\],

data\["currency"\],

data.get("memo")

)

return jsonify(payout), 201

@app.route("/payouts/\<payout_id\>", methods=\["GET"\])

def get(payout_id):

p = get_payout(payout_id)

if p is None:

return jsonify({"error": "not found"}), 404

return jsonify(p), 200

@app.route("/payouts/\<payout_id\>/process", methods=\["POST"\])

def process(payout_id):

result = process_payout(payout_id)

if result is None:

return jsonify({"error": "not found"}), 404

\# BUG: returns 200 even when there are processing errors

return jsonify(result), 200

@app.route("/merchants/\<merchant_id\>/payouts", methods=\["GET"\])

def merchant_payouts(merchant_id):

payouts = get_merchant_payouts(merchant_id)

\# BUG: doesn't return 404 for unknown merchants — returns empty list
silently

total = sum(p\["amount"\] for p in payouts) \# BUG: KeyError — key is
'amt' not 'amount'

return jsonify({

"merchant_id": merchant_id,

"payouts": payouts,

"total_amount": total,

"count": len(payouts)

}), 200

if \_\_name\_\_ == "\_\_main\_\_":

app.run(debug=True, port=5000)

**📄 FILE 3: test_payouts.py — Test suite (partially broken)**

import pytest

from payout_models import (

create_payout, get_payout, process_payout,

calculate_fee, get_merchant_payouts, active_payouts

)

from payout_api import app

@pytest.fixture

def client():

app.config\["TESTING"\] = True

with app.test_client() as client:

yield client

@pytest.fixture(autouse=True)

def clear_payouts():

active_payouts.clear()

yield

active_payouts.clear()

\# --- Model Tests ---

def test_create_payout():

p = create_payout("M001", 100.00, "USD")

assert p\["merchant_id"\] == "M001"

assert p\["amt"\] == 100.00

assert p\["status"\] == "PENDING"

def test_calculate_fee_usd():

fee = calculate_fee(100.00, "USD")

assert fee == 3.20 \# 100 \* 0.029 + 0.30 = 3.20

def test_calculate_fee_eur():

fee = calculate_fee(100.00, "EUR")

assert fee == 4.00 \# 100 \* 0.035 + 0.50 = 4.00

def test_process_payout():

p = create_payout("M001", 250.00, "USD")

result = process_payout(p\["id"\])

assert result\["status"\] == "COMPLETED"

assert result\["net_amount"\] == 250.00 - result\["fee"\]

def test_process_nonexistent_payout():

result = process_payout("fake-id-12345")

assert result is None

\# --- API Tests ---

def test_api_create_payout(client):

resp = client.post("/payouts", json={

"merchant_id": "M100",

"amount": 500.00,

"currency": "USD"

})

assert resp.status_code == 201

data = resp.get_json()

assert data\["amt"\] == 500.00

def test_api_get_payout(client):

resp = client.post("/payouts", json={

"merchant_id": "M100",

"amount": 200.00,

"currency": "USD"

})

payout_id = resp.get_json()\["id"\]

resp2 = client.get(f"/payouts/{payout_id}")

assert resp2.status_code == 200

def test_api_merchant_payouts(client):

client.post("/payouts", json={"merchant_id": "M200", "amount": 100.00,
"currency": "USD"})

client.post("/payouts", json={"merchant_id": "M200", "amount": 200.00,
"currency": "USD"})

resp = client.get("/merchants/M200/payouts")

assert resp.status_code == 200

data = resp.get_json()

assert data\["count"\] == 2

assert data\["total_amount"\] == 300.00 \# FAILS: KeyError on 'amount' —
key is 'amt'

def test_api_missing_fields(client):

resp = client.post("/payouts", json={"merchant_id": "M300"})

\# FAILS: expects 400 but gets 500 (unhandled KeyError)

assert resp.status_code == 400

**📄 FILE 4: incident_log.txt — Production log snippets**

\[2026-03-30 23:14:02 UTC\] ERROR payout_api - Unhandled exception in
/payouts

Traceback: KeyError: 'currency'

Request body: {"merchant_id": "M4421", "amount": 750.00}

Client IP: 10.0.3.47

\[2026-03-30 23:14:18 UTC\] ERROR payout_api - Unhandled exception in
/merchants/M2201/payouts

Traceback: KeyError: 'amount'

Note: Model uses key 'amt' but API code references 'amount'

\[2026-03-30 23:15:01 UTC\] WARNING payout_models - Payout created with
negative amount

payout_id: 9f3a2b7c-..., merchant_id: M1105, amount: -500.00, currency:
USD

NOTE: No validation exists — this should never reach the database

\[2026-03-30 23:15:33 UTC\] ERROR payout_models - calculate_fee returned
None

payout_id: d4e82c19-..., currency: GBP

Downstream: net_amount calculation produced TypeError (int - NoneType)

\[2026-03-30 23:16:12 UTC\] WARNING security - Payout M1105 processed
with negative amount

net_amount: -515.20 — CREDITS merchant instead of debiting

SEVERITY: CRITICAL — financial loss vector

\[2026-03-31 01:30:00 UTC\] INFO test_runner - Nightly test results: 5
passed, 4 failed

FAILED: test_api_merchant_payouts — KeyError: 'amount'

FAILED: test_api_missing_fields — AssertionError: 500 != 400

FAILED: test_calculate_fee_gbp — no such test exists (coverage gap)

FAILED: test_process_duplicate — no such test exists (coverage gap)

1.  **Run the failing tests to confirm the broken state:**

2.  pytest test_payouts.py -v

🔴 **Expected:** At
minimum, test_api_merchant_payouts and test_api_missing_fields should
fail. Other tests may pass. This confirms you're starting from the
correct broken state.

**🔬 Step-by-Step Lab Flow**

⏱️ **Simulated Timeline:** Each step includes a "clock" to simulate real
incident pressure. Instructors can project a visible timer if desired.

------------------------------------------------------------------------

**🟥 PHASE 1: TRIAGE — Diagnose the Incident (15 minutes)**

**⏱️ Simulated Clock: 9:47 AM — Incident declared. You're on.**

**Step 1.1 — Human Reasoning: Read the Incident Log FIRST**

Open incident_log.txt and read every line. Before touching Copilot,
create a triage list.

**🧠 Developer Task:** Categorize the issues by severity:

CRITICAL (fix first — financial loss):

□ Negative payout amounts accepted → credits merchant instead of
debiting

□ calculate_fee returns None for GBP → TypeError crashes processing

HIGH (fix next — service availability):

□ POST /payouts crashes with KeyError when fields are missing

□ GET /merchants/\<id\>/payouts crashes — 'amount' vs 'amt' key mismatch

MEDIUM (fix after — test reliability):

□ 4 failing tests

□ Missing test coverage for GBP fees and duplicate processing

LOW (fix last — code quality):

□ Inconsistent key naming ('amt' vs 'amount')

□ No idempotency on process_payout

📝 **Instructor Note:** This triage step is **mandatory and
Copilot-free**. In real incidents, developers must think before acting.
Copilot's agentic capabilities don't replace your judgment in these
situations—they amplify
it.​[**1**![](./media/image1.gif)](https://github.blog/ai-and-ml/github-copilot/how-to-maximize-github-copilots-agentic-capabilities/)​
The triage order (critical → high → medium → low) mirrors real incident
response.

**Step 1.2 — Use Copilot Chat to Confirm the Diagnosis**

Open Copilot Chat and type:

**Copilot Chat Prompt:**

@workspace I'm investigating a production incident. Read
incident_log.txt

and cross-reference it with payout_models.py and payout_api.py.

For each error in the log, identify:

1\. The exact line of code causing the error

2\. The root cause

3\. Suggested severity (critical/high/medium/low)

Present as a table.

**🟢 Expected Copilot Behavior:** Copilot should produce a table mapping
each log entry to specific code lines and root causes.

**✅ Developer Action:**

- Compare Copilot's table against your manual triage.

- Does Copilot correctly identify the **negative amount security
  issue** as critical?

- Does Copilot catch the **'amt' vs 'amount' inconsistency** across
  files?

📝 **Instructor Note:** "Once you've identified the problem area, you
can turn to GitHub Copilot and ask, 'I'm giving this input but getting
this output—what's wrong?' That's where GitHub Copilot really
shines."​[**2**![](./media/image1.gif)](https://github.blog/ai-and-ml/github-copilot/how-to-debug-code-with-github-copilot/)​
The key learning: Copilot confirms and enriches your diagnosis — but the
triage *priority* is a human decision based on business impact.

**Step 1.3 — Use /explain on the Most Dangerous Code**

Select the calculate_fee function in payout_models.py. Type in Copilot
Chat:

**Copilot Chat Prompt:**

/explain What happens when an unsupported currency like "GBP" is passed

to this function? Trace the downstream impact.

**🟢 Expected Copilot Behavior:** Copilot should explain that the
function returns None, which then causes p\["amt"\] - None to raise
a TypeError in process_payout().

**✅ Developer Validation:** Confirm this matches log entry \#4. Root
cause confirmed. Now we fix.

------------------------------------------------------------------------

**🟧 PHASE 2: CRITICAL FIXES — Agent Mode Multi-File Repair (25
minutes)**

**⏱️ Simulated Clock: 10:02 AM — Triage complete. Begin fixing.**

**Step 2.1 — Activate Agent Mode**

In VS Code:

1.  Open the Copilot Chat panel.

2.  At the bottom of the chat input, find the **mode dropdown**.

3.  Select **"Agent"** from the dropdown.

Open the Copilot Chat view in VS Code and select agent from the chat
mode dropdown
list.​[**3**![](./media/image1.gif)](https://github.blog/ai-and-ml/github-copilot/agent-mode-101-all-about-github-copilots-powerful-mode/)​

📝 **Instructor Note:** Explain the mode distinction clearly. GitHub
Copilot agent mode is an autonomous and agentic real-time, synchronous
collaborator that performs multi-step coding tasks based on
natural-language prompts. Rather than just responding to requests, agent
mode actively works toward your
goal.​[**3**![](./media/image1.gif)](https://github.blog/ai-and-ml/github-copilot/agent-mode-101-all-about-github-copilots-powerful-mode/)​

**Step 2.2 — Agent Mode: Fix the Critical Security Vulnerability**

Type in Agent Mode:

**Agent Mode Prompt:**

INCIDENT FIX - CRITICAL PRIORITY

In payout_models.py, the create_payout function accepts negative
amounts.

This is a security vulnerability — negative payouts credit merchants

instead of debiting them (see incident_log.txt).

Fix this by:

1\. Adding input validation in create_payout() to reject amounts \<= 0

2\. Adding input validation to reject empty or None merchant_id

3\. Raising a ValueError with clear messages for invalid inputs

4\. In payout_api.py, catching ValueError in the POST /payouts endpoint

and returning a 400 response with the error message

5\. Do NOT change any existing test expectations

After making changes, run: pytest test_payouts.py -v

**🟢 Expected Agent Behavior:** Watch Agent Mode's process carefully.
Agent mode autonomously uses various tools to get to the end result.
After it runs commands and applies edits, agent mode works to detect
syntax errors, terminal output, test results, and build errors. Based on
the results, it then determines how to
course-correct.​[**3**![](./media/image1.gif)](https://github.blog/ai-and-ml/github-copilot/agent-mode-101-all-about-github-copilots-powerful-mode/)​

You should see Agent Mode:

1.  **Read** payout_models.py and payout_api.py

2.  **Edit** create_payout() to add validation

3.  **Edit** the POST /payouts endpoint to catch ValueError

4.  **Run** pytest test_payouts.py -v in the terminal

5.  **Iterate** if tests fail

**✅ Responsible AI Checkpoint \#1 — Review Agent's Work:**

[TABLE]

📝 **Instructor Note:** Have learners **document in a notepad** what
Agent got right vs. what needed correction. This feeds into the final
reflection.

**Step 2.3 — Agent Mode: Fix the Fee Calculation Bug**

**Agent Mode Prompt:**

INCIDENT FIX - CRITICAL

The calculate_fee function in payout_models.py returns None for
unsupported

currencies (like GBP). This causes a TypeError downstream in
process_payout().

Fix this by:

1\. Adding a default fee calculation for unsupported currencies (4% +
$1.00)

2\. OR raising a ValueError for unsupported currencies

3\. Handling this error gracefully in process_payout()

4\. Update the POST /payouts/\<id\>/process endpoint in payout_api.py

to return a 400 error if the fee calculation fails

Run tests after changes.

**✅ Developer Decision Point — Accept or Refine:**

Agent Mode may choose Option 1 (default fee) or Option 2 (raise
ValueError). **Which is correct?**

[TABLE]

**If Agent chose the default fee:** Override and ask it to use
ValueError instead. In fintech, **correctness \> availability** for
financial calculations.

**Agent Mode Follow-up Prompt:**

Actually, for a financial system, it's safer to reject unknown
currencies

with a ValueError rather than applying a default fee. Please change the

approach to raise ValueError for unsupported currencies and handle it

in the API layer with a 400 response.

Run tests:

pytest test_payouts.py -v

**Step 2.4 — Agent Mode: Fix the Key Mismatch Bug (amt vs amount)**

This is the bug crashing the merchant payouts endpoint. Type in Agent
Mode:

**Agent Mode Prompt:**

INCIDENT FIX - HIGH PRIORITY

There is a key naming inconsistency across the codebase:

\- payout_models.py stores the amount as "amt"

\- payout_api.py references "amount" in the merchant_payouts endpoint

\- This causes a KeyError crash on GET /merchants/\<id\>/payouts

Fix this across ALL files consistently. The canonical key should be
"amount"

(not "amt") because it is more readable. Update:

1\. payout_models.py - change "amt" to "amount" everywhere

2\. payout_api.py - verify all references use "amount"

3\. test_payouts.py - update any test assertions referencing "amt"

Run all tests after changes.

**🟢 Expected Agent Behavior:** Agent Mode should:

1.  Read all 3 files to understand the scope

2.  Rename "amt" → "amount" in payout_models.py

3.  Update process_payout() where it references p\["amt"\]

4.  Update test assertions (e.g., assert p\["amt"\] → assert
    p\["amount"\])

5.  Run tests

Whether you want Copilot to analyze your code base, propose file edits,
or run tests, agent mode allows Copilot to complete all the necessary
subtasks on its
own.​[**3**![](./media/image1.gif)](https://github.blog/ai-and-ml/github-copilot/agent-mode-101-all-about-github-copilots-powerful-mode/)​

**✅ Responsible AI Checkpoint \#2:**

[TABLE]

pytest test_payouts.py -v

✅ **Expected:** test_api_merchant_payouts should now pass.

------------------------------------------------------------------------

**🟨 PHASE 3: Fix Failing Tests & Add Missing Coverage (20 minutes)**

**⏱️ Simulated Clock: 10:27 AM — Critical fixes deployed. Stabilizing
tests.**

**Step 3.1 — Fix the Missing Fields Test**

The test test_api_missing_fields expects a 400 response when required
fields are missing, but the original code returned 500 (unhandled
KeyError). After Phase 2's validation fixes, this should now work.

Run:

pytest test_payouts.py::test_api_missing_fields -v

If it still fails, select the test and the relevant API code, then:

**Copilot Chat Prompt:**

/fix This test expects a 400 status code when 'currency' is missing from

the POST /payouts request body. The endpoint should validate required
fields

and return 400 with a descriptive error. Fix either the test or the

endpoint as needed.
