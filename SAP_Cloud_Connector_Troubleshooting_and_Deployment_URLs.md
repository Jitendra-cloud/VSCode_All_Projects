# SAP Cloud Connector Troubleshooting & Deployment URL Configuration

## Document Purpose

This document records the troubleshooting procedure used for an SAP S/4HANA on-premise/private backend connected to SAP BTP through SAP Cloud Connector.

It is intentionally divided into two independent sections:

1. **Cloud Connector / BTP Tunnel Troubleshooting**
2. **Adding New SAP Backend URLs/Paths for Fiori/UI5 Deployment and Runtime Access**

The goal is to make the procedure reusable for future SAP Fiori, SAPUI5, RAP, OData, and ABAP Repository deployment projects.

---

# Part A — Cloud Connector Troubleshooting

## 1. Topic

### SAP Cloud Connector connectivity, DNS, tunnel, and BTP destination troubleshooting

## 2. Purpose

Use this section when:

- SAP Cloud Connector shows `Reconnecting`
- Tunnel Information is not connected
- BTP Destination Check Connection fails
- BTP reports that the backend status cannot be determined
- Fiori Generator cannot access an on-premise OData service
- UI5 deployment returns HTTP `503`
- Cloud Connector logs contain DNS errors
- A previously configured DNS suffix/search domain interferes with BTP endpoint resolution

---

## 3. Architecture

Typical flow:

```text
SAPUI5 / Fiori Application
        |
        v
SAP Business Technology Platform
        |
        | Destination
        v
SAP Connectivity Service
        |
        v
SAP Cloud Connector
        |
        | Secure Tunnel
        v
SAP S/4HANA On-Premise / Private System
```

For an on-premise S/4HANA system, Cloud Connector provides the controlled connection between SAP BTP and the internal SAP system.

---

## 4. Important Components

### SAP BTP Destination

The destination defines how BTP accesses the backend.

Typical properties:

```text
Name: S4D_100
Type: HTTP
URL: http://s4h2023:44303
Proxy Type: OnPremise
Authentication: BasicAuthentication
User: <SAP user>
Password: <password>
WebIDEEnabled: true
WebIDEUsage: odata_gen
HTML5.DynamicDestination: true
sap-client: 100
```

Important:

- The destination URL identifies the backend host/port.
- `ProxyType=OnPremise` tells BTP to route through Cloud Connector.
- The destination credentials are SAP backend credentials.
- `sap-client=100` selects SAP client 100 when required.
- Do not change HTTP/HTTPS merely because Cloud Connector uses HTTPS internally. Verify the actual backend configuration before changing it.

---

# 5. Cloud Connector Tunnel Troubleshooting

## Step 1 — Check Cloud Connector connection

Open the Cloud Connector administration UI.

Check:

```text
Subaccount
Region
Region Host
Tunnel Information
```

A healthy connection should show a connected/active state.

A problematic state may look like:

```text
Reconnecting
```

or:

```text
---- Reconnecting
```

If the tunnel is reconnecting, do not immediately troubleshoot SAP authorization. First establish Cloud Connector/BTP connectivity.

---

# 6. Check Cloud Connector Logs

Cloud Connector logs are especially useful when the tunnel cannot be established.

Look for errors involving:

```text
DNS
connectivitynotification
resolve
connection refused
timeout
certificate
tunnel
```

Example DNS error:

```text
Failed to resolve
'connectivitynotification.cf.us10.hana.ondemand.com'
```

This indicates that the Cloud Connector machine cannot resolve the SAP BTP connectivity endpoint.

---

# 7. Determine the Correct BTP Region Endpoint

The endpoint depends on the SAP BTP region.

For the US East (VA) AWS region, the connectivity notification endpoint used in this troubleshooting case was:

```text
connectivitynotification.cf.us10.hana.ondemand.com
```

Always verify the current endpoint from official SAP documentation for the actual BTP region instead of copying an endpoint from an unrelated landscape.

---

# 8. Check Windows DNS Configuration

Run PowerShell:

```powershell
Get-DnsClient | Select-Object InterfaceAlias,ConnectionSpecificSuffix
```

Then:

```powershell
Get-DnsClientGlobalSetting | Select-Object SuffixSearchList
```

The second command is particularly useful for identifying old global DNS suffixes.

Example:

```text
SuffixSearchList
----------------
{goldenabc.local}
```

An old DNS search suffix can cause hostname resolution problems when the machine is trying to resolve SAP BTP endpoints.

---

# 9. Removing an Obsolete Global DNS Suffix

If an old suffix is confirmed to be obsolete and should no longer be used, open **PowerShell as Administrator**.

Run:

```powershell
Set-DnsClientGlobalSetting -SuffixSearchList @()
```

Verify:

```powershell
Get-DnsClientGlobalSetting | Select-Object SuffixSearchList
```

Expected result:

```text
SuffixSearchList
----------------
{}
```

### Important

Do not remove a DNS suffix simply because it is unfamiliar.

First confirm that:

- It belongs to an old environment.
- It is no longer required by the organization.
- It is not required for internal name resolution.

In this troubleshooting case, `goldenabc.local` was identified as an old configuration and removed.

---

# 10. Test DNS Resolution

After changing DNS configuration:

```powershell
nslookup connectivitynotification.cf.us10.hana.ondemand.com
```

A successful result should resolve the hostname to an SAP BTP connectivity endpoint/IP address.

Example:

```text
Name:
connectivity.us10-l-c.uc-live.shoot.live.k8s-hana.ondemand.com

Addresses:
34.198.33.88
98.82.227.152
107.20.252.91

Aliases:
connectivitynotification.cf.us10.hana.ondemand.com
```

### Important

`nslookup` may display a DNS timeout and still subsequently return an answer.

For example:

```text
DNS request timed out.
Name: ...
Addresses: ...
```

This should be investigated, but successful resolution means the hostname was ultimately resolved.

---

# 11. Test TCP/HTTPS Connectivity

Use:

```powershell
Test-NetConnection connectivitynotification.cf.us10.hana.ondemand.com -Port 443
```

Important result:

```text
TcpTestSucceeded : True
```

This confirms that the machine can establish a TCP connection to the endpoint on port 443.

It does not by itself prove that the Cloud Connector tunnel is healthy; it proves network reachability from the Windows machine.

---

# 12. Restart Cloud Connector

After fixing DNS/network problems:

1. Open:

```text
services.msc
```

2. Find:

```text
SAP Cloud Connector
```

3. Restart the service.

4. Wait approximately 1–2 minutes.

5. Open the Cloud Connector administration UI.

6. Check:

```text
Tunnel Information
```

The status should move from:

```text
Reconnecting
```

to a connected/healthy state.

---

# 13. Recheck BTP Destination

Once Cloud Connector is connected:

Open SAP BTP Cockpit:

```text
Connectivity
    >
Destinations
    >
S4D_100
```

Use:

```text
Check Connection
```

A healthy result should indicate that the backend is reachable through Cloud Connector.

If it still fails, continue troubleshooting rather than changing multiple configuration values at once.

---

# 14. Troubleshooting Decision Tree

```text
Cloud Connector = Reconnecting
        |
        v
Check Cloud Connector logs
        |
        +--> DNS resolution error?
        |       |
        |       +--> Check Windows DNS
        |       |
        |       +--> Check DNS suffix/search list
        |       |
        |       +--> nslookup BTP endpoint
        |       |
        |       +--> Test-NetConnection port 443
        |
        +--> Certificate error?
        |       |
        |       +--> Check certificate/trust configuration
        |
        +--> Network timeout?
                |
                +--> Check firewall/proxy/network
```

---

# 15. Common Mistakes

### Mistake 1 — Changing the BTP destination URL immediately

Do not change the destination URL just because Cloud Connector is disconnected.

First establish that Cloud Connector itself is connected.

---

### Mistake 2 — Troubleshooting SAP authorization while the tunnel is down

An HTTP `503` associated with an unavailable backend path is different from an HTTP `403` authorization problem.

General approach:

```text
503 / backend unavailable
    -> check Cloud Connector/network/tunnel

403 / forbidden
    -> check SAP authorization/service permissions
```

---

### Mistake 3 — Assuming every Cloud Connector path must return HTTP 200

A Cloud Connector resource such as:

```text
/sap/opu/odata4/
```

is an allowlist/path definition.

It is not necessarily a complete OData service endpoint.

The actual service path must be tested.

---

# Part B — Adding New URLs / Paths for Deployment and Runtime

## 16. Topic

### Adding SAP backend resource paths to Cloud Connector for SAPUI5/Fiori applications

## 17. Purpose

Use this section when:

- A new OData service is consumed by a Fiori/UI5 application.
- A RAP OData V4 service must be accessed from BTP.
- A UI5 application must be deployed to an ABAP repository.
- A new SAP backend service path is not yet exposed through Cloud Connector.
- A deployment returns an error because the required backend resource is not allowed.

---

# 18. Two Different Purposes Must Be Distinguished

A Fiori/UI5 project can require backend paths for two separate activities:

### A. Runtime service access

The application needs access to an OData service such as:

```text
/sap/opu/odata4/...
```

### B. UI5 application deployment

The deployment tool may need access to:

```text
/sap/opu/odata/UI5/ABAP_REPOSITORY_SRV/
```

These are different purposes.

Do not assume that exposing the OData service automatically exposes the ABAP repository deployment service.

---

# 19. Example RAP OData V4 Service

In this project the RAP service binding was:

```text
ZJS_UI_TRAVEL_D_D_O4
```

Binding type:

```text
OData V4 - UI
```

Service path:

```text
/sap/opu/odata4/sap/zjs_ui_travel_d_d_o4/srvd/sap/zjs_ui_travel_d_d/0001/
```

This is the path required by the application for runtime OData access.

---

# 20. Add the OData V4 Path to Cloud Connector

In Cloud Connector:

```text
Cloud To On-Premise
    >
Access Control
```

Add the relevant resource.

Example:

```text
URL Path:

/sap/opu/odata4/
```

or, when tighter access control is preferred, the specific service path:

```text
/sap/opu/odata4/sap/zjs_ui_travel_d_d_o4/srvd/sap/zjs_ui_travel_d_d/0001/
```

Select:

```text
Path And All Sub-Paths
```

### Security principle

A narrower path is generally easier to control than exposing a broad root unnecessarily.

Use the broad path only when multiple services genuinely need it and organizational security policy permits it.

---

# 21. Test the Actual RAP Service

Do not use only:

```text
/sap/opu/odata4/
```

as proof that the service works.

Test the actual service path:

```text
/sap/opu/odata4/sap/zjs_ui_travel_d_d_o4/srvd/sap/zjs_ui_travel_d_d/0001/
```

The service metadata endpoint is also useful:

```text
/sap/opu/odata4/sap/zjs_ui_travel_d_d_o4/srvd/sap/zjs_ui_travel_d_d/0001/$metadata
```

The exact metadata URL should be tested according to the service's OData V4 configuration.

---

# 22. ABAP Repository Service for UI5 Deployment

For UI5 deployment to the ABAP repository, the deployment process uses:

```text
/sap/opu/odata/UI5/ABAP_REPOSITORY_SRV/
```

Therefore this path may also need to be allowed in Cloud Connector.

Add:

```text
/sap/opu/odata/UI5/ABAP_REPOSITORY_SRV/
```

with:

```text
Path And All Sub-Paths
```

---

# 23. Verify ABAP Repository Service in SAP

In the S/4HANA system, use transaction:

```text
SICF
```

Check:

```text
/UI5/ABAP_REPOSITORY_SRV
```

The service should be active.

A useful direct test is:

```text
/sap/opu/odata/UI5/ABAP_REPOSITORY_SRV/$metadata
```

A successful metadata response demonstrates that the ABAP Repository OData service is available.

---

# 24. Runtime vs Deployment Paths

Keep this distinction for future projects:

| Purpose | Example Path |
|---|---|
| RAP OData V4 runtime | `/sap/opu/odata4/...` |
| Classic OData runtime | `/sap/opu/odata/...` |
| UI5 ABAP repository deployment | `/sap/opu/odata/UI5/ABAP_REPOSITORY_SRV/` |

One application can require more than one of these.

---

# 25. Example Cloud Connector Resource Configuration

For a RAP UI5 application:

```text
Resource 1:
Path:
/sap/opu/odata4/

Access:
Path And All Sub-Paths

Purpose:
RAP OData V4 runtime
```

```text
Resource 2:
Path:
/sap/opu/odata/UI5/ABAP_REPOSITORY_SRV/

Access:
Path And All Sub-Paths

Purpose:
UI5 application deployment to ABAP repository
```

For tighter control, replace the broad OData V4 path with the exact RAP service path.

---

# 26. Deployment Architecture

Typical deployment flow:

```text
BAS / VS Code
      |
      | npm run build
      v
UI5 Application
      |
      | Deployment
      v
BTP Destination
      |
      v
Cloud Connector
      |
      v
ABAP_REPOSITORY_SRV
      |
      v
SAP S/4HANA ABAP Repository
      |
      v
BSP Application
```

Example BSP application:

```text
ZDJP_BSP_TRAVEL
```

---

# 27. Deployment Troubleshooting Sequence

Use this order.

### Step 1 — Build

Run:

```powershell
npm run build
```

Confirm:

```text
Build succeeded
Archive created
```

Build warnings should be evaluated separately from deployment errors.

---

### Step 2 — Confirm Cloud Connector

Check:

```text
Tunnel Information
```

It must be connected.

---

### Step 3 — Confirm Destination

Check:

```text
S4D_100
```

Verify:

```text
Proxy Type = OnPremise
```

and correct backend URL/credentials.

---

### Step 4 — Confirm ABAP Repository Path

Cloud Connector must allow:

```text
/sap/opu/odata/UI5/ABAP_REPOSITORY_SRV/
```

---

### Step 5 — Confirm SICF

Check:

```text
/UI5/ABAP_REPOSITORY_SRV
```

---

### Step 6 — Test Metadata

Test:

```text
/sap/opu/odata/UI5/ABAP_REPOSITORY_SRV/$metadata
```

---

### Step 7 — Deploy

Run:

```powershell
npm run deploy
```

---

# 28. Understanding Common HTTP Errors

## HTTP 503

Generally investigate connectivity/backend availability first.

Check:

```text
Cloud Connector
DNS
Tunnel
Destination
Backend availability
```

---

## HTTP 403

Investigate authorization.

Possible areas include:

```text
SAP user authorization
ABAP repository authorization
Transport/package authorization
Service authorization
```

Use SAP authorization tracing tools such as SU53 when appropriate.

Do not jump to authorization troubleshooting while the Cloud Connector tunnel itself is disconnected.

---

## HTTP 404

Investigate:

```text
Incorrect URL path
Inactive SICF service
Incorrect OData service path
Incorrect service version/path
```

---

# 29. Adding a New Backend URL — Reusable Procedure

Whenever a new SAP service is required:

### Step 1

Identify the exact SAP service URL/path.

Example:

```text
/sap/opu/odata4/sap/<service>/srvd/sap/<service-definition>/<version>/
```

### Step 2

Determine the purpose:

```text
Runtime OData
```

or:

```text
Deployment
```

### Step 3

Add the resource to Cloud Connector.

### Step 4

Select the appropriate sub-path option.

### Step 5

Save the configuration.

### Step 6

Test the actual backend resource.

### Step 7

Test the BTP destination.

### Step 8

Test the application or deployment.

---

# 30. Recommended Troubleshooting Order

Always isolate the layers.

```text
Layer 1
Windows DNS
        |
        v
Layer 2
Internet / TCP 443
        |
        v
Layer 3
Cloud Connector -> BTP
        |
        v
Layer 4
Cloud Connector -> SAP backend
        |
        v
Layer 5
BTP Destination
        |
        v
Layer 6
SAP Service / SICF
        |
        v
Layer 7
SAP Authorization
        |
        v
Layer 8
Fiori/UI5 application
```

This prevents changing several unrelated configurations at the same time.

---

# 31. Quick Command Reference

## Check DNS suffix

```powershell
Get-DnsClientGlobalSetting | Select-Object SuffixSearchList
```

## Remove obsolete global suffix

Run as Administrator:

```powershell
Set-DnsClientGlobalSetting -SuffixSearchList @()
```

## Resolve BTP endpoint

```powershell
nslookup connectivitynotification.cf.us10.hana.ondemand.com
```

## Test HTTPS/TCP

```powershell
Test-NetConnection connectivitynotification.cf.us10.hana.ondemand.com -Port 443
```

## Check DNS adapter configuration

```powershell
Get-DnsClient | Select-Object InterfaceAlias,ConnectionSpecificSuffix
```

## Build UI5 application

```powershell
npm run build
```

## Deploy UI5 application

```powershell
npm run deploy
```

---

# 32. Important Lessons from This Troubleshooting Case

1. A Cloud Connector `Reconnecting` state must be investigated before changing application configuration.
2. Cloud Connector logs can reveal DNS problems that are not visible in the BTP destination itself.
3. An old Windows DNS search suffix can interfere with hostname resolution.
4. Removing an obsolete suffix requires Administrator privileges.
5. `nslookup` verifies DNS resolution.
6. `Test-NetConnection ... -Port 443` verifies TCP reachability.
7. A successful TCP test does not automatically mean Cloud Connector is connected.
8. Cloud Connector must be checked again after network/DNS corrections.
9. OData runtime paths and UI5 deployment paths are separate resources.
10. `/sap/opu/odata4/` is a resource path, not necessarily a complete service endpoint.
11. RAP OData V4 services normally use `/sap/opu/odata4/...`.
12. UI5 ABAP repository deployment uses `/sap/opu/odata/UI5/ABAP_REPOSITORY_SRV/`.
13. HTTP 503 and HTTP 403 should be investigated at different layers.
14. Always identify the exact service path before adding a Cloud Connector resource.
15. Prefer the narrowest resource path that satisfies the application's actual requirement, subject to organizational security policy.

---

# 33. Future Checklist

## Cloud Connector health

- [ ] Cloud Connector service running
- [ ] BTP subaccount connected
- [ ] Correct BTP region
- [ ] Tunnel status connected
- [ ] No DNS errors in logs
- [ ] No certificate errors
- [ ] No network timeout

## Windows network

- [ ] DNS works
- [ ] Correct DNS suffix configuration
- [ ] BTP hostname resolves
- [ ] TCP 443 succeeds

## BTP Destination

- [ ] Correct destination name
- [ ] Correct backend URL
- [ ] `ProxyType=OnPremise`
- [ ] Correct authentication
- [ ] Correct SAP client
- [ ] Destination Check Connection succeeds

## Runtime service

- [ ] Correct OData service path
- [ ] Cloud Connector resource exists
- [ ] Correct sub-path setting
- [ ] SAP service active
- [ ] `$metadata` works

## UI5 deployment

- [ ] Build succeeds
- [ ] ABAP Repository service active
- [ ] `/sap/opu/odata/UI5/ABAP_REPOSITORY_SRV/` allowed
- [ ] Destination available
- [ ] Cloud Connector connected
- [ ] SAP user has required authorization
- [ ] Package and transport are correct
- [ ] Deployment succeeds

---

# 34. Official Documentation to Consult

Use official SAP documentation as the primary source for current configuration details.

- SAP Help Portal — SAP Connectivity / Cloud Connector documentation
- SAP Help Portal — SAP BTP Connectivity prerequisites
- SAP Help Portal — Troubleshooting Cloud Connector tunnel connectivity
- SAPUI5 documentation — Deployment and ABAP repository integration
- SAP Developers — SAPUI5 and Fiori tutorials
- SAP Learning — Developing SAPUI5 Applications

Always verify region-specific endpoints and current Cloud Connector procedures against the current SAP documentation because SAP BTP infrastructure and supported configuration can change.

---

# 35. Document Maintenance

When a future troubleshooting incident occurs, append:

```text
Date:
SAP BTP Region:
Cloud Connector Version:
S/4HANA Version:
Destination:
Problem:
Error Message:
Root Cause:
Commands Used:
Configuration Changed:
Verification:
Final Solution:
```

This creates a reusable SAP Fiori/UI5 troubleshooting knowledge base instead of solving each incident from scratch.
