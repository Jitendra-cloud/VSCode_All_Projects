# Build a CAP Application

> Source: SAP Developer Center — **Build a CAP Application**  
> URL: https://developers.sap.com/tutorials/build-cap-app  
> Tutorial status: **Out of maintenance**. SAP recommends using the current **Develop a Full-Stack CAP Application Following the SAP BTP Developer’s Guide** mission for an up-to-date version.
>
> This document captures the five steps and the Node.js/Java instructions available on the SAP tutorial page.

## Overview

**Level:** Beginner  
**Estimated time:** 30 minutes

### You will learn

- How to create a CAP project
- How to add a domain model
- How to create services
- How to add data to the database

## Prerequisites

You need SAP Business Application Studio configured.

The original tutorial refers to the **Set Up SAP Business Application Studio for Development** tutorial, which is part of the **Develop a Full-Stack CAP Application Following SAP BTP Developer’s Guide** tutorial group.

---

# Step 1 — Create a CAP Project

You can create a CAP project with either **Node.js** or **Java**. Choose one technology and follow that path consistently.

## Option A — Node.js

### 1. Open the development space

In SAP Business Application Studio:

1. Open your **IncidentManagement** dev space.
2. Make sure the dev space is in **RUNNING** status.

### 2. Open a terminal

Choose:

**Burger menu → Terminal → New Terminal**

### 3. Navigate to the projects folder

From the root directory:

```bash
cd projects
```

The main command-line tool used in CAP is `cds`. It can be used to build, run, and debug CAP applications.

### 4. Create the CAP project

Run:

```bash
cds init --add nodejs incident-management
```

This creates an `incident-management` folder containing the new CAP project.

### 5. Open the project

In SAP Business Application Studio:

1. Choose the **Explorer** icon.
2. Choose **Open Folder**.
3. Enter:

```text
/home/user/projects/
```

4. Select `incident-management`.
5. Choose **OK**.

You can also use:

- macOS: `Cmd+Shift+E`
- Windows/Linux: `Ctrl+Shift+E`

### 6. Open a terminal inside the project

While in the `incident-management` folder:

**Burger menu → Terminal → New Terminal**

### 7. Install dependencies

Run:

```bash
npm install
```

### 8. Start the CAP server

Run:

```bash
cds watch
```

The CAP server watches project files and automatically restarts when files are changed.

For a newly created project, you will initially see a message similar to:

```text
cds serve all --with-mocks --in-memory?
live reload enabled for browsers

___________________________
No models found in db/,srv/,app/,schema,services.
Waiting for some to arrive...
```

At this point the project exists, but it does not yet contain a domain model or service definitions.

---

## Option B — Java

### 1. Open the development space

In SAP Business Application Studio:

1. Open your **IncidentManagement** dev space.
2. Make sure the dev space is in **RUNNING** status.

### 2. Open a terminal

Choose:

**Burger menu → Terminal → New Terminal**

### 3. Navigate to the projects folder

Run:

```bash
cd projects
```

### 4. Create the CAP Java project

Run:

```bash
cds init incident-management --add java
```

This creates an `incident-management` folder containing the CAP Java project.

### 5. Open the project

In SAP Business Application Studio:

1. Choose the **Explorer** icon.
2. Choose **Open Folder**.
3. Enter:

```text
/home/user/projects/
```

4. Select `incident-management`.
5. Choose **OK**.

### 6. Open a terminal inside the project

Choose:

**Burger menu → Terminal → New Terminal**

### 7. Navigate to `srv`

Run:

```bash
cd srv
```

### 8. Start the CAP Java server

Run:

```bash
mvn cds:watch
```

Initially, because no CDS model exists yet, the build reports that no CDS model was found. The watcher remains active and will rebuild when files are added.

---

# Step 2 — Add a Domain Model

Create the domain model in the `db` folder.

## 1. Create `schema.cds`

Create:

```text
db/schema.cds
```

## 2. Add the CDS model

Paste:

```cds
using {
  cuid,
  managed,
  sap.common.CodeList
} from '@sap/cds/common';

namespace sap.capire.incidents;

/**
 * Incidents created by Customers.
 */
entity Incidents : cuid, managed {
  customer     : Association to Customers;
  title        : String @title: 'Title';
  urgency      : Association to Urgency default 'M';
  status       : Association to Status default 'N';
  conversation : Composition of many {
                   key ID        : UUID;
                       timestamp : type of managed : createdAt;
                       author    : type of managed : createdBy;
                       message   : String;
                 };
}

/**
 * Customers entitled to create support Incidents.
 */
entity Customers : managed {
  key ID           : String;
      firstName    : String;
      lastName     : String;
      name         : String = trim(firstName || ' ' || lastName);
      email        : EMailAddress;
      phone        : PhoneNumber;
      incidents    : Association to many Incidents
                       on incidents.customer = $self;
      creditCardNo : String(16) @assert.format: '^[1-9]\d{15}$';
      addresses    : Composition of many Addresses
                       on addresses.customer = $self;
}

entity Addresses : cuid, managed {
  customer      : Association to Customers;
  city          : String;
  postCode      : String;
  streetAddress : String;
}

entity Status : CodeList {
  key code        : String enum {
        new = 'N';
        assigned = 'A';
        in_process = 'I';
        on_hold = 'H';
        resolved = 'R';
        closed = 'C';
      };
      criticality : Integer;
}

entity Urgency : CodeList {
  key code : String enum {
    high = 'H';
    medium = 'M';
    low = 'L';
  };
}

type EMailAddress : String;
type PhoneNumber  : String;
```

## What this model contains

### `Incidents`

Represents incidents created by customers.

It contains:

- Customer association
- Title
- Urgency
- Status
- Conversation composition

### `Customers`

Represents customers who can create support incidents.

It contains:

- ID
- First name
- Last name
- Calculated name
- Email
- Phone
- Credit card number
- Incident association
- Address composition

### `Addresses`

Stores customer address information:

- Customer
- City
- Post code
- Street address

### `Status`

A code list containing:

| Code | Meaning | Criticality |
|---|---|---:|
| N | New | 3 |
| A | Assigned | 2 |
| I | In Process | 2 |
| H | On Hold | 3 |
| R | Resolved | 2 |
| C | Closed | 4 |

### `Urgency`

A code list containing:

| Code | Meaning |
|---|---|
| H | High |
| M | Medium |
| L | Low |

### Common CAP types

The model imports `cuid` and `managed` from `@sap/cds/common`.

- `cuid` provides a unique ID.
- `managed` adds administrative fields such as `createdAt` and `createdBy`.

The calculated `name` field combines first and last name.

## What happens after saving?

With Node.js, the running CAP server detects `schema.cds` automatically and creates an in-memory SQLite database.

Typical output:

```text
[cds] - loaded model from 2 file(s):
  db/schema.cds
  <path-to>/node_modules/@sap/cds/common.cds

[cds] - connect using bindings from: { registry: '~/.cds-services.json' }
[cds] - connect to db > sqlite { url: ':memory:' }
/> successfully deployed to in-memory database.
```

At this point you may still see:

```text
No service definitions found in loaded models.
Waiting for some to arrive...
```

That is expected because services have not been defined yet.

---

# Step 3 — Create Services

CAP encourages creating **single-purpose services**.

This tutorial creates:

- `ProcessorService` — for support personnel who process incidents
- `AdminService` — for administrators

## Node.js

### 1. Create `services.cds`

Create:

```text
srv/services.cds
```

### 2. Add the service definitions

```cds
using {sap.capire.incidents as my} from '../db/schema';

/**
 * Service used by support personell, i.e. the incidents' 'processors'.
 */
service ProcessorService {
    entity Incidents as projection on my.Incidents;

    @readonly
    entity Customers as projection on my.Customers;
}

/**
 * Service used by administrators to manage customers and incidents.
 */
service AdminService {
    entity Customers as projection on my.Customers;
    entity Incidents as projection on my.Incidents;
}
```

### 3. Check the server output

The CAP server should expose the services:

```text
[cds] - serving ProcessorService { path: '/odata/v4/processor' }
[cds] - serving AdminService { path: '/odata/v4/admin' }
[cds] - server listening on { url: 'http://localhost:4004' }
```

The Node.js endpoints are:

```text
http://localhost:4004/odata/v4/processor
http://localhost:4004/odata/v4/admin
```

Open:

```text
http://localhost:4004
```

to see the generic CAP index page.

If necessary, stop the server with:

```text
Ctrl+C
```

and restart it with:

```bash
cds watch
```

---

## Java

For Java, create the same service definitions in:

```text
srv/services.cds
```

Use:

```cds
using { sap.capire.incidents as my } from '../db/schema';

/**
 * Service used by support personell, i.e. the incidents' 'processors'.
 */
service ProcessorService {
    entity Incidents as projection on my.Incidents;

    @readonly
    entity Customers as projection on my.Customers;
}

/**
 * Service used by administrators to manage customers and incidents.
 */
service AdminService {
    entity Customers as projection on my.Customers;
    entity Incidents as projection on my.Incidents;
}
```

The Java server is started with:

```bash
mvn cds:watch
```

The tutorial shows the Java application starting on port `8080`.

Typical important output includes:

```text
Registered service AdminService
Registered service ProcessorService
Tomcat initialized with port 8080
CdsODataV4Servlet mapped to /odata/v4
IndexPageServlet mapped to /
Tomcat started on port 8080
```

In SAP Business Application Studio:

1. Look for the popup indicating that a service is listening on port `8080`.
2. Choose **Open in a New Tab**.
3. The generic `index.html` page should open.

If required, stop the server with `Ctrl+C` and restart with:

```bash
mvn cds:watch
```

---

# Step 4 — Generate CSV Templates

The in-memory SQLite database now exists. The next step is to create CSV templates for initial data.

## 1. Run `cds add data`

From the project root:

```bash
cds add data
```

## 2. Expected output

You should see files similar to:

```text
Adding feature 'data'...
Creating db/data/sap.capire.incidents-Addresses.csv
Creating db/data/sap.capire.incidents-Customers.csv
Creating db/data/sap.capire.incidents-Incidents.csv
Creating db/data/sap.capire.incidents-Incidents.conversation.csv
Creating db/data/sap.capire.incidents-Status.csv
Creating db/data/sap.capire.incidents-Status.texts.csv
Creating db/data/sap.capire.incidents-Urgency.csv
Creating db/data/sap.capire.incidents-Urgency.texts.csv

Successfully added features to your project.
```

The generated templates are located under:

```text
db/data/
```

The tutorial generates eight files:

```text
db/data/sap.capire.incidents-Addresses.csv
db/data/sap.capire.incidents-Customers.csv
db/data/sap.capire.incidents-Incidents.csv
db/data/sap.capire.incidents-Incidents.conversation.csv
db/data/sap.capire.incidents-Status.csv
db/data/sap.capire.incidents-Status.texts.csv
db/data/sap.capire.incidents-Urgency.csv
db/data/sap.capire.incidents-Urgency.texts.csv
```

---

# Step 5 — Fill in the Initial Data

> **Important:** The tutorial distinguishes initial data from test data. Initial data is intended for production configuration/code lists, while test data is intended for development and testing.

Replace the generated CSV templates with the following content.

## 5.1 Addresses

File:

```text
db/data/sap.capire.incidents-Addresses.csv
```

Content:

```csv
ID,customer_ID,city,postCode,streetAddress
17e00347-dc7e-4ca9-9c5d-06ccef69f064,1004155,Rome,00164,Piazza Adriana
d8e797d9-6507-4aaa-b43f-5d2301df5135,1004161,Munich,80809,Olympia Park
ff13d2fa-e00f-4ee5-951c-3303f490777b,1004100,Walldorf,69190,Dietmar-Hopp-Allee
```

## 5.2 Customers

File:

```text
db/data/sap.capire.incidents-Customers.csv
```

Content:

```csv
ID,firstName,lastName,email,phone
1004155,Daniel,Watts,daniel.watts@demo.com,+39-555-123
1004161,Stormy,Weathers,stormy.weathers@demo.com,+49-020-022
1004100,Sunny,Sunshine,sunny.sunshine@demo.com,+49-555-789
```

## 5.3 Incident conversations

File:

```text
db/data/sap.capire.incidents-Incidents.conversation.csv
```

Content:

```csv
ID,up__ID,timestamp,author,message
2b23bb4b-4ac7-4a24-ac02-aa10cabd842c,3b23bb4b-4ac7-4a24-ac02-aa10cabd842c,1995-12-17T03:24:00Z,Harry John,Can you please check if battery connections are fine?
2b23bb4b-4ac7-4a24-ac02-aa10cabd843c,3a4ede72-244a-4f5f-8efa-b17e032d01ee,1995-12-18T04:24:00Z,Emily Elizabeth,Can you please check if there are any loose connections?
9583f982-d7df-4aad-ab26-301d4a157cd7,3583f982-d7df-4aad-ab26-301d4a157cd7,2022-09-04T12:00:00Z,Sunny Sunshine,Please check why the solar panel is broken.
9583f982-d7df-4aad-ab26-301d4a158cd7,3ccf474c-3881-44b7-99fb-59a2a4668418,2022-09-04T13:00:00Z,Bradley Flowers,What exactly is wrong?
```

## 5.4 Incidents

File:

```text
db/data/sap.capire.incidents-Incidents.csv
```

Content:

```csv
ID,customer_ID,title,urgency_code,status_code
3b23bb4b-4ac7-4a24-ac02-aa10cabd842c,1004155,Inverter not functional,H,C
3a4ede72-244a-4f5f-8efa-b17e032d01ee,1004161,No current on a sunny day,H,N
3ccf474c-3881-44b7-99fb-59a2a4668418,1004161,Strange noise when switching off Inverter,M,N
3583f982-d7df-4aad-ab26-301d4a157cd7,1004100,Solar panel broken,H,I
```

## 5.5 Status

File:

```text
db/data/sap.capire.incidents-Status.csv
```

Content:

```csv
code,descr,criticality
N,New,3
A,Assigned,2
I,In Process,2
H,On Hold,3
R,Resolved,2
C,Closed,4
```

## 5.6 Urgency

File:

```text
db/data/sap.capire.incidents-Urgency.csv
```

Content:

```csv
code,descr
H,High
M,Medium
L,Low
```

## 5.7 Translation files

The following generated files are left empty in this tutorial:

```text
db/data/sap.capire.incidents-Status.texts.csv
db/data/sap.capire.incidents-Urgency.texts.csv
```

These files are intended for translated text when localization/translations are added.

---

# Verify the Loaded Data

When the CSV files are detected, the CAP server automatically loads their contents into the database.

For Node.js, you should see output similar to:

```text
[cds] - connect to db > sqlite { database: ':memory:' }
> init from db/data/sap.capire.incidents-Addresses.csv
> init from db/data/sap.capire.incidents-Customers.csv
> init from db/data/sap.capire.incidents-Incidents.csv
> init from db/data/sap.capire.incidents-Incidents.conversation.csv
> init from db/data/sap.capire.incidents-Status.csv
> init from db/data/sap.capire.incidents-Status.texts.csv
> init from db/data/sap.capire.incidents-Urgency.csv
> init from db/data/sap.capire.incidents-Urgency.texts.csv
/> successfully deployed to in-memory database.
```

For Java, the server similarly reports that the CSV data has been loaded into the database.

Make sure the CAP server is running.

### Node.js

```bash
cds watch
```

### Java

```bash
mvn cds:watch
```

---

# Test the OData Services

Once the data is loaded, use the generic service providers to test OData requests.

## Node.js endpoints

### Get all incidents

```text
/odata/v4/processor/Incidents
```

Full local URL:

```text
http://localhost:4004/odata/v4/processor/Incidents
```

### Get customers and their incidents

```text
/odata/v4/processor/Customers?$select=firstName&$expand=incidents
```

Full local URL:

```text
http://localhost:4004/odata/v4/processor/Customers?$select=firstName&$expand=incidents
```

---

## Java endpoints

The tutorial exposes the Java service through the OData V4 application path.

### Get all incidents

```text
/odata/v4/ProcessorService/Incidents
```

### Get customers and their incidents

```text
/odata/v4/ProcessorService/Customers?$select=firstName&$expand=incidents
```

The exact host/port depends on the Java runtime configuration shown when the application starts; the tutorial's BAS setup uses port `8080`.

---

# Expected Result

At the end of the tutorial you have a CAP application containing:

```text
incident-management/
├── db/
│   ├── schema.cds
│   └── data/
│       ├── sap.capire.incidents-Addresses.csv
│       ├── sap.capire.incidents-Customers.csv
│       ├── sap.capire.incidents-Incidents.csv
│       ├── sap.capire.incidents-Incidents.conversation.csv
│       ├── sap.capire.incidents-Status.csv
│       ├── sap.capire.incidents-Status.texts.csv
│       ├── sap.capire.incidents-Urgency.csv
│       └── sap.capire.incidents-Urgency.texts.csv
└── srv/
    └── services.cds
```

The application contains:

- A domain model for incidents, customers, addresses, status, and urgency.
- `ProcessorService`
- `AdminService`
- OData V4 service endpoints.
- Initial CSV data loaded into the development database.
- Associations and compositions between the domain entities.

---

# Complete Command Summary

## Node.js

```bash
cd projects

cds init --add nodejs incident-management

cd incident-management

npm install

cds watch
```

Generate data templates:

```bash
cds add data
```

Start/restart the server:

```bash
cds watch
```

## Java

```bash
cd projects

cds init incident-management --add java

cd incident-management/srv

mvn cds:watch
```

Generate data templates from the project root:

```bash
cds add data
```

Restart the Java CAP server:

```bash
mvn cds:watch
```

---

# Final Checklist

- [ ] SAP Business Application Studio is configured.
- [ ] `IncidentManagement` dev space is running.
- [ ] `incident-management` CAP project is created.
- [ ] Node.js or Java runtime is selected.
- [ ] Dependencies are installed.
- [ ] `db/schema.cds` exists.
- [ ] Domain model is added.
- [ ] `srv/services.cds` exists.
- [ ] `ProcessorService` is available.
- [ ] `AdminService` is available.
- [ ] `cds add data` has been executed.
- [ ] CSV files are populated.
- [ ] CAP server is running.
- [ ] `/Incidents` endpoint returns incident data.
- [ ] Customer/incident `$expand` query returns data.

---

# Important Note About the SAP Tutorial

The SAP page currently marks this tutorial as **OUT OF MAINTENANCE**. The page states that for an up-to-date version and support, you should refer to the **Develop a Full-Stack CAP Application Following the SAP BTP Developer’s Guide** mission in SAP Discovery Center.

Therefore, use this document as a reproduction of the steps available on the referenced tutorial page, but prefer the current SAP mission/documentation when creating a new project with current CAP versions.

## Source

SAP Developer Center:

https://developers.sap.com/tutorials/build-cap-app
