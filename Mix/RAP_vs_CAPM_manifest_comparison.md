# RAP-Based vs CAPM-Based `manifest.json` Comparison

This document compares the two manifest examples provided:

- **RAP-based:** OData V2 service, `Booking` entity set, and the `sap.ui.generic.app` descriptor structure.
- **CAPM-based:** OData V4 service, `Incidents` entity, and the `sap.fe` routing/target structure.

> **Important:** These are the specific examples supplied, not universal rules. RAP can also use OData V4, and CAP applications can have different manifest configurations.

---

## 1. High-level comparison

| Area | RAP-based manifest | CAPM-based manifest |
|---|---|---|
| Manifest version | `1.86.0` | `1.89.0` |
| Application ID | `project1` | `ns.incidents` |
| Service endpoint | `/sap/opu/odata/dmo/UI_TRAVEL_PROC_M_O2/` | `/odata/v4/processor/` |
| OData version | V2 | V4 |
| Main entity | `Booking` | `Incidents` |
| Fiori elements framework | OData V2 Smart Templates | Fiori elements for OData V4 |
| List Report component | `sap.suite.ui.generic.template.ListReport` | `sap.fe.templates.ListReport` |
| Object Page component | `sap.suite.ui.generic.template.ObjectPage` | `sap.fe.templates.ObjectPage` |
| Main page configuration | `sap.ui.generic.app` | `sap.ui5.routing` targets |
| Fiori elements app settings | Not shown under `sap.fe` | `sap.fe.app.enableLazyLoading` |

## 2. Application and service configuration

| Property | RAP-based | CAPM-based | Explanation |
|---|---|---|---|
| `sap.app.id` | `project1` | `ns.incidents` | Different application IDs/namespaces. |
| `sap.app.sourceTemplate.id` | `@sap/generator-fiori:lrop` | `@sap/generator-fiori:lrop` | Same Fiori generator template. |
| `sap.app.sourceTemplate.version` | `1.34.0` | `1.32.0` | Different generator template versions. |
| `sap.app.sourceTemplate.toolsId` | `a9f7edda-3ec5-4bf5-8760-a391b0fd2731` | `d34379f9-dd18-4023-9e5c-15254937dd86` | Different project-generation identifiers. |
| `sap.app.dataSources.mainService.uri` | `/sap/opu/odata/dmo/UI_TRAVEL_PROC_M_O2/` | `/odata/v4/processor/` | RAP example calls an ABAP OData endpoint; CAPM example calls a CAP service endpoint. |
| `sap.app.dataSources.mainService.settings.odataVersion` | `2.0` | `4.0` | The supplied RAP example uses OData V2; the CAPM example uses OData V4. |
| `sap.app.dataSources.mainService.settings.annotations` | `DMO_UI_TRAVEL_PROC_M_O2_VAN`, `annotation` | Empty array | RAP example lists two annotation sources; CAPM example lists none. |

### RAP annotation and metadata sources

The RAP-based manifest declares additional data sources:

| Data source | Type / URI | Purpose |
|---|---|---|
| `DMO_UI_TRAVEL_PROC_M_O2_VAN` | `ODataAnnotation` | Annotation document retrieved from the ABAP annotation catalog. |
| `annotation` | `annotations/annotation.xml` | Local annotation XML file. |
| `mainService.settings.localUri` | `localService/mainService/metadata.xml` | Local OData metadata snapshot. |
| `DMO_UI_TRAVEL_PROC_M_O2_VAN.settings.localUri` | `localService/mainService/DMO_UI_TRAVEL_PROC_M_O2_VAN.xml` | Local copy of the annotation document. |

The supplied CAPM manifest does not declare these local metadata or annotation data sources.

## 3. UI5 dependencies and libraries

| Property | RAP-based | CAPM-based | Explanation |
|---|---|---|---|
| `sap.ui5.dependencies.minUI5Version` | `1.148.10` | `1.152.0` | CAPM example specifies a higher minimum UI5 version. |
| `sap.m` | Included | Included | Common UI5 controls. |
| `sap.ui.core` | Included | Included | Core UI5 functionality. |
| `sap.ushell` | Included | Not listed | Included in the RAP example. |
| `sap.f` | Included | Not listed | Included in the RAP example. |
| `sap.ui.comp` | Included | Not listed | Included in the RAP example. |
| `sap.ui.generic.app` | Included | Not listed | Used by the OData V2 generic template configuration. |
| `sap.suite.ui.generic.template` | Included | Not listed | Used by the OData V2 Smart Template components. |
| `sap.fe.templates` | Not listed | Included | Used by the OData V4 Fiori elements templates. |

**Key distinction:** The supplied RAP manifest uses the OData V2 Smart Template libraries. The supplied CAPM manifest uses the OData V4 Fiori elements template library.

## 4. Model configuration

Both manifests define an unnamed default model (`""`) connected to `mainService`, and an i18n resource model.

| Property | RAP-based | CAPM-based | Explanation |
|---|---|---|---|
| Default model data source | `mainService` | `mainService` | Same model source name. |
| Default model `preload` | `true` | `true` | Same setting. |
| `defaultBindingMode` | `TwoWay` | Not specified | Explicitly configured only in the RAP example. |
| `defaultCountMode` | `Inline` | Not specified | Explicitly configured only in the RAP example. |
| `refreshAfterChange` | `false` | Not specified | Explicitly configured only in the RAP example. |
| `metadataUrlParams` | `sap-value-list=none` | Not specified | RAP example supplies an OData metadata URL parameter. |
| `operationMode` | Not specified | `Server` | CAPM example explicitly uses server operation mode. |
| `autoExpandSelect` | Not specified | `true` | CAPM example enables automatic `$expand`/`$select` handling. |
| `earlyRequests` | Not specified | `true` | CAPM example enables early requests. |
| i18n bundle | `project1.i18n.i18n` | `ns.incidents.i18n.i18n` | Different application namespaces. |

## 5. Routing and page configuration

### RAP-based: `sap.ui.generic.app`

The RAP example uses the generic application descriptor:

- `sap.ui.generic.app._version`: `1.3.0`
- `pages`: `ListReport|Booking`
- Entity set: `Booking`
- Nested page: `ObjectPage|Booking`

The List Report component is:

```json
{
  "name": "sap.suite.ui.generic.template.ListReport"
}
```

The Object Page component is:

```json
{
  "name": "sap.suite.ui.generic.template.ObjectPage"
}
```

The page settings include:

- `condensedTableLayout: true`
- `smartVariantManagement: true`
- `enableTableFilterInPageVariant: true`
- `filterSettings.dateSettings.useDateRange: true`
- `tableSettings.type: "ResponsiveTable"`
- Object Page `defaultLayoutTypeIfExternalNavigation: "MidColumnFullScreen"`

### CAPM-based: `sap.ui5.routing`

The CAPM example explicitly defines routes and targets.

| Route | Pattern | Target |
|---|---|---|
| `IncidentsList` | `:?query:` | `IncidentsList` |
| `IncidentsObjectPage` | `Incidents({key}):?query:` | `IncidentsObjectPage` |

The List Report target uses:

```json
{
  "name": "sap.fe.templates.ListReport",
  "contextPath": "/Incidents"
}
```

The Object Page target uses:

```json
{
  "name": "sap.fe.templates.ObjectPage",
  "contextPath": "/Incidents"
}
```

The CAPM page settings include:

- `variantManagement: "Page"`
- Navigation from `Incidents` to `IncidentsObjectPage`
- `tableSettings.type: "ResponsiveTable"`
- Object Page `editableHeaderContent: false`

### Routing comparison

| Feature | RAP-based | CAPM-based |
|---|---|---|
| Page descriptor | `sap.ui.generic.app` | `sap.ui5.routing` |
| Explicit routes | Empty `routes` array | List Report and Object Page routes |
| Explicit targets | Empty `targets` object | Component targets for both pages |
| List Report entity configuration | `entitySet: "Booking"` | `contextPath: "/Incidents"` |
| Object Page entity configuration | `entitySet: "Booking"` | `contextPath: "/Incidents"` |
| Navigation | Nested page definition | Routing navigation configuration |
| Table type | `ResponsiveTable` | `ResponsiveTable` |
| Variant management | `smartVariantManagement: true` | `variantManagement: "Page"` |

## 6. Fiori elements and deployment-specific settings

| Property | RAP-based | CAPM-based | Explanation |
|---|---|---|---|
| `sap.ui.generic.app` | Present | Not present | RAP example uses the OData V2 generic application descriptor. |
| `sap.fe` | Not present | Present | CAPM example has Fiori elements V4 app configuration. |
| `sap.fe.app.enableLazyLoading` | Not specified | `true` | Configured only in CAPM example. |
| `sap.cloud.public` | Not present | `true` | CAPM example includes SAP BTP application metadata. |
| `sap.cloud.service` | Not present | `incidentmanagement.service` | CAPM example identifies the HTML5 application/service configuration. |

## 7. Settings that are the same

| Property | RAP-based | CAPM-based |
|---|---|---|
| `sap.ui.technology` | `UI5` | `UI5` |
| `sap.ui5.flexEnabled` | `true` | `true` |
| `sap.ui5.contentDensities.compact` | `true` | `true` |
| `sap.ui5.contentDensities.cozy` | `true` | `true` |
| Desktop device support | `true` | `true` |
| Tablet device support | `true` | `true` |
| Phone device support | `true` | `true` |
| `sap.ui5.resources.css` | Empty array | Empty array |
| `sap.fiori.registrationIds` | Empty array | Empty array |
| `sap.fiori.archeType` | `transactional` | `transactional` |

## 8. Important conceptual differences

### OData V2 versus OData V4

The RAP manifest supplied here points to an OData V2 service, while the CAPM manifest points to an OData V4 service.

This is a difference between these two examples, not a limitation of RAP. RAP can expose OData V4 services as well.

### Fiori elements V2 versus V4

The RAP example uses:

- `sap.suite.ui.generic.template.ListReport`
- `sap.suite.ui.generic.template.ObjectPage`
- `sap.ui.generic.app`

The CAPM example uses:

- `sap.fe.templates.ListReport`
- `sap.fe.templates.ObjectPage`
- Explicit routes and targets in `sap.ui5.routing`

These are different Fiori elements application configuration approaches.

### Metadata and annotations

The RAP example explicitly lists local metadata and annotation files. The CAPM example does not list equivalent files in the supplied manifest. This does not mean CAPM cannot use annotations; annotations may be provided by the service or added through other supported annotation mechanisms.

## 9. Manifest syntax observations

The snippets as pasted should be checked before saving them as `manifest.json`.

- A strict JSON manifest requires quoted property names and string values.
- The RAP snippet appears to be missing a comma before `"sap.ui.generic.app"` and has a trailing comma after the final property.
- The CAPM snippet uses JavaScript-style unquoted keys and appears to be missing a comma before `"sap.cloud"`. It is therefore not strict JSON as pasted.

Use valid JSON syntax in the actual `manifest.json` file.

---

## 10. Summary

| Topic | RAP example | CAPM example |
|---|---|---|
| Backend service | ABAP OData V2 | CAP OData V4 |
| Fiori elements generation style | Generic application descriptor | Explicit routing and targets |
| UI5 template libraries | `sap.suite.ui.generic.template` | `sap.fe.templates` |
| Main entity | `Booking` | `Incidents` |
| Annotation setup shown | External catalog annotation plus local XML | No annotation data source listed |
| Model configuration style | V2 model settings | V4 server/auto-expand settings |
| BTP cloud metadata | Not shown | `sap.cloud` included |

**Bottom line:** The most significant differences are the OData protocol version, the Fiori elements libraries, the page/routing configuration approach, and the model settings. The other differences are largely application-specific values or optional configuration.
