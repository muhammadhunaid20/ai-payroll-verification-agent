# AI Payroll Verification Agent

A goal-driven AI Payroll Verification Agent built with Python, LLMs, function calling, Google Sheets API, and human-in-the-loop approval.

The agent understands a user's payroll verification goal, dynamically selects the tools required to retrieve attendance, sales, payroll, and business policy data, and uses deterministic Python verification logic to validate payroll entries. It identifies discrepancies, explains results using available evidence, and requires explicit human approval before making payroll updates.

This project demonstrates a practical application of Agentic AI in an enterprise payroll and business-process workflow, combining LLM-based goal understanding and tool selection with controlled business logic, runtime policies, real-time business data, and human oversight.

Unlike a fixed automation workflow, macro, VBA script, Power Query process, or predefined sequence, the agent starts from a business goal and uses available tools to determine the information required for verification. The LLM handles goal understanding and tool orchestration, while Python remains responsible for deterministic payroll verification and calculations.

---

## Demo & Test the Agent

The project includes a sample Google Spreadsheet with synthetic payroll, attendance, sales, and policy data.

### Online Demo Google Sheet

**[Open the Demo Google Sheet](https://docs.google.com/spreadsheets/d/1vmRSuGGhDKH03uG0Z9rV7YQYeFVGhowwGIINr_mb9yk/edit?usp=sharing)**

The spreadsheet is provided as a view-only demonstration.

If you want to run the agent yourself:

1. Open the demo Google Sheet.
2. Select **File → Make a copy**.
3. Save your own copy to Google Drive.
4. Copy the ID of your new spreadsheet.
5. Replace `YOUR_GOOGLE_SHEET_ID` in the notebook with your copied spreadsheet ID.
6. Configure Google authentication and your LLM API key.
7. Run the agent.

The original demonstration spreadsheet remains unchanged while users can experiment safely with their own copy.

### Demo Data Included

The Google Spreadsheet contains separate business data for:

```text
Attendance
Attendance_Policy
Sales
Commission_Policy
Payroll
```

This separation represents a common enterprise scenario where payroll verification depends on information maintained across multiple business records.

---

## Project Overview

Payroll verification often requires manual checking across several data sources.

A payroll officer may prepare payroll while attendance, overtime, sales commission, and policy information are maintained separately. An incorrect value in one component can affect total earnings or deductions.

This project introduces an AI-powered verification layer between payroll preparation and payroll finalization.

Instead of simply calculating payroll from scratch, the agent addresses a practical business question:

> **Is the payroll prepared by the payroll officer consistent with the available business data and applicable policies?**

The agent can retrieve relevant information, determine which tools are needed, verify payroll values, identify differences, explain supported discrepancies, and wait for human approval before changing the payroll record.

---

## What Makes This an AI Agent?

This project is intentionally different from a traditional fixed automation workflow.

A conventional automation might execute a predefined sequence:

```text
Read Sheet
→ Calculate
→ Write Result
```

This project introduces an LLM as the **goal-understanding and tool-selection layer**.

The user provides a business goal such as:

```text
Verify payroll for employee EMP001 and explain any incorrect amounts.
```

The agent determines which available tools are relevant to the request, retrieves the required information, receives authoritative results from Python functions, and uses those results to produce a business-readable verification report.

The high-level flow is:

```text
User Goal
    ↓
LLM Agent
    ↓
Tool / Function Selection
    ↓
Business Data
    ↓
Python Verification Logic
    ↓
Evidence-Based Result
    ↓
Human Approval
    ↓
Payroll Update
```

This makes the project a practical example of **goal-driven Agentic AI**, rather than simply a macro, VBA script, Power Query transformation, fixed Python automation, or predefined workflow.

---

## Agentic Architecture

```text
                         ┌─────────────────────┐
                         │      User Goal      │
                         │ "Verify EMP001"     │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │     LLM Agent       │
                         │ Goal Understanding  │
                         │ Tool Selection      │
                         └──────────┬──────────┘
                                    │
                         Function / Tool Calling
                                    │
              ┌─────────────────────┼─────────────────────┐
              ▼                     ▼                     ▼
       ┌─────────────┐       ┌─────────────┐       ┌──────────────┐
       │ Attendance  │       │    Sales    │       │   Policies   │
       │    Data     │       │    Data     │       │    Data      │
       └──────┬──────┘       └──────┬──────┘       └──────┬───────┘
              │                     │                     │
              └─────────────────────┼─────────────────────┘
                                    ▼
                         ┌─────────────────────┐
                         │ Python Verification │
                         │ Deterministic Logic │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Verification Report │
                         │ Current vs Expected │
                         │ Differences         │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │   Human Approval    │
                         └──────────┬──────────┘
                                    │
                         ┌──────────┴──────────┐
                         ▼                     ▼
                    Approved                Rejected
                         │                     │
                         ▼                     ▼
                  Update Payroll             STOP
```

---

## Core Capabilities

### 1. Payroll Verification

The agent verifies payroll values prepared by a human payroll officer against underlying business data and applicable policies.

It can validate:

* Commission earnings
* Overtime earnings
* Total earnings
* Late-entry deductions
* Early-exit deductions
* Total deductions

The system compares payroll values with expected values produced by the verification logic.

---

### 2. Runtime Business Policies

Payroll rules are not permanently hard-coded into the agent's business logic.

Attendance and commission policies are maintained in Google Sheets and retrieved at runtime.

Attendance policies can contain:

* Day type
* Minimum payable hours
* Maximum payable hours
* Hourly rate
* Multiplier
* Effective date
* Active status

This allows business rules to be maintained separately from the Python implementation.

A policy change can therefore be reflected through the business data source instead of requiring the developer to rewrite the verification logic.

---

### 3. Effective-Date and Active-Policy Handling

Attendance policies include an effective date and active status.

The verification process uses policy information from the business data source rather than assuming that a rule is permanently valid.

This reflects an important enterprise requirement: business rules can change over time and payroll verification should operate against the applicable policy data.

---

### 4. Overtime Validation

The agent handles minimum and maximum payable overtime hours.

For example:

```text
Minimum payable hours = 2
Maximum payable hours = 4
Hourly rate = Rs. 200
```

If an employee has:

```text
Actual overtime = 5 hours
```

the verification logic treats:

```text
Payable overtime = 4 hours
Excess overtime = 1 hour
```

Only the payable hours are included in expected overtime earnings.

The agent reports excess hours when the Python verification result identifies them.

---

### 5. Commission Verification

Commission is verified using sales data and the commission policy stored in Google Sheets.

The process is:

```text
Sales Records
      ↓
Sales Amount
      ↓
Applicable Commission Policy
      ↓
Expected Commission
      ↓
Comparison with Payroll
```

The LLM does not independently invent or modify commission calculations.

The Python verification result is treated as the authoritative calculation.

---

## Evidence-Based AI Behavior

A key design principle is:

> **The agent explains what the available data supports instead of inventing a reason for a discrepancy.**

For example:

```text
Commission
Current:  Rs. 1,000
Expected: Rs. 5,000
Difference: Rs. -4,000
```

The agent reports the values returned by the verification logic.

It does not invent explanations such as:

```text
"The payroll officer probably forgot..."
"The commission was likely entered incorrectly..."
"The employee may have..."
```

unless such information is actually available in the underlying data.

When the available information does not establish the reason, the agent states that the available data is insufficient to determine the reason.

This provides a more controlled and auditable interaction between an LLM and enterprise business data.

---

## Deterministic Business Logic + LLM Reasoning

The project separates two responsibilities.

### LLM Agent

The LLM is responsible for:

* Understanding the user's goal
* Selecting relevant tools
* Determining which information is needed
* Interpreting tool results
* Communicating verification results
* Explaining discrepancies using available evidence

### Python Verification Layer

Python is responsible for:

* Reading business data
* Applying verification rules
* Calculating expected values
* Comparing actual and expected values
* Returning structured verification results
* Updating payroll after approval

This separation keeps deterministic business calculations outside free-form LLM reasoning.

The LLM acts as the **agentic decision and interaction layer**, while Python performs the controlled business verification.

---

## Why This Is Different From Fixed Automation

Traditional technologies such as VBA, Excel formulas, Power Query, fixed Python scripts, and predefined workflow automation can be useful for deterministic processes.

This project addresses a different problem.

The agent receives a business goal and has access to a set of controlled tools. The LLM determines which tools are relevant, while Python performs the authoritative verification.

The architecture therefore separates:

```text
Goal Understanding
        +
Tool Selection
        +
Business Verification
        +
Human Decision
```

rather than relying only on:

```text
Trigger
→ Fixed Steps
→ Fixed Output
```

The purpose is not to claim that traditional automation is ineffective. Instead, the project demonstrates where an LLM-driven agent can add a flexible goal-oriented interaction layer to an existing business process.

---

## Human-in-the-Loop Payroll Approval

Payroll changes are not automatically committed merely because the AI identifies a discrepancy.

The project requires explicit human approval before updating payroll.

The workflow is:

```text
Verify
  ↓
Show Current Values
  ↓
Show Expected Values
  ↓
Show Differences
  ↓
Human Review
  ↓
Approve?
  ├── No → No Changes
  │
  └── Yes → Update Payroll
```

This keeps the final payroll correction under human control.

It is particularly relevant to enterprise workflows where financial records should not be changed solely on the basis of an LLM response.

---

## Available Agent Tools

The current agent exposes the following tools:

| Tool                    | Purpose                                             |
| ----------------------- | --------------------------------------------------- |
| `get_attendance`        | Retrieves attendance records for an employee        |
| `get_sales`             | Retrieves sales records for an employee             |
| `get_attendance_policy` | Retrieves attendance and overtime policies          |
| `get_commission_policy` | Retrieves commission policies                       |
| `verify_payroll`        | Verifies payroll against business data and policies |
| `update_payroll`        | Updates payroll after explicit approval             |

These tools provide controlled access to business information and actions.

---

## Google Sheets as the Business Data Layer

Google Sheets is used as the project's lightweight business data layer.

The current prototype separates information into:

```text
Attendance
Attendance_Policy
Sales
Commission_Policy
Payroll
```

This structure makes the prototype easy to inspect while demonstrating an architecture that can later connect to:

* ERP systems
* Databases
* REST APIs
* HR systems
* Finance systems
* Enterprise applications

Google Sheets is therefore the prototype data source, not a limitation of the overall agent architecture.

---

## Example Verification

Example payroll:

```text
Employee: Ahmed
Employee ID: EMP001

Payroll Officer:
Commission       = Rs. 1,000
Overtime         = Rs. 800
Total Earnings   = Rs. 1,800
```

The verification agent retrieves relevant sales, attendance, and policy information.

The verification result can identify:

```text
Commission
Current:  Rs. 1,000
Expected: Rs. 5,000
Difference: Rs. -4,000

Overtime
Current:  Rs. 800
Expected: Rs. 8,200
Difference: Rs. -7,400
```

The agent can also report supported overtime observations, such as actual overtime exceeding the maximum payable hours defined by the applicable policy.

The human can then review the verification result before deciding whether the payroll should be updated.

---

## Business Value

This project demonstrates how Agentic AI can be applied to a real enterprise business process rather than only building a conversational chatbot.

Potential business value includes:

* Reducing manual payroll verification effort
* Detecting inconsistencies before payroll finalization
* Centralizing policy-driven verification
* Making payroll discrepancies easier to understand
* Keeping humans in control of financial changes
* Separating AI interaction from deterministic calculations
* Creating a structured verification process
* Providing a foundation for enterprise system integration

The architecture can also be adapted to other verification-oriented business processes where an AI agent needs to inspect multiple data sources and apply controlled rules.

---

## Enterprise and ERP Perspective

The project was designed from an ERP and business-process perspective.

In a real enterprise environment, the same architecture could connect:

```text
ERP / HR System
       ↓
Attendance Data
       ↓
Sales / Commission Data
       ↓
Payroll Policies
       ↓
AI Verification Agent
       ↓
Human Approval
       ↓
Payroll / ERP Update
```

Google Sheets is used as an accessible prototype data source so the complete Agentic AI workflow can be demonstrated without requiring a production ERP environment.

This makes the project relevant to both **AI engineering and ERP/business process automation**.

---

## Technology Stack

* Python
* Large Language Models (LLMs)
* LLM Function Calling
* Tool Calling
* Agentic AI
* AI Agents
* OpenRouter
* Google Sheets API
* `gspread`
* Google Colab
* Human-in-the-Loop AI
* Business Process Automation

---

## Repository Structure

```text
ai-payroll-verification-agent/
│
├── payroll_agent.ipynb
├── requirements.txt
├── .gitignore
└── README.md
```

The notebook contains the prototype implementation, including:

* Google Sheets integration
* Business data retrieval
* Payroll verification functions
* Agent tool definitions
* Tool registry
* LLM agent loop
* Human approval workflow

---

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/ai-payroll-verification-agent.git
cd ai-payroll-verification-agent
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Open the notebook

Open:

```text
payroll_agent.ipynb
```

The project can also be executed through Google Colab.

### 4. Prepare the Google Sheet

Use the [Demo Google Sheet](YOUR_DEMO_GOOGLE_SHEET_LINK) or create your own spreadsheet.

The required sheets are:

```text
Attendance
Attendance_Policy
Sales
Commission_Policy
Payroll
```

If using the demo:

1. Open the demo spreadsheet.
2. Select **File → Make a copy**.
3. Save the copy in your Google Drive.
4. Copy the spreadsheet ID from the new URL.
5. Configure the notebook with your copied spreadsheet ID.

The notebook intentionally uses:

```python
spreadsheet = sheets_client.open_by_key(
    "YOUR_GOOGLE_SHEET_ID"
)

print("Google Sheet connected.")
```

Replace only `YOUR_GOOGLE_SHEET_ID` with the ID of your own spreadsheet copy.

### 5. Configure Google authentication

Authenticate the Google account that has access to your spreadsheet.

Do not publish Google credentials or OAuth tokens in the repository.

### 6. Configure the LLM API

The notebook uses an LLM through OpenRouter.

Configure the required API key securely through environment variables or Colab Secrets.

Do not place API keys directly inside the notebook.

### 7. Run the agent

Example:

```python
run_agent(
    "Verify payroll for employee ID EMP001 and explain every incorrect amount."
)
```

### 8. Review and approve corrections

After reviewing the verification result:

```python
approve_payroll("EMP001")
```

No payroll correction should be written without explicit human approval.

---

## Security

Do not commit sensitive information to the repository.

Never publish:

* API keys
* Google credentials
* OAuth tokens
* Private payroll records
* Real employee information
* Production spreadsheet credentials
* Confidential company data

Use environment variables, Colab Secrets, or another secure credential mechanism.

The public demonstration should use synthetic or anonymized business data.

---

## Design Principles

### Goal-Driven Execution

The agent starts from the user's business goal rather than a single hard-coded workflow.

### Controlled Tool Access

The LLM interacts with business data through explicitly defined functions.

### Authoritative Calculations

Deterministic payroll calculations are performed by Python rather than relying on free-form LLM arithmetic.

### Runtime Policies

Business rules are retrieved from the data source rather than permanently embedded in Python code.

### Evidence-Based Explanations

The agent explains discrepancies using available verification results.

### No Unsupported Assumptions

The agent does not invent business reasons that are not supported by available data.

### Human Approval

Financial record updates require explicit human confirmation.

### Separation of Concerns

The architecture separates:

```text
LLM Agent
    ↓
Tool Selection
    ↓
Data Access
    ↓
Deterministic Verification
    ↓
Human Decision
    ↓
Data Update
```

This provides a practical foundation for controlled enterprise AI agents.

---

## Future Enhancements

Possible extensions include:

* Multi-employee payroll verification
* Payroll verification dashboard
* Audit logs for verification and approval
* Role-based approval
* Additional payroll components
* REST API integration
* ERP integration
* Database-backed business data
* Email notifications
* Automated verification reports
* Enterprise authentication
* Monitoring and observability
* Exception management
* Approval history and audit trails

---

## Project Purpose

This repository demonstrates the application of **Agentic AI to payroll verification**, combining LLM-based goal understanding, function calling, Python business logic, APIs, enterprise data, and human approval.

The central design pattern is:

> **Use the LLM to understand the business goal and orchestrate tools, use deterministic software to perform controlled business calculations, and keep humans in control of consequential actions.**

This pattern can be extended beyond payroll to finance, procurement, operations, compliance, ERP workflows, reporting, and other enterprise verification processes.

---

## Author

**Muhammad Hunaid Haroon**

ERP Consultant | Business Analyst | AI & Agentic Automation

This project represents practical work at the intersection of:

* ERP and business process consulting
* AI engineering
* Agentic AI
* LLM applications
* Business automation
* Enterprise data workflows

---

## License

This project is intended as a learning and portfolio demonstration of an AI-powered enterprise business-process agent.
