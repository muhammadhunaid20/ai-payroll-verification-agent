# AI Payroll Verification Agent

An **Agentic AI payroll verification system** built with **Python, LLM tool calling, Google Sheets API, and OpenRouter API**.

The agent verifies payroll entries against payroll policies and business data, identifies discrepancies, and presents the verification result for **human review and approval**.

> **Project 1 focuses on payroll verification, not autonomous payroll processing.**

---

## 1. Project Identity

**AI Payroll Verification Agent** is an Agentic AI application for payroll teams.

### Core Purpose

**Payroll Officer → prepares payroll → AI Agent verifies → discrepancies detected → Human reviews**

The system combines:

* **LLM** — understands the user's goal and determines what information/tools are required.
* **AI Agent** — coordinates the workflow and tool execution.
* **Tools** — retrieve payroll, policy, attendance, and sales data.
* **Python** — performs deterministic payroll calculations and validation.
* **Google Sheets API** — provides business data and payroll policies.
* **OpenRouter API** — provides access to the LLM.
* **Human-in-the-loop** — retains final business control.

---

## 2. Business Problem

Payroll verification can involve manually checking:

* Payroll officer entries
* Overtime calculations
* Late-entry deductions
* Early-exit deductions
* Attendance rules
* Sales commissions
* Applicable payroll policies
* Entered values versus expected values

Manual verification can result in calculation errors or missed discrepancies.

The AI Payroll Verification Agent provides an additional verification layer before payroll is approved.

---

## 3. How the Agent Works

```text
User Request
     ↓
AI Agent
     ↓
LLM understands the goal
     ↓
LLM selects required tools
     ↓
Tools retrieve Google Sheets data
     ↓
Python validates calculations and policies
     ↓
Expected values vs entered values
     ↓
Discrepancy Detection
     ↓
Verification Result
     ↓
Human Review / Approval
```

The agent does not simply generate a text response. It can determine which tools are required, execute those tools, use their results, and coordinate a multi-step verification workflow.

---

## 4. Semantic Knowledge Model

The project can be understood through these entities and relationships:

```text
AI Payroll Verification Agent
    → is a → Agentic AI Application

Agent
    → uses → LLM
    → executes → Tools
    → coordinates → Verification Workflow

LLM
    → understands → User Goal
    → selects → Tools
    → interprets → Tool Results

Tools
    → retrieve → Payroll Data
    → retrieve → Policy Data
    → retrieve → Attendance Data
    → retrieve → Sales Data

Python
    → performs → Deterministic Validation
    → calculates → Expected Values
    → compares → Entered vs Expected Values

Validation
    → detects → Payroll Discrepancies

Discrepancy
    → requires → Human Review

Google Sheets API
    → provides → Business Data

OpenRouter API
    → provides → LLM Access
```

This structure describes the project's concepts without claiming that the application itself implements a knowledge graph.

---

## 5. LLM vs Agent vs Tools vs Python

These components have different responsibilities.

| Component             | Responsibility                                                            |
| --------------------- | ------------------------------------------------------------------------- |
| **LLM**               | Understands requests, interprets goals, decides which tools may be needed |
| **Agent**             | Coordinates the LLM, tools, workflow, and results                         |
| **Tools**             | Perform specific business-data operations                                 |
| **Python**            | Performs reliable calculations and deterministic validation               |
| **Google Sheets API** | Reads business and policy data                                            |
| **OpenRouter API**    | Connects the application with an LLM                                      |

### Why separate LLM and Python?

The LLM is useful for **understanding and reasoning about the task**.

Python is used for **exact calculations and rule validation**.

This reduces dependence on the LLM for calculations where deterministic code is more appropriate.

---

## 6. Payroll Verification

The agent verifies payroll entries using policies and source data loaded from Google Sheets.

### Example: Overtime

Policy:

```text
Working Day OT
Minimum = 2 hours
Maximum = 4 hours
Rate = Rs 200/hour
```

If an employee has **5 hours** of working-day overtime:

```text
Recorded OT = 5 hours
Maximum payable OT = 4 hours
Expected OT = 4 × Rs 200
Expected amount = Rs 800
```

If the payroll officer entered a different amount, the agent identifies the discrepancy.

### Example: Late Entry

```text
Late Entry Rate = Rs 250/hour
Late Entry = 0.5 hour
Expected Deduction = Rs 125
```

The system compares the expected amount with the payroll officer's entered amount.

---

## 7. Policy-Driven Validation

Payroll policy values are **not hard-coded in Python**.

The system loads applicable policy records from Google Sheets at runtime.

Policies can contain:

* Policy type
* Minimum value
* Maximum value
* Rate
* Effective date
* Active status

The verification process considers the applicable **Active** policy and its **Effective From** date.

This allows business rules to be maintained as data rather than modifying Python code whenever a policy changes.

---

## 8. Commission Verification

The agent can also verify sales commission calculations against commission policies and sales data.

Example commission structure:

| Sales Range     | Commission Rate |
| --------------- | --------------: |
| 0–50,000        |              2% |
| 50,001–100,000  |              5% |
| 100,001–500,000 |              7% |

The system can compare:

```text
Sales Data
    ↓
Applicable Commission Policy
    ↓
Expected Commission
    ↓
Payroll Officer's Commission
    ↓
Comparison
    ↓
Discrepancy / Verified
```

---

## 9. Tool Calling

Tool calling allows the LLM to request specific application functions instead of attempting to perform every operation itself.

Conceptually:

```text
User: Verify this payroll entry
        ↓
LLM: Determine required information
        ↓
Agent: Execute required tools
        ↓
Tools: Retrieve data
        ↓
Python: Validate calculations
        ↓
Agent: Return verification result
```

Tool arguments are exchanged as structured data, typically using **JSON**.

---

## 10. API Architecture

The project uses two primary APIs:

### Google Sheets API

Used for:

* Payroll data
* Attendance data
* Sales data
* Payroll policies
* Business records

### OpenRouter API

Used as the LLM access layer.

The application can work with an LLM available through OpenRouter rather than being tied to a single model provider.

```text
AI Payroll Agent
      │
      ├── OpenRouter API → LLM
      │
      └── Google Sheets API → Business Data
```

---

## 11. Human-in-the-Loop

The agent is designed as a **verification assistant**, not an autonomous payroll approver.

```text
Agent Verification
       ↓
Verified / Discrepancy
       ↓
Human Payroll Officer
       ↓
Review
       ↓
Approval / Correction
```

This keeps the final payroll decision under human control.

---

## 12. Traditional Automation vs AI Agent

Traditional automation generally follows a predefined sequence:

```text
Input → Fixed Rules → Output
```

This project adds an LLM-driven agent layer:

```text
User Goal
   ↓
LLM interprets goal
   ↓
Agent determines required tools
   ↓
Tools retrieve information
   ↓
Python performs deterministic validation
   ↓
Agent coordinates result
```

The important Agentic AI concepts demonstrated are:

* LLM reasoning
* Tool calling
* Function execution
* Structured arguments
* Multi-step workflow
* External data access
* Deterministic validation
* Human-in-the-loop

---

## 13. Current Implementation vs Future Scope

### Implemented in Project 1

* AI payroll verification
* Agent workflow
* LLM integration
* Tool calling
* Python validation
* Google Sheets API
* OpenRouter API
* Payroll policy validation
* Attendance verification
* Sales/commission verification
* Discrepancy detection
* Human review workflow

### Not Implemented in Project 1

* RAG
* Embeddings
* Vector database
* Semantic document search
* Policy-document retrieval
* Knowledge graph
* Graph database
* ERPNext Integration
* Odoo ERP Integration
* Autonomous payroll approval

### Project 2 Direction

The next version can extend the same payroll agent with:

```text
Payroll Agent
     +
Policy Documents
     ↓
Document Processing
     ↓
Chunking + Embeddings
     ↓
Vector Database
     ↓
Semantic Retrieval
     ↓
RAG
     ↓
Policy-aware Payroll Verification
```

Therefore, **Project 1 is a structured-data/tool-based payroll agent**, while future versions can add document-based knowledge retrieval.

---

## 14. Frequently Asked Questions

### What is this project?

An Agentic AI application that verifies payroll calculations and identifies discrepancies.

### Is this a chatbot?

No. It uses an LLM together with tools, external business data, Python validation, and a multi-step workflow.

### What does the LLM do?

It interprets the user's goal and determines what information or tools are required.

### What does Python do?

Python performs deterministic calculations, policy checks, comparisons, and discrepancy detection.

### Which APIs are used?

**Google Sheets API** for business data and **OpenRouter API** for LLM access.

### Does Project 1 use RAG?

No. RAG is planned for a future project version.

### Does Project 1 use a vector database?

No. Project 1 uses structured data from Google Sheets.

### Does it automatically approve payroll?

No. Final review and approval remain with a human.

---

## 15. Technology Stack

* **Python**
* **LLM / Generative AI**
* **Agentic AI**
* **LLM Tool Calling**
* **Function Calling**
* **JSON**
* **Google Sheets API**
* **OpenRouter API**
* **Google Sheets**
* **Deterministic Business Rules**
* **Human-in-the-Loop**

---

## 16. Repository Structure

```text
AI-Payroll-Verification-Agent/
│
├── AI_Payroll_Verification_Agent.ipynb
├── README.md
├── requirements.txt
└── .gitignore
```

The notebook is designed to be usable from environments such as **Google Colab** and **VS Code**.

---

## 17. Project Summary

**Project:** AI Payroll Verification Agent

**Category:** Agentic AI / Generative AI / Payroll Automation

**Purpose:** Verify payroll calculations against policies and business data.

**LLM Role:** Goal understanding, reasoning, tool selection, and orchestration.

**Agent Role:** Workflow coordination and tool execution.

**Python Role:** Deterministic calculations and validation.

**Data Source:** Google Sheets.

**APIs:** Google Sheets API and OpenRouter API.

**Core Capability:** Payroll verification and discrepancy detection.

**Business Domain:** Payroll, attendance, overtime, deductions, sales commission, payroll policies.

**Architecture:** LLM + Agent + Tools + Python + External APIs + Human Review.

**Current Retrieval Approach:** Structured business-data retrieval through tools.

**Future Retrieval Approach:** Document retrieval and RAG.

**Human Control:** Final payroll review and approval.

---

## 18. Scope Statement

The **AI Payroll Verification Agent** demonstrates how an LLM-powered agent can interact with business tools and structured payroll data while using Python for deterministic validation.

The project deliberately separates:

```text
LLM → Understand and Reason
Agent → Coordinate
Tools → Retrieve / Execute
Python → Calculate and Validate
Human → Review and Approve
```

This separation makes the project a practical example of **Agentic AI applied to payroll verification** rather than a simple chatbot or fixed-rule automation script.

---

## Author

**Muhammad Hunaid Haroon**

MBA | Certified ERP Professional | ERP & Business Process Consultant | Agentic AI Learner

GitHub: `muhammadhunaid20`
