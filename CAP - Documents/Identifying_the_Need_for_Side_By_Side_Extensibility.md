# Identifying the Need for Side-By-Side Extensibility

## Course Context

**Course:** Develop Extensions with CAP Following the SAP BTP Developer's Guide  
**Lesson:** Identifying the Need for Side-By-Side Extensibility

## Learning Objective

After completing this lesson, you will be able to **identify the need for side-by-side extensibility**.

---

## 1. Introduction

Imagine you are a developer working for a company that uses **SAP S/4HANA Cloud**.

The company wants to extend the standard SAP S/4HANA Cloud solution with:

- Custom business logic
- Custom user interfaces (UIs)
- Additional business functionality

In traditional SAP ERP systems, developers could add custom ABAP code directly to the ABAP application server. With cloud solutions such as SAP S/4HANA Cloud, this traditional approach is no longer possible in the same way.

The move to the cloud has changed how SAP solutions are extended.

However, SAP S/4HANA Cloud can still be adapted to meet customer-specific business requirements.

---

## 2. In-App Extensibility vs. Side-by-Side Extensibility

There are different ways to customize and extend an SAP solution.

### In-App / Key-User Extensibility

For relatively simple requirements, SAP provides in-app extensibility options.

For example:

- Adding a custom field
- Adapting a standard SAP Fiori UI

This type of extensibility is useful when the requirement is relatively simple.

### Side-by-Side Extensibility

For more complex requirements, such as:

- Custom business logic
- Custom applications
- More extensive business processes
- Additional user interfaces

a **side-by-side extensibility** approach is appropriate.

The extension is developed outside the S/4HANA core, typically on **SAP Business Technology Platform (SAP BTP)**.

---

## 3. What Is Side-by-Side Extensibility?

Side-by-side extensibility means that the custom extension is developed and operated separately from the core SAP solution.

A simplified architecture is:

```text
+---------------------------+
|     SAP S/4HANA Cloud     |
|                           |
|     Standard Solution     |
+-------------+-------------+
              |
              | APIs
              |
              v
+---------------------------+
|          SAP BTP           |
|                           |
|    Custom Extension       |
|    Custom Business Logic  |
|    Custom UIs             |
+---------------------------+
```

The SAP S/4HANA Cloud system remains the core business system, while the custom application runs on SAP BTP.

---

## 4. SAP Business Technology Platform

**SAP BTP** is a cloud-based development platform focused on business-centric application development and extension.

It provides a cloud-native development environment and capabilities that help developers extend business processes across SAP landscapes and beyond.

SAP BTP provides:

- Development tools
- Runtime environments
- Services
- Infrastructure components
- Security capabilities
- Integration capabilities
- Cloud-native application development capabilities

The goal is to provide a consistent development experience across SAP products and the cloud.

---

## 5. SAP BTP Application Runtimes

SAP BTP provides three main application runtimes for different development scenarios:

1. **SAP BTP, ABAP Environment**
2. **SAP BTP, Cloud Foundry Runtime**
3. **SAP BTP, Kyma Runtime**

These runtimes support different types of application development.

### Simplified View

```text
                         SAP BTP
                           |
          +----------------+----------------+
          |                |                |
          v                v                v
   ABAP Environment   Cloud Foundry       Kyma
          |                |                |
       ABAP/RAP        Cloud-native     Cloud-native
       Extensions       Extensions       Extensions
```

---

## 6. Pro-Code and Low-Code/No-Code

SAP BTP supports different developer profiles.

### Pro-Code

Professional developers can use programming languages and frameworks to build enterprise-grade applications.

Examples include:

- ABAP
- JavaScript / Node.js
- TypeScript
- Java
- Go
- Python
- CAP
- SAP Cloud SDK
- RAP

### Low-Code / No-Code

SAP also provides low-code/no-code capabilities for citizen developers.

The SAP Build portfolio includes:

- **SAP Build Apps**
- **SAP Build Process Automation**
- **SAP Build Work Zone**

These products help create enterprise extensions with less traditional coding.

---

## 7. Fusion Teams

SAP describes a hybrid approach in which professional developers and citizen developers work together.

These collaborative teams are referred to as **Fusion Teams**.

For example:

```text
Professional Developers
        |
        | Collaboration
        v
   Fusion Team
        ^
        | Collaboration
        |
Citizen Developers
```

A Fusion Team can combine:

- Pro-code development
- Low-code/no-code development
- Business expertise
- Technical expertise

This allows organizations to combine different development approaches.

---

## 8. Keep the Core Clean

One of the most important concepts in this lesson is:

> **Keep the core clean.**

The idea is to avoid unnecessary modifications to the core SAP solution.

Instead of putting custom functionality directly into the core system, SAP recommends consuming available **Application Programming Interfaces (APIs)** and integrating them into extension applications.

The general approach is:

```text
SAP S/4HANA Cloud
       |
       | APIs
       v
SAP BTP Extension
       |
       +-- Custom Business Logic
       +-- Custom UI
       +-- Custom Services
       +-- Integrations
```

This separation helps keep the core SAP solution clean while allowing customers to build their own extensions.

---

## 9. Why SAP BTP Is Used for Extensions

SAP BTP provides capabilities for building and operating cloud-native applications.

It provides developers with:

- Development tools
- Services
- Infrastructure
- Runtime environments
- Security capabilities
- Scalability
- Reliability

For example, developers can use:

**SAP Business Application Studio**

as a development environment on SAP BTP.

They can also use the **SAP BTP Kyma runtime** and other BTP services depending on the application architecture.

---

## 10. ABAP-Based Extensions

For **ABAP-based extensions**, the SAP BTP runtime used is:

**SAP BTP, ABAP Environment**

ABAP-based extensions use the:

**ABAP RESTful Application Programming Model (RAP)**

Simplified architecture:

```text
SAP BTP
   |
   v
ABAP Environment
   |
   v
RAP
   |
   v
ABAP Extension
```

---

## 11. Non-ABAP-Based Extensions

For non-ABAP extensions, SAP BTP provides more flexibility in terms of programming languages and runtime environments.

Examples of programming languages include:

- JavaScript / Node.js
- TypeScript
- Java
- Go
- Python

These applications can be developed:

- Locally in an IDE of your choice
- In SAP Business Application Studio

They can then be deployed to:

- SAP BTP, Cloud Foundry Runtime
- SAP BTP, Kyma Runtime

---

## 12. CAP and SAP Cloud SDK

For non-ABAP extensions, SAP highlights technologies such as:

- **SAP Cloud Application Programming Model (CAP)**
- **SAP Cloud SDK**

A simplified comparison is:

```text
Non-ABAP Extensions
        |
        +----------------------+
        |                      |
        v                      v
       CAP              SAP Cloud SDK
        |
        v
   Node.js / Java
```

The exact technology depends on the requirements and architecture of the extension.

---

## 13. What This Learning Journey Uses

This learning journey focuses specifically on:

- **Non-ABAP extensions**
- **SAP Cloud Application Programming Model (CAP)**
- **Node.js / JavaScript**
- **SAP BTP, Cloud Foundry Runtime**

Therefore, the main path for this course is:

```text
SAP S/4HANA Cloud
        |
        | APIs
        v
      SAP BTP
        |
        v
       CAP
        |
        v
   Node.js / JavaScript
        |
        v
 Cloud Foundry Runtime
```

This is the architecture that will be used throughout the CAP learning journey.

---

## 14. Side-by-Side Extensibility — Simple Example

Suppose a company uses SAP S/4HANA Cloud and wants a custom approval application.

The standard S/4HANA system provides the business data.

The company develops a custom approval application on SAP BTP.

The application consumes S/4HANA APIs.

```text
+-----------------------+
|   SAP S/4HANA Cloud   |
|                       |
|   Standard Business   |
|       Processes       |
+-----------+-----------+
            |
            | API
            v
+-----------------------+
|       SAP BTP         |
|                       |
| Custom Approval App   |
|                       |
| Custom UI             |
| Custom Logic          |
+-----------------------+
```

The custom functionality is therefore kept outside the core S/4HANA system.

---

## 15. Important Concepts to Remember

| Concept | Meaning |
|---|---|
| **SAP S/4HANA Cloud** | Core SAP business solution |
| **In-App Extensibility** | Extension/customization inside the SAP application using supported mechanisms |
| **Side-by-Side Extensibility** | Extension developed separately from the SAP core |
| **SAP BTP** | Platform for building and running extensions |
| **Keep the Core Clean** | Avoid unnecessary custom modifications to the core |
| **API** | Interface used to access/integrate capabilities and data |
| **ABAP Environment** | Runtime for ABAP-based extensions |
| **Cloud Foundry** | Runtime for cloud-native applications |
| **Kyma** | Kubernetes-based cloud-native runtime |
| **CAP** | Framework for building cloud applications |
| **SAP Cloud SDK** | Development toolkit for SAP-related cloud applications |
| **SAP Build** | Low-code/no-code development portfolio |
| **Fusion Team** | Collaboration between professional and citizen developers |

---

## 16. Exam / Interview Notes

### Question: Why is side-by-side extensibility needed?

Because complex custom requirements, such as custom business logic and UIs, should be developed outside the SAP S/4HANA Cloud core rather than relying on traditional direct modifications to the application server.

### Question: What is the main idea behind "Keep the Core Clean"?

Keep the standard SAP core as close to the standard product as possible and build custom functionality separately, consuming SAP-provided APIs where appropriate.

### Question: Where are side-by-side extensions commonly developed?

On **SAP BTP**.

### Question: What runtime is used for ABAP-based extensions?

**SAP BTP, ABAP Environment.**

### Question: Which runtimes can be used for non-ABAP extensions?

**SAP BTP, Cloud Foundry Runtime** and **SAP BTP, Kyma Runtime**.

### Question: What does this learning journey focus on?

**CAP + Node.js (JavaScript) + Cloud Foundry.**

---

## 17. Quick Memory Trick

```text
S/4HANA Cloud
     =
   Core

SAP BTP
     =
 Extension Platform

API
     =
 Connection

CAP
     =
 Build Cloud Applications

Side-by-Side
     =
 Custom Extension Outside the Core

Keep the Core Clean
     =
 Avoid Unnecessary Core Modifications
```

---

## Source

SAP Learning: **Identifying the Need for Side-By-Side Extensibility**

https://learning.sap.com/courses/develop-extensions-with-cap-following-the-sap-btp-developer-s-guide/identifying-the-need-for-side-by-side-extensibility_f1e838f0-f02a-43b4-8896-cedc25a7d5d0

This document is a structured study note based on the content of the SAP Learning lesson.
