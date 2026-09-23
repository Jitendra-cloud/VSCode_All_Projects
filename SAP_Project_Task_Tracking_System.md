# SAP Project & Task Tracking System

## 1. Purpose

This document defines a personal tracking structure for SAP learning, development, troubleshooting, deployment, and project activities.

The tracker is intended for projects involving:

- SAPUI5 / Fiori
- RAP
- OData V2 / V4
- SAP BTP
- Business Application Studio (BAS)
- Eclipse / ADT
- ABAP
- Cloud Connector
- Destinations
- BSP deployment
- Fiori Launchpad
- SAP Learning tutorials
- SAP Help / SAP Developers documentation
- Troubleshooting and connectivity activities

The main objective is to maintain a long-term record of:

1. What was built
2. Why it was built
3. Which SAP system was used
4. Which technologies were involved
5. What the prerequisites were
6. Whether the project was successful
7. Where it was deployed
8. What problems were encountered
9. What still needs to be done
10. Which learning/documentation sources were used

---

# 2. Recommended Tracking Structure

## Core columns

| Column | Purpose |
|---|---|
| Task ID | Unique identifier for the project/task |
| Task | Project or task name |
| Task Type | Master, Prerequisite, Subtask, Troubleshooting, etc. |
| Date | Date the task was worked on |
| System / Trial | SAP system or trial account used |
| Platform | BAS, Eclipse, S/4HANA, BTP, etc. |
| Technology | SAPUI5, Fiori, RAP, OData V4, ABAP, etc. |
| Project Name | GitHub/project/repository name |
| App Description | What was created or changed |
| Learning Objective | What should be learned from the task |
| Purpose | Why the task was performed |
| Prerequisites | Tasks/knowledge required before starting |
| Status | Current task status |
| Deploy Status | Deployment status |
| Deploy Description | Where/how the application was deployed |
| Current Issue | Current problem, if any |
| Next Step | Next action to perform |
| Source | Learning/tutorial/documentation source |
| Source URL | Official source URL |
| GitHub | Repository URL, if applicable |
| Documentation | Link/name of project documentation |
| Notes | Additional technical notes |

---

# 3. Task ID Convention

Use a stable Task ID instead of relying only on the project name.

Recommended format:

```text
SAP-001
SAP-002
SAP-003
```

For subtasks:

```text
SAP-002-01
SAP-002-02
SAP-002-03
```

Example:

```text
SAP-002
Fiori/UI5 DataSource NONE

SAP-002-01
Create UI5 project

SAP-002-02
Configure manifest.json

SAP-002-03
Configure ui5.yaml

SAP-002-04
Add OData V4 datasource

SAP-002-05
Configure destination

SAP-002-06
Deploy to ABAP

SAP-002-07
Test application
```

This makes dependencies easier to track.

---

# 4. Task Types

Recommended values:

| Task Type | Meaning |
|---|---|
| Master | Main project or learning project |
| Prerequisite | Required knowledge/project before another task |
| Subtask | Activity belonging to a master project |
| Troubleshooting | Problem investigation/fix |
| Research | Technical research/documentation study |
| Deployment | Deployment-specific activity |
| Configuration | System/application configuration |
| Documentation | Documentation-only activity |
| Testing | Functional or technical testing |

---

# 5. Status Values

Use a small, consistent set of statuses:

```text
Not Started
In Progress
Successful
Blocked
Troubleshooting
On Hold
Completed
```

Suggested meaning:

### Not Started

Task has been identified but work has not started.

### In Progress

Currently being worked on.

### Successful

The intended technical objective has been achieved and tested.

### Blocked

Progress is blocked by another dependency, system issue, authorization, connectivity, etc.

### Troubleshooting

The task is specifically focused on diagnosing/fixing a problem.

### On Hold

Temporarily paused.

### Completed

Documentation/learning/project work is finished.

---

# 6. Deployment Status

Recommended values:

```text
Not Required
Not Started
In Progress
Successful
Failed
Blocked
```

Example:

```text
Deploy Status: Successful

Deploy Description:
Deployed in ABAP using BSP Library.
```

---

# 7. Prerequisites

Instead of maintaining:

```text
Prerequisite_1
Prerequisite_2
Prerequisite_3
Prerequisite_4
```

prefer one field:

```text
Prerequisites
```

Example:

```text
SAP-002-01; SAP-003
```

This avoids adding more columns when a project has more than four prerequisites.

Prerequisites can represent either:

### Project prerequisites

```text
SAP-001
SAP-002
```

or

### Knowledge prerequisites

```text
JavaScript
SAPUI5 basics
OData V4
RAP basics
ABAP CDS
```

For important projects, use both where necessary:

```text
Prerequisites:
SAP-001; SAPUI5 basics; OData V4
```

---

# 8. Technology Column

Use the Technology column to identify the SAP technologies involved.

Examples:

```text
SAPUI5; Fiori; OData V4; BTP BAS
```

```text
RAP; ABAP CDS; OData V4; Draft
```

```text
BTP; Destination; Cloud Connector
```

```text
ABAP; AMDP; HANA SQLScript
```

This makes it possible to filter the tracker later.

For example:

> Show all projects involving OData V4.

or:

> Show all RAP projects.

---

# 9. Learning Objective

Every major learning project should have a clear learning objective.

Example:

```text
Understand how a RAP OData V4 draft business service is
created and consumed from a SAPUI5 application.
```

Another example:

```text
Understand how a SAPUI5 application can initially be created
without a datasource and later connected to an OData service.
```

This separates:

```text
What I built
```

from:

```text
What I learned
```

---

# 10. Current Issue / Next Step

These two columns are especially important for ongoing work.

Example:

```text
Current Issue:
Street and PostCode are not being persisted.

Next Step:
Check RAP behavior mapping for Street and PostCode.
```

Another example:

```text
Current Issue:
Cloud Connector connection fails on HTTPS port 443.

Next Step:
Check Cloud Connector system mapping and connectivity.
```

This allows work to resume later without reconstructing the entire troubleshooting history.

---

# 11. Example: RAP V4 Project

## SAP-001

### Task

```text
RAP_V4_DRAFT_M
```

### Task Type

```text
Master
```

### System / Trial

```text
Trial - JHN
```

### Platform

```text
S/4HANA / ABAP
```

### Technology

```text
RAP; OData V4; Draft; Fiori/UI5; BTP BAS
```

### App Description

```text
Created a sample RAP V4 application for consumption
by a SAP Fiori/UI5 application in BTP BAS.
```

### Learning Objective

```text
Understand how a RAP OData V4 draft business service
is created and consumed from SAPUI5.
```

### Purpose

```text
Learn and test OData V4 consumption from a SAPUI5 application.
```

### Status

```text
In Progress / Successful
```

### Prerequisites

```text
ABAP CDS
RAP basics
OData V4
SAPUI5 basics
BTP BAS
```

---

# 12. Example: SAPUI5 DataSource-NONE Project

## SAP-002

### Task

```text
Fiori/UI5_(DataSource-NONE)
```

### Task Type

```text
Master
```

### Date

```text
22-Sep-2026
```

### System / Trial

```text
Trial - LAK
```

### Platform

```text
BAS - Fiori
```

### Technology

```text
SAPUI5; Fiori; OData V4; BTP BAS
```

### Project Name

```text
SAPUI5_DATASOURCE_NONE
```

### App Description

```text
Created a UI5 application from the SAP Learning tutorial
without cloning an existing GitHub project. A datasource
was added later.
```

### Learning Objective

```text
Understand how a UI5 application works initially without
a datasource and how an OData datasource can be added later.
```

### Purpose

```text
Check whether the application continues working after
adding a datasource later, including destination configuration
in ui5.yaml.
```

### Prerequisites

```text
SAP-002-01
SAP-001 / V4_Odata_Service
```

### Status

```text
Successful
```

### Deploy Status

```text
Successful
```

### Deploy Description

```text
Deployed in ABAP using BSP Library.
```

### Source

```text
SAP Learning
```

### Source

Developing UIs with SAPUI5

---

# 13. Recommended Project/Task Relationship

Instead of putting every activity into one project row, use:

```text
MASTER PROJECT
       |
       +-- TASK
       |
       +-- TASK
       |
       +-- TASK
       |
       +-- TROUBLESHOOTING
       |
       +-- DEPLOYMENT
       |
       +-- DOCUMENTATION
```

Example:

```text
SAP-002
Fiori/UI5 DataSource NONE
│
├── SAP-002-01 Create UI5 application
├── SAP-002-02 Configure manifest.json
├── SAP-002-03 Configure ui5.yaml
├── SAP-002-04 Configure datasource
├── SAP-002-05 Configure destination
├── SAP-002-06 Test OData service
├── SAP-002-07 Deploy to ABAP BSP
└── SAP-002-08 Create documentation
```

This structure is easier to maintain than putting all activities into one row.

---

# 14. Source Tracking

For SAP learning projects, maintain the source separately.

Recommended columns:

```text
Source
Source URL
```

Examples of Source values:

```text
SAP Learning
SAP Help Portal
SAP Developers
SAPUI5 Documentation
SAP Tutorial
GitHub
Personal Experiment
```

Prefer official SAP sources when available.

Useful source categories include:

- SAP Learning
- SAPUI5 documentation
- SAP Help Portal
- SAP Developers
- SAP Tutorials
- SAP Notes
- SAP Community
- GitHub repositories

For a personal experiment, clearly mark it as:

```text
Personal Experiment
```

rather than treating it as official SAP guidance.

---

# 15. Documentation Tracking

Each major project should have its own documentation.

Recommended Documentation values:

```text
README.md
PROJECT_NOTES.md
TROUBLESHOOTING.md
DEPLOYMENT.md
```

For a larger project:

```text
docs/
├── README.md
├── Architecture.md
├── Development.md
├── Deployment.md
├── Troubleshooting.md
└── Learning.md
```

---

# 16. Recommended Final Tracker

A practical spreadsheet structure is:

| Task ID | Task | Task Type | Date | System / Trial | Platform | Technology | Project Name | App Description | Learning Objective | Purpose | Prerequisites | Status | Deploy Status | Deploy Description | Current Issue | Next Step | Source | Source URL | GitHub | Documentation | Notes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|

This should be the main tracking structure.

---

# 17. Example Final Records

| Task ID | Task | Type | System | Platform | Technology | Status | Deploy |
|---|---|---|---|---|---|---|---|
| SAP-001 | RAP_V4_DRAFT_M | Master | Trial - JHN | S/4HANA | RAP; OData V4; Draft | In Progress | — |
| SAP-002 | Fiori/UI5_(DataSource-NONE) | Master | Trial - LAK | BAS - Fiori | SAPUI5; OData V4 | Successful | Successful |
| SAP-002-01 | UI5 Project Creation | Prerequisite | Trial - LAK | BAS | SAPUI5 | Successful | — |
| SAP-003 | V4_Odata_Service | Prerequisite | Trial - JHN | S/4HANA | RAP; OData V4 | Successful | — |

---

# 18. Recommended Workflow

For every new SAP task:

```text
1. Create Task ID
       ↓
2. Define Task Type
       ↓
3. Record System / Platform
       ↓
4. Record Technology
       ↓
5. Define Learning Objective
       ↓
6. Define Purpose
       ↓
7. Record Prerequisites
       ↓
8. Perform development/configuration
       ↓
9. Record problems
       ↓
10. Record solution
       ↓
11. Test
       ↓
12. Deploy if required
       ↓
13. Update Status
       ↓
14. Record Source URLs
       ↓
15. Create/update documentation
```

---

# 19. Troubleshooting Task Structure

For troubleshooting projects, add more detail.

Example:

```text
Task ID:
SAP-004

Task:
Cloud Connector Connectivity Troubleshooting

Task Type:
Troubleshooting

System:
S/4HANA 2023

Platform:
BTP / Cloud Connector / ABAP

Technology:
Cloud Connector; Destination; HTTPS; OData

Current Issue:
Destination cannot reach backend.

Symptoms:
HTTP 502 / connection refused / DNS error

Investigation:
1. Check Cloud Connector status
2. Check system mapping
3. Check virtual host
4. Check internal host
5. Check port
6. Test backend connectivity
7. Test BTP destination
8. Test application

Root Cause:
<record after investigation>

Solution:
<record after successful fix>

Verification:
<record successful test>

Documentation:
Cloud_Connector_Troubleshooting.md
```

This creates a reusable knowledge base instead of just recording that the problem was fixed.

---

# 20. Final Recommendation

The tracker should serve three purposes simultaneously:

### A. Project Management

```text
What am I working on?
What is blocked?
What is complete?
What is the next step?
```

### B. Learning Management

```text
What did I learn?
What were the prerequisites?
Which SAP topic does this belong to?
Which official source did I use?
```

### C. Technical Knowledge Base

```text
What problem occurred?
Why did it occur?
How was it fixed?
How can I reproduce/test it?
```

The combination is particularly useful for SAP development because a single project can involve:

```text
ABAP
  ↓
CDS
  ↓
RAP
  ↓
OData V4
  ↓
SAPUI5
  ↓
BAS
  ↓
Destination
  ↓
Cloud Connector
  ↓
BTP
  ↓
Fiori Launchpad
  ↓
ABAP/BSP Deployment
```

The tracker can therefore become a long-term **SAP development learning and project history**, rather than only a task list.
