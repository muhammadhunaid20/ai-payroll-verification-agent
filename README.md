# AI Payroll Verification Agent

A goal-driven **AI Payroll Verification Agent** built with Python, LLMs, function calling, Google Sheets API, and human-in-the-loop approval.

This project demonstrates how an **AI Agent** can verify payroll prepared by a payroll officer by dynamically accessing attendance, sales, payroll, and policy data, validating payroll values against business rules, explaining discrepancies using available evidence, and updating payroll only after explicit human approval.

The project is designed as a practical example of **Agentic AI for enterprise business processes**, combining LLM-based decision making with deterministic Python business logic and real-time business data from Google Sheets.

---

## Project Overview

Payroll verification is often dependent on manual checking across multiple sources.

A payroll officer may prepare a payroll sheet while attendance, overtime, sales commission, and policy information exist in separate business records. A mistake in one component can affect total earnings or deductions.

This project introduces an AI-powered verification layer between payroll preparation and payroll finalization.

Instead of simply calculating payroll from scratch, the agent answers a more practical enterprise question:

> **"Is the payroll prepared by the payroll officer consistent with the available business data and current policies?"**

The agent can inspect the relevant records, retrieve applicable policies, verify the payroll values, identify differences, explain supported discrepancies, and wait for human approval before making an update.

---

## What Makes This an AI Agent?

This project is intentionally different from a traditional fixed automation workflow.

A conventional automation might execute a predefined sequence such as:

```text
Read Sheet
→ Calculate
→ Write Result
```

This project uses an LLM as the goal-understanding and tool-selection layer.

The user provides a goal such as:

```text
Verify payroll for employee EMP001 and explain any incorrect amounts.
```

The agent determines which available tools are relevant, retrieves the required information, receives authoritative results from Python functions, and uses those results to produce a business-readable verification report.

The high-level flow is:

```text
User Goal
    ↓
LLM Agent
    ↓
Tool / Function Selection
    ↓
Google Sheets Data
    ↓
Python Verification Logic
    ↓
Evidence-Based Verification Result
    ↓
Human Approval
    ↓
Update Payroll
```

This makes the project a practical example of **goal-driven Agentic AI**, rather than a simple macro, VBA script, Power Query transformation, or fixed workflow automation.

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
                         │ Differences          │
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

The agent verifies payroll values prepared by a human payroll officer against the underlying business data.

It can validate:

* Commission earnings
* Overtime earnings
* Total earnings
* Late-entry deductions
* Early-exit deductions
* Total deductions

The system compares the payroll value with the expected value produced by the verification logic.

---

### 2. Runtime Business Policies

Payroll rules are not hard-coded into the agent's business logic.

Attendance and commission policies are stored in Google Sheets and retrieved at runtime.

For example, overtime policies can contain:

* Day type
* Minimum payable hours
* Maximum payable hours
* Hourly rate
* Multiplier
* Effective date
* Active status

This allows business rules to be maintained outside the Python code.

A policy change can therefore be reflected through the business data source rather than requiring the developer to rewrite payroll logic.

---

### 3. Effective-Date and Active-Policy Handling

Attendance policies contain an effective date and active status.

The verification process uses the policy data available from the business source rather than assuming that a particular policy is permanently valid.

This is important in enterprise systems because payroll rules can change over time.

---

### 4. Overtime Validation

The agent handles minimum and maximum payable overtime hours.

For example, if a policy specifies:

```text
Minimum payable hours = 2
Maximum payable hours = 4
Hourly rate = Rs. 200
```

and an employee has:

```text
Actual overtime = 5 hours
```

the verification logic treats:

```text
Payable overtime = 4 hours
Excess overtime = 1 hour
```

The excess hour is not included in expected overtime earnings.

The agent reports the excess only when the Python verification logic identifies it.

---

### 5. Commission Verification

Commission is verified using sales data and the commission policy stored in Google Sheets.

The system can retrieve:

```text
Sales Records
      ↓
Total Sales
      ↓
Applicable Commission Policy
      ↓
Expected Commission
      ↓
Comparison with Payroll
```

The LLM does not independently invent or alter the commission calculation.

The Python verification result is treated as the authoritative calculation.

---

## Evidence-Based AI Behavior

A key design principle of this project is:

> **The agent should explain what the available data supports, not invent a reason for a discrepancy.**

For example, if payroll contains:

```text
Commission: Rs. 1,000
Expected:   Rs. 5,000
Difference: Rs. -4,000
```

the agent reports the difference returned by the verification logic.

It does not invent explanations such as:

```text
"The payroll officer probably forgot..."
"The commission was likely entered incorrectly..."
"The employee may have..."
```

unless such information actually exists in the available data.

When the available information does not establish a reason, the agent explicitly states that the available data is insufficient to determine the reason.

This creates a more controlled and auditable interaction between an LLM and enterprise business data.

---

## Deterministic Business Logic + LLM Reasoning

The project deliberately separates two responsibilities.

### LLM

The LLM is responsible for:

* Understanding the user's goal
* Selecting relevant tools
* Deciding which information is needed
* Interpreting tool results
* Communicating verification results
* Explaining discrepancies using available evidence

### Python

Python is responsible for:

* Reading business data
* Applying payroll verification rules
* Calculating expected values
* Comparing actual and expected values
* Returning structured verification results
* Updating the payroll sheet after approval

This separation reduces the risk of allowing the LLM to freely invent business calculations.

The LLM acts as the **agentic decision and interaction layer**, while Python performs the deterministic business verification.

---

## Why This Is Different From Fixed Automation

This project is not intended to replace one fixed manual workflow with another hard-coded script.

Traditional approaches such as:

* VBA macros
* Excel formulas
* Power Query
* fixed Python scripts
* predefined workflow automation
* simple API integrations

can be useful for deterministic processes.

The purpose of this project is different.

The agent receives a business goal and has access to a set of tools. The LLM determines which tools are relevant to the goal, while Python performs the authoritative verification.

This creates a separation between:

```text
Goal Understanding
        +
Tool Selection
        +
Business Verification
        +
Human Decision
```

rather than simply:

```text
Trigger
→ Fixed Steps
→ Fixed Output
```

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

This design keeps the final payroll correction under human control.

It is particularly useful for enterprise workflows where financial records should not be changed solely on the basis of an LLM response.

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

The tools provide the LLM with controlled access to business information and actions.

---

## Google Sheets as the Business Data Source

Google Sheets is used as the project's lightweight business data layer.

The current project separates information into logical sheets such as:

```text
Attendance
Attendance_Policy
Sales
Commission_Policy
Payroll
```

This makes the prototype easy to understand while demonstrating concepts that can later be connected to an ERP, database, REST API, or enterprise application.

The same agent architecture can therefore be extended beyond Google Sheets.

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

The verification agent retrieves the relevant sales, attendance, and policy information.

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

The human can then review the result before deciding whether the payroll should be updated.

---

## Business Value

The project demonstrates how Agentic AI can be applied to a real business process rather than only building a conversational chatbot.

Potential business value includes:

* Reducing manual payroll verification effort
* Detecting inconsistencies before payroll finalization
* Centralizing policy-driven verification
* Making payroll discrepancies easier to understand
* Maintaining human control over financial changes
* Separating AI decision-making from deterministic calculations
* Creating an auditable verification process
* Providing a foundation for integration with enterprise systems

The same architecture can be adapted to other enterprise verification tasks where an AI agent needs to inspect multiple data sources and apply controlled business rules.

---

## Enterprise and ERP Perspective

The project was designed from an enterprise business-process perspective.

In a real ERP environment, the same architecture could be connected to:

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

Google Sheets is used here as an accessible prototype data source so that the complete Agentic AI workflow can be demonstrated without requiring a production ERP environment.

This makes the project relevant to both **AI engineering** and **ERP/business process automation**.

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

The notebook contains the complete prototype implementation, including:

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

### 4. Configure your own Google Sheet

Create a Google Spreadsheet containing the required sheets:

```text
Attendance
Attendance_Policy
Sales
Commission_Policy
Payroll
```

Use your own test or sample data.

### 5. Configure the LLM API

The notebook uses an LLM through OpenRouter.

Configure the required API key securely through the environment or Colab secrets rather than placing credentials directly inside the notebook.

### 6. Run the agent

Example:

```python
run_agent(
    "Verify payroll for employee ID EMP001 and explain every incorrect amount."
)
```

### 7. Review and approve corrections

The human approval workflow can then be used:

```python
approve_payroll("EMP001")
```

No payroll update should occur without explicit approval.

---

## Security

Do not commit sensitive information to the repository.

Never publish:

* API keys
* Google credentials
* OAuth tokens
* Private payroll records
* Real employee information
* Production Google Sheet credentials
* Confidential company data

Use environment variables, Colab Secrets, or another secure credential mechanism.

For a public demonstration, use synthetic or anonymized business data.

---

## Design Principles

The project follows several principles that are important when applying LLMs to enterprise workflows:

### Goal-driven execution

The agent starts from the user's business goal rather than a single hard-coded workflow.

### Controlled tool access

The LLM interacts with business data through explicitly defined tools.

### Authoritative calculations

Deterministic payroll calculations are performed by Python rather than relying on free-form LLM arithmetic.

### Runtime policies

Business rules are retrieved from the data source rather than permanently embedded in the agent prompt.

### Evidence-based explanations

The agent explains discrepancies using the available verification results.

### No unsupported assumptions

The agent does not invent business reasons that are not supported by the available data.

### Human approval

Financial record updates require explicit human confirmation.

### Separation of concerns

The project separates:

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

This architecture provides a practical foundation for building more controlled enterprise AI agents.

---

## Future Enhancements

Possible future extensions include:

* Multi-employee payroll verification
* Payroll verification dashboard
* Audit logs for every verification and approval
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

This repository is a practical demonstration of applying **Agentic AI, LLMs, function calling, Python, APIs, and business-process automation** to a real-world payroll verification problem.

The project focuses on an important enterprise AI pattern:

> **Use the LLM to understand goals and orchestrate tools, use deterministic software to perform controlled business calculations, and keep humans in control of consequential actions.**

This pattern can be extended to payroll, finance, procurement, operations, compliance, ERP workflows, reporting, and other enterprise verification use cases.

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
