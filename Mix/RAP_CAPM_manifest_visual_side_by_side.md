# RAP vs CAPM `manifest.json` — Line-by-Line Side-by-Side

This layout follows the uploaded reference image: **RAP manifest on the left, comparison status in the middle, and CAPM manifest on the right**.

> `...` indicates omitted nested content for readability. The comparison is based only on the two manifest snippets supplied.

| RAP Manifest | Comparison | CAPM Manifest |
|---|:---:|---|
| `{` |  | `{` |
| `"_version": "1.86.0",` | **Different**<br><small>Manifest version differs</small> | `"_version": "1.89.0",` |
| `"sap.app": {` | Both Same | `"sap.app": {` |
| `  "id": "project1",` | **Different**<br><small>Application ID differs</small> | `  "id": "ns.incidents",` |
| `  "type": "application",` | Both Same | `  "type": "application",` |
| `  "i18n": "i18n/i18n.properties",` | Both Same | `  "i18n": "i18n/i18n.properties",` |
| `  "applicationVersion": { "version": "0.0.1" },` | Both Same | `  "applicationVersion": { "version": "0.0.1" },` |
| `  "title": "{{appTitle}}",` | Both Same | `  "title": "{{appTitle}}",` |
| `  "description": "{{appDescription}}",` | Both Same | `  "description": "{{appDescription}}",` |
| `  "resources": "resources.json",` | Both Same | `  "resources": "resources.json",` |
| `  "sourceTemplate": { "id": "@sap/generator-fiori:lrop", "version": "1.34.0", ... },` | **Different**<br><small>Template version and toolsId differ</small> | `  "sourceTemplate": { "id": "@sap/generator-fiori:lrop", "version": "1.32.0", ... },` |
| `  "dataSources": { ... RAP annotation sources and mainService ... }` | **Different**<br><small>RAP declares annotation sources and OData V2 service; CAP declares OData V4 service</small> | `  "dataSources": { "mainService": { "uri": "/odata/v4/processor/", "type": "OData", ... } }` |
| `},` | Both Same | `},` |
| `"sap.ui": {` | Both Same | `"sap.ui": {` |
| `  "technology": "UI5",` | Both Same | `  "technology": "UI5",` |
| `  "icons": { "icon": "", "favIcon": "", "phone": "", "phone@2": "", "tablet": "", "tablet@2": "" },` | Both Same | `  "icons": { "icon": "", "favIcon": "", "phone": "", "phone@2": "", "tablet": "", "tablet@2": "" },` |
| `  "deviceTypes": { "desktop": true, "tablet": true, "phone": true }` | Both Same | `  "deviceTypes": { "desktop": true, "tablet": true, "phone": true }` |
| `},` | Both Same | `},` |
| `"sap.ui5": {` | Both Same | `"sap.ui5": {` |
| `  "flexEnabled": true,` | Both Same | `  "flexEnabled": true,` |
| `  "dependencies": { "minUI5Version": "1.148.10", "libs": { sap.m, sap.ui.core, sap.ushell, sap.f, sap.ui.comp, sap.ui.generic.app, sap.suite.ui.generic.template } },` | **Different**<br><small>Minimum UI5 version and Fiori elements libraries differ</small> | `  "dependencies": { "minUI5Version": "1.152.0", "libs": { sap.m, sap.ui.core, sap.fe.templates } },` |
| `  "contentDensities": { "compact": true, "cozy": true },` | Both Same | `  "contentDensities": { "compact": true, "cozy": true },` |
| `  "models": { ... RAP V2 model settings ... }` | **Different**<br><small>RAP sets TwoWay binding, Inline count, refreshAfterChange and metadataUrlParams; CAP sets Server mode, autoExpandSelect and earlyRequests</small> | `  "models": { ... CAP V4 model settings ... }` |
| `  "resources": { "css": [] },` | Both Same | `  "resources": { "css": [] },` |
| `  "routing": { "config": {}, "routes": [], "targets": {} }` | **Different**<br><small>RAP uses sap.ui.generic.app pages; CAP defines explicit routes and component targets</small> | `  "routing": { "config": {}, "routes": [IncidentsList, IncidentsObjectPage], "targets": { ... } }` |
| `},` | Both Same | `},` |
| `"sap.fiori": { "registrationIds": [], "archeType": "transactional" }` | Both Same | `"sap.fiori": { "registrationIds": [], "archeType": "transactional" }` |
| `Not present` | **Different**<br><small>CAP includes Fiori elements V4 app settings</small> | `"sap.fe": { "app": { "enableLazyLoading": true } }` |
| `Not present` | **Different**<br><small>CAP includes BTP HTML5 application metadata</small> | `"sap.cloud": { "public": true, "service": "incidentmanagement.service" }` |
| `Not present` | **Different**<br><small>RAP has sap.ui.generic.app; CAP does not</small> | `"sap.ui.generic.app": Not present` |
| `"sap.ui.generic.app": { ... ListReport\|Booking and ObjectPage\|Booking ... }` | **Different**<br><small>RAP uses OData V2 generic app descriptor</small> | `Not present (pages are configured under sap.ui5.routing.targets)` |

## Detailed differences to expand

| Area | RAP manifest | CAPM manifest |
|---|---|---|
| Service protocol | OData V2 (`odataVersion: 2.0`) | OData V4 (`odataVersion: 4.0`) |
| Main service URI | `/sap/opu/odata/dmo/UI_TRAVEL_PROC_M_O2/` | `/odata/v4/processor/` |
| UI5 minimum version | `1.148.10` | `1.152.0` |
| Fiori elements libraries | `sap.suite.ui.generic.template` and `sap.ui.generic.app` | `sap.fe.templates` |
| Main entity | `Booking` | `Incidents` |
| Page configuration | `sap.ui.generic.app` | `sap.ui5.routing` routes and targets |
| Default model settings | `defaultBindingMode`, `defaultCountMode`, `refreshAfterChange`, `metadataUrlParams` | `operationMode`, `autoExpandSelect`, `earlyRequests` |
| Annotation sources | OData annotation catalog source plus local annotation XML | No separate annotation data source shown |
| BTP metadata | No `sap.cloud` block shown | `sap.cloud.public` and `sap.cloud.service` |

## Important interpretation

- The supplied RAP manifest uses OData V2, but **RAP can also expose OData V4**. OData version is not determined solely by whether the backend is RAP or CAP.
- The supplied CAPM manifest uses Fiori elements V4 routing and templates. The RAP snippet uses the older OData V2 generic-template descriptor structure.
- The CAPM example does not declare separate annotation data sources; this does not mean CAP cannot use annotations.
- The snippets are examples with different app IDs and entity names; those values are application-specific.
- The pasted snippets should be validated as strict JSON. The RAP snippet appears to be missing a comma before `sap.ui.generic.app`; the CAPM snippet uses unquoted keys and appears to be missing a comma before `sap.cloud`.

## Legend

- **Both Same** — corresponding setting/value is the same in both snippets.
- **Different** — values, configuration, or presence differ.
- **Not present** — the corresponding property is absent from that supplied manifest.
