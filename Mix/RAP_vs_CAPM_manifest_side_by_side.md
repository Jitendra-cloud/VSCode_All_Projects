# RAP-Based vs CAPM-Based `manifest.json` — Side-by-Side Comparison

This comparison places the **RAP manifest on the left**, the **description in the middle**, and the **CAPM manifest on the right**.

> These are the values from the two examples provided. RAP can also expose OData V4, and CAP applications can have different manifest configurations.

## 1. Application and service configuration

| RAP Manifest | Description | CAPM Manifest |
|---|---|---|
| `_version: "1.86.0"` | Manifest schema version | `_version: "1.89.0"` |
| `sap.app.id: "project1"` | Application ID / namespace | `sap.app.id: "ns.incidents"` |
| `sourceTemplate.id: "@sap/generator-fiori:lrop"` | Fiori generator template | `sourceTemplate.id: "@sap/generator-fiori:lrop"` |
| `sourceTemplate.version: "1.34.0"` | Generator template version | `sourceTemplate.version: "1.32.0"` |
| `sourceTemplate.toolsId: "a9f7edda-3ec5-4bf5-8760-a391b0fd2731"` | Project-generation identifier | `sourceTemplate.toolsId: "d34379f9-dd18-4023-9e5c-15254937dd86"` |
| `mainService.uri: "/sap/opu/odata/dmo/UI_TRAVEL_PROC_M_O2/"` | Backend OData service URL | `mainService.uri: "/odata/v4/processor/"` |
| `mainService.settings.odataVersion: "2.0"` | OData protocol version | `mainService.settings.odataVersion: "4.0"` |
| `mainService.settings.annotations: ["DMO_UI_TRAVEL_PROC_M_O2_VAN", "annotation"]` | Annotation sources attached to the main service | `mainService.settings.annotations: []` |

## 2. Data sources, metadata, and annotations

| RAP Manifest | Description | CAPM Manifest |
|---|---|---|
| `DMO_UI_TRAVEL_PROC_M_O2_VAN` data source is defined as `ODataAnnotation` | Separate annotation document from the ABAP annotation catalog | Not defined |
| `annotation` data source points to `annotations/annotation.xml` | Local annotation XML file | Not defined |
| `mainService.settings.localUri: "localService/mainService/metadata.xml"` | Local OData metadata snapshot | Not defined |
| `DMO_UI_TRAVEL_PROC_M_O2_VAN.settings.localUri: "localService/mainService/DMO_UI_TRAVEL_PROC_M_O2_VAN.xml"` | Local copy of the annotation document | Not defined |

## 3. UI5 dependencies and libraries

| RAP Manifest | Description | CAPM Manifest |
|---|---|---|
| `minUI5Version: "1.148.10"` | Minimum supported SAPUI5 version | `minUI5Version: "1.152.0"` |
| `sap.m` | Common UI5 controls | `sap.m` |
| `sap.ui.core` | Core UI5 functionality | `sap.ui.core` |
| `sap.ushell` | Launchpad / shell integration library | Not listed |
| `sap.f` | SAP Fiori layout library | Not listed |
| `sap.ui.comp` | Smart controls and related components | Not listed |
| `sap.ui.generic.app` | Generic application framework library used by the V2 descriptor | Not listed |
| `sap.suite.ui.generic.template` | Fiori elements OData V2 Smart Template components | Not listed |
| Not listed | Fiori elements OData V4 templates library | `sap.fe.templates` |

## 4. Model configuration

| RAP Manifest | Description | CAPM Manifest |
|---|---|---|
| Default model `"": { dataSource: "mainService", preload: true }` | Default OData model connected to the main service | Default model `"": { dataSource: "mainService", preload: true }` |
| `defaultBindingMode: "TwoWay"` | Explicit two-way binding mode | Not specified |
| `defaultCountMode: "Inline"` | Count information is requested inline | Not specified |
| `refreshAfterChange: false` | Automatic refresh after changes is disabled | Not specified |
| `metadataUrlParams: { "sap-value-list": "none" }` | Metadata URL parameter for value-list handling | Not specified |
| Not specified | OData V4 operation mode | `operationMode: "Server"` |
| Not specified | Automatically manage `$select` and `$expand` | `autoExpandSelect: true` |
| Not specified | Send early requests | `earlyRequests: true` |
| `bundleName: "project1.i18n.i18n"` | i18n resource bundle namespace | `bundleName: "ns.incidents.i18n.i18n"` |

## 5. Routing and page descriptor

| RAP Manifest | Description | CAPM Manifest |
|---|---|---|
| `sap.ui.generic.app` | Main page descriptor configuration | `sap.ui5.routing` |
| `sap.ui.generic.app._version: "1.3.0"` | Generic application descriptor version | Not applicable / not shown |
| `routes: []` | No explicit routes listed in this example | Two explicit routes are defined |
| `targets: {}` | No explicit routing targets listed in this example | List Report and Object Page component targets are defined |
| `ListReport\|Booking` | List Report page key | `IncidentsList` |
| `entitySet: "Booking"` | Main entity set for the List Report | `contextPath: "/Incidents"` |
| `ObjectPage\|Booking` | Object Page page key nested under the List Report | `IncidentsObjectPage` |
| `entitySet: "Booking"` | Entity set for the Object Page | `contextPath: "/Incidents"` |
| Not specified as an explicit route | Route pattern for the List Report | `:?query:` |
| Not specified as an explicit route | Route pattern for the Object Page | `Incidents({key}):?query:` |

## 6. List Report configuration

| RAP Manifest | Description | CAPM Manifest |
|---|---|---|
| `sap.suite.ui.generic.template.ListReport` | List Report component | `sap.fe.templates.ListReport` |
| `list: true` | Marks the component as a List Report | Not specified |
| `condensedTableLayout: true` | Use condensed table layout | Not specified |
| `smartVariantManagement: true` | Enable Smart Variant Management | `variantManagement: "Page"` |
| `enableTableFilterInPageVariant: true` | Include table filters in the page variant | Not specified |
| `filterSettings.dateSettings.useDateRange: true` | Enable date-range handling in filters | Not specified |
| `tableSettings.type: "ResponsiveTable"` | Table control type | `tableSettings.type: "ResponsiveTable"` |
| Not specified | Navigation from the entity to its Object Page | `navigation.Incidents.detail.route: "IncidentsObjectPage"` |

## 7. Object Page configuration

| RAP Manifest | Description | CAPM Manifest |
|---|---|---|
| `sap.suite.ui.generic.template.ObjectPage` | Object Page component | `sap.fe.templates.ObjectPage` |
| `defaultLayoutTypeIfExternalNavigation: "MidColumnFullScreen"` | Default layout when opening through external navigation | Not specified |
| Not specified | Whether header content is editable | `editableHeaderContent: false` |
| `entitySet: "Booking"` | Object Page entity set | `contextPath: "/Incidents"` |

## 8. Fiori elements and deployment settings

| RAP Manifest | Description | CAPM Manifest |
|---|---|---|
| `sap.ui.generic.app` is present | OData V2 generic application descriptor | Not present |
| Not present | Fiori elements V4 app configuration | `sap.fe` is present |
| Not specified | Enable lazy loading | `sap.fe.app.enableLazyLoading: true` |
| Not present | Public application metadata for SAP BTP | `sap.cloud.public: true` |
| Not present | SAP BTP HTML5 application service name | `sap.cloud.service: "incidentmanagement.service"` |

## 9. Common settings

| RAP Manifest | Description | CAPM Manifest |
|---|---|---|
| `sap.ui.technology: "UI5"` | UI technology | `sap.ui.technology: "UI5"` |
| `sap.ui5.flexEnabled: true` | Enable UI flexibility | `sap.ui5.flexEnabled: true` |
| `contentDensities.compact: true` | Compact density supported | `contentDensities.compact: true` |
| `contentDensities.cozy: true` | Cozy density supported | `contentDensities.cozy: true` |
| `deviceTypes.desktop: true` | Desktop supported | `deviceTypes.desktop: true` |
| `deviceTypes.tablet: true` | Tablet supported | `deviceTypes.tablet: true` |
| `deviceTypes.phone: true` | Phone supported | `deviceTypes.phone: true` |
| `resources.css: []` | No custom CSS resources listed | `resources.css: []` |
| `sap.fiori.registrationIds: []` | No registration IDs listed | `sap.fiori.registrationIds: []` |
| `sap.fiori.archeType: "transactional"` | Fiori application archetype | `sap.fiori.archeType: "transactional"` |

## 10. Main technical differences at a glance

| RAP Manifest | Description | CAPM Manifest |
|---|---|---|
| ABAP service URL under `/sap/opu/odata/` | Backend service endpoint style in these examples | CAP service URL under `/odata/v4/` |
| OData V2 | OData protocol used | OData V4 |
| `sap.suite.ui.generic.template.*` | Fiori elements component family | `sap.fe.templates.*` |
| `sap.ui.generic.app` | Page descriptor approach | `sap.ui5.routing` routes and targets |
| `Booking` | Main business entity in the example | `Incidents` |
| Local metadata and annotation data sources are declared | Metadata / annotation setup shown in manifest | No equivalent data sources are declared in this example |
| V2 model settings such as `defaultCountMode` | Model configuration style | V4 settings such as `operationMode`, `autoExpandSelect`, and `earlyRequests` |
| No `sap.cloud` block shown | BTP deployment metadata shown in manifest | `sap.cloud` block is present |

## 11. Important notes

1. **RAP does not mean OData V2 only.** RAP can expose OData V4 services. This particular RAP manifest points to an OData V2 service.
2. **CAPM does not mean every manifest must look exactly like this.** The CAPM example uses OData V4 and Fiori elements V4 configuration.
3. **Annotations are not exclusive to RAP.** The CAPM snippet simply does not declare separate annotation data sources.
4. **The two examples use different application entities.** `Booking` and `Incidents` are application-specific names, not framework differences.
5. **The snippets as pasted need JSON syntax validation.** The RAP snippet appears to be missing a comma before `sap.ui.generic.app` and has a trailing comma. The CAPM snippet uses unquoted keys and appears to be missing a comma before `sap.cloud`. Ensure the actual `manifest.json` is valid JSON.

---

## 12. Final takeaway

The most important differences between the two supplied manifests are:

- **Service protocol:** RAP example uses OData V2; CAPM example uses OData V4.
- **Fiori elements libraries:** RAP example uses `sap.suite.ui.generic.template`; CAPM example uses `sap.fe.templates`.
- **Page configuration:** RAP example uses `sap.ui.generic.app`; CAPM example uses explicit `sap.ui5.routing` routes and targets.
- **Model settings:** RAP example has V2-oriented model settings; CAPM example has V4-oriented server and auto-expand/select settings.
- **Deployment metadata:** The CAPM example includes a `sap.cloud` section that is not shown in the RAP example.
