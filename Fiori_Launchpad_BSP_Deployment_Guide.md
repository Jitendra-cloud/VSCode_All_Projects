# SAP Fiori Launchpad --- Add a Deployed UI5 BSP Application

## Purpose

This document records the complete configuration performed to make the
deployed SAPUI5 BSP application **`ZDJP_BSP_TRAVEL`** available and
executable from the SAP Fiori Launchpad.

The configuration was performed step-by-step on an SAP S/4HANA system.

------------------------------------------------------------------------

# 1. Scenario Overview

### UI5 application

  -------------------------------------------------------------------------------------------------------------
  Item                                Value
  ----------------------------------- -------------------------------------------------------------------------
  BSP Application                     `ZDJP_BSP_TRAVEL`

  UI5 Component / Namespace           `djp.travel`

  BSP URL                             `https://s4h2023.sapdemo.com:44303/sap/bc/ui5_ui5/sap/ZDJP_BSP_TRAVEL/`

  Application Type                    SAPUI5 Fiori App
  -------------------------------------------------------------------------------------------------------------

### S/4HANA technical stack verified

  Component     Release
  ----------- ---------
  SAP_BASIS         758
  SAP_UI            758
  SAP_GWFND         758
  S4CORE            108
  S4FND             108

### Relevant authorizations/tools available

  Tool / Transaction   Result
  -------------------- ------------------------------
  `/UI2/FLPD_CUST`     Available
  `/UI2/FLP_CUST`      Not available in this system
  `PFCG`               Available

------------------------------------------------------------------------

# 2. Final Architecture

The configuration created the following chain:

``` text
SAPUI5 Application
      |
      v
BSP Application
ZDJP_BSP_TRAVEL
      |
      v
Fiori Launchpad Target Mapping
Semantic Object: DJPTravel
Action: display
      |
      v
Fiori Launchpad Catalog
ZDJP_BSP_TRAVEL
      |
      v
PFCG Role
Z_DJP_TRAVEL
      |
      v
User
S23A31
      |
      v
Fiori Launchpad
      |
      v
DJP Travel
```

The important Launchpad intent is:

``` text
#DJPTravel-display
```

------------------------------------------------------------------------

# 3. Verify the BSP Application

The BSP application was first verified in **SE80**.

## BSP details

``` text
BSP Application: ZDJP_BSP_TRAVEL
Status:          Active
Application:     ZDJP_BSP_TRAVEL
Package:         ZJS01_FLIGHT_DRAFT
Application Class: /UI5/CL_UI5_BSP_APPLICATION
```

![BSP Application in SE80](screenshots/01_bsp_application_se80.png)

------------------------------------------------------------------------

# 4. Verify the BSP URL

The deployed BSP application was tested directly in the browser.

``` text
https://s4h2023.sapdemo.com:44303/sap/bc/ui5_ui5/sap/ZDJP_BSP_TRAVEL/
```

The browser Network trace showed:

``` text
GET /sap/bc/ui5_ui5/sap/ZDJP_BSP_TRAVEL/
Status: 200
Type:   html
```

This confirms that the BSP endpoint was being served successfully.

The `favicon.ico` request returned HTTP 404, but this was unrelated to
the application.

![BSP Network Request](screenshots/02_bsp_network_200_ok.png)

------------------------------------------------------------------------

# 5. Check S/4HANA System Version

Open:

``` text
System -> Status
```

The system status screen showed:

-   Product Version: see installed software details
-   Database: HDB
-   Server: `s4h2023_A4H_03`

![System Status](screenshots/03_system_status.png)

------------------------------------------------------------------------

# 6. Check Installed Software Components

The installed software details showed:

``` text
SAP_BASIS  758
SAP_ABA    751
SAP_GWFND  758
SAP_UI     758
S4FND      108
S4CORE     108
```

The important components for this configuration were:

``` text
SAP_BASIS 758
SAP_UI    758
SAP_GWFND 758
S4CORE    108
```

![Installed Software
Versions](screenshots/04_installed_software_versions.png)

------------------------------------------------------------------------

# 7. Verify Fiori Launchpad

The Fiori Launchpad was available at:

``` text
https://s4h2023.sapdemo.com:44303/sap/bc/ui2/flp
```

The Launchpad already contained existing applications such as:

-   Maintain Business Partner
-   Display/Maintain Customer Hierarchy

![Fiori Launchpad](screenshots/05_fiori_launchpad.png)

This confirmed that the Launchpad itself was operational before adding
the custom application.

------------------------------------------------------------------------

# 8. Open Fiori Launchpad Designer

The classic customization transaction used was:

``` text
/UI2/FLPD_CUST
```

It opened successfully.

The Launchpad Designer displayed catalogs, tiles and target mappings.

> Note: The system displayed a notice that SAP provides successor tools
> such as Launchpad App Manager and Launchpad Content Manager for many
> Launchpad Designer tasks.

![Fiori Launchpad Designer](screenshots/06_flpd_catalog.png)

------------------------------------------------------------------------

# 9. Create a Customer Catalog

A dedicated catalog was created for the custom application instead of
modifying an existing SAP/test catalog.

In `/UI2/FLPD_CUST`, create a catalog using the **Standard** catalog
type.

### Values

  Field   Value
  ------- -------------------
  Type    `Standard`
  Title   `DJP Travel`
  ID      `ZDJP_BSP_TRAVEL`

Do not select:

``` text
Remote (deprecated)
```

![Create Catalog](screenshots/07_create_catalog.png)

After creation, the catalog initially showed:

``` text
Tiles: 0
Target Mappings: 0
```

------------------------------------------------------------------------

# 10. Create the Target Mapping

The target mapping connects the Launchpad intent to the deployed UI5
application.

Open the new catalog:

``` text
ZDJP_BSP_TRAVEL
```

and create a **Target Mapping**.

![Create Target Mapping](screenshots/08_create_target_mapping.png)

## Intent

  Field             Value
  ----------------- -------------
  Semantic Object   `DJPTravel`
  Action            `display`

The resulting Launchpad intent is:

``` text
#DJPTravel-display
```

## Target

  Field              Value
  ------------------ ----------------------------------------
  Application Type   `SAPUI5 Fiori App`
  Title              `DJP Travel`
  URL                `/sap/bc/ui5_ui5/sap/ZDJP_BSP_TRAVEL/`
  ID                 `djp.travel`

## General

  Field                         Value
  ----------------------------- --------------------------------
  Information                   `DJP Travel Fiori Application`
  Desktop                       Enabled
  Tablet                        Enabled
  Phone                         Enabled
  Allow additional parameters   Enabled

No parameters were required for this application.

### Why these fields matter

#### Semantic Object

``` text
DJPTravel
```

Identifies the navigation/business object used by the Launchpad intent.

#### Action

``` text
display
```

Defines the operation associated with the semantic object.

#### ID

``` text
djp.travel
```

Identifies the SAPUI5 component.

#### URL

``` text
/sap/bc/ui5_ui5/sap/ZDJP_BSP_TRAVEL/
```

Points to the deployed BSP application.

------------------------------------------------------------------------

# 11. Create the App Launcher Tile

A static **App Launcher** tile was created.

![Create Tile](screenshots/09_create_tile.png)

## General

  Field         Value
  ------------- -----------------------------
  Title         `DJP Travel`
  Subtitle      `Travel Application`
  Keywords      Empty
  Icon          `sap-icon://travel-expense`
  Information   `Fiori/UI5 Application`

## Navigation

Enable:

``` text
Use semantic object navigation
```

Then configure:

  Field             Value
  ----------------- -------------
  Semantic Object   `DJPTravel`
  Action            `display`
  Parameters        Empty
  Target URL        Not used

## Tile Actions

Leave the Tile Actions table empty.

### Important relationship

The tile and target mapping must use the same intent:

``` text
DJPTravel + display
```

Therefore:

``` text
Tile
  |
  +-- Semantic Object: DJPTravel
  +-- Action: display
            |
            v
Target Mapping
  |
  +-- Semantic Object: DJPTravel
  +-- Action: display
            |
            v
SAPUI5 Fiori App
  |
  +-- URL: /sap/bc/ui5_ui5/sap/ZDJP_BSP_TRAVEL/
  +-- ID: djp.travel
```

------------------------------------------------------------------------

# 12. Create the PFCG Role

Transaction:

``` text
PFCG
```

A dedicated role was created:

``` text
Z_DJP_TRAVEL
```

Description:

``` text
DJP Travel Fiori Launchpad Role
```

The purpose of this role is to provide the user's Launchpad content and
corresponding authorization profile.

------------------------------------------------------------------------

# 13. Add the Launchpad Catalog to the Role

In:

``` text
PFCG -> Z_DJP_TRAVEL -> Menu
```

the Launchpad catalog was added:

``` text
ZDJP_BSP_TRAVEL
```

The resulting role structure was:

``` text
Z_DJP_TRAVEL
    |
    +-- Launchpad Catalog
            |
            +-- ZDJP_BSP_TRAVEL
```

This connects the Launchpad content to the PFCG role.

------------------------------------------------------------------------

# 14. Assign the User

The user used for testing was:

``` text
S23A31
```

The role was assigned to this user:

``` text
Z_DJP_TRAVEL
        |
        +-- S23A31
```

After the role assignment, the authorization profile was generated.

------------------------------------------------------------------------

# 15. Generate the Authorization Profile

In:

``` text
PFCG -> Z_DJP_TRAVEL -> Authorizations
```

the authorization profile was generated.

SAP provided an automatically generated profile name:

``` text
T-AH960669
```

The default generated profile name was retained.

![Generate Authorization
Profile](screenshots/10_generate_authorization_profile.png)

The role was then saved.

------------------------------------------------------------------------

# 16. Final Test

After the role assignment and authorization profile generation, the user
logged into the Fiori Launchpad again.

The application appeared in Launchpad search as:

``` text
DJP Travel
```

The application was selected and **successfully launched**.

Therefore the complete configuration was verified end-to-end.

------------------------------------------------------------------------

# 17. Troubleshooting Guide

## 17.1 BSP URL returns 404

Verify:

``` text
/sap/bc/ui5_ui5/sap/ZDJP_BSP_TRAVEL/
```

Also verify that the BSP application is active in SE80.

------------------------------------------------------------------------

## 17.2 BSP URL returns 200 but application is blank

Open browser Developer Tools:

``` text
F12 -> Console
F12 -> Network
```

Check for:

``` text
Component.js
manifest.json
Component-preload.js
library-preload.js
```

A missing application resource can cause the UI to remain blank even
though the BSP root request returns HTTP 200.

------------------------------------------------------------------------

## 17.3 Fiori Launchpad opens but app is not visible

Check the complete chain:

``` text
BSP
  |
  v
Catalog
  |
  v
Target Mapping
  |
  v
Tile
  |
  v
PFCG Role
  |
  v
User
```

Verify that the catalog:

``` text
ZDJP_BSP_TRAVEL
```

is in the role:

``` text
Z_DJP_TRAVEL
```

and the role is assigned to the user.

------------------------------------------------------------------------

## 17.4 Tile exists but clicking it does nothing

Check that the tile and target mapping use exactly the same:

``` text
Semantic Object: DJPTravel
Action: display
```

The intent must match:

``` text
#DJPTravel-display
```

------------------------------------------------------------------------

## 17.5 Authorization profile is not generated

In PFCG:

``` text
Z_DJP_TRAVEL
 -> Authorizations
 -> Change Authorization Data
 -> Generate
```

Keep the SAP-generated profile name unless there is a specific
organizational naming requirement.

------------------------------------------------------------------------

## 17.6 User was assigned the role but still cannot see the application

After role assignment:

1.  Save the PFCG role.
2.  Ensure the authorization profile is generated.
3.  Log out of Fiori Launchpad.
4.  Log in again.
5.  Search for `DJP Travel`.

Launchpad content and authorization changes may not be reflected in an
already-established user session immediately.

------------------------------------------------------------------------

# 18. Final Configuration Summary

``` text
BSP Application
ZDJP_BSP_TRAVEL
        |
        | URL
        v
/sap/bc/ui5_ui5/sap/ZDJP_BSP_TRAVEL/
        |
        v
Launchpad Catalog
ZDJP_BSP_TRAVEL
        |
        +----------------------+
        |                      |
        v                      v
Target Mapping              Tile
DJPTravel-display           DJP Travel
        |                      |
        +----------+-----------+
                   |
                   v
             Intent
       #DJPTravel-display
                   |
                   v
             PFCG Role
            Z_DJP_TRAVEL
                   |
                   v
                User
                S23A31
                   |
                   v
          Fiori Launchpad
                   |
                   v
             DJP Travel
                   |
                   v
             UI5 Application
```

------------------------------------------------------------------------

# 19. Important Values to Keep for Future Maintenance

  Object             Value
  ------------------ ----------------------------------------
  BSP Application    `ZDJP_BSP_TRAVEL`
  UI5 Component ID   `djp.travel`
  BSP URL            `/sap/bc/ui5_ui5/sap/ZDJP_BSP_TRAVEL/`
  Catalog            `ZDJP_BSP_TRAVEL`
  Catalog Title      `DJP Travel`
  Semantic Object    `DJPTravel`
  Action             `display`
  Intent             `#DJPTravel-display`
  Tile Title         `DJP Travel`
  PFCG Role          `Z_DJP_TRAVEL`
  Test User          `S23A31`
  FLP URL            `/sap/bc/ui2/flp`
  FLP Customizing    `/UI2/FLPD_CUST`

------------------------------------------------------------------------

# 20. Recommended Configuration Pattern for Future BSP Applications

For another deployed UI5 BSP application, repeat this pattern:

``` text
1. Deploy UI5 application
       |
2. Verify BSP in SE80
       |
3. Test BSP URL directly
       |
4. Verify application loads
       |
5. Create customer Launchpad catalog
       |
6. Create target mapping
       |
7. Create app launcher tile
       |
8. Create PFCG role
       |
9. Add Launchpad catalog to role
       |
10. Assign role to user
       |
11. Generate authorization profile
       |
12. Log out/in
       |
13. Search for application in FLP
       |
14. Test application
```

------------------------------------------------------------------------

# 21. Key Concepts

## BSP

A **BSP Application** is the repository/runtime location used by the
ABAP system to serve the deployed UI5 application.

Example:

``` text
ZDJP_BSP_TRAVEL
```

## Fiori Launchpad

The central shell through which users access Fiori applications.

## Catalog

A collection of Launchpad content such as:

-   Tiles
-   Target mappings

Example:

``` text
ZDJP_BSP_TRAVEL
```

## Tile

The visual application entry shown to the user.

Example:

``` text
DJP Travel
```

## Target Mapping

Defines where a Launchpad intent should navigate.

Example:

``` text
DJPTravel + display
        |
        v
/sap/bc/ui5_ui5/sap/ZDJP_BSP_TRAVEL/
```

## Semantic Object

Represents the navigation object used in the Launchpad intent.

``` text
DJPTravel
```

## Action

Represents the operation:

``` text
display
```

Together:

``` text
DJPTravel-display
```

## PFCG Role

Connects the Launchpad content and authorizations to users.

Example:

``` text
Z_DJP_TRAVEL
```

------------------------------------------------------------------------

# 22. Final Result

The final test proved that:

``` text
BSP application
      +
Launchpad Catalog
      +
Target Mapping
      +
Tile
      +
PFCG Role
      +
User Assignment
      +
Authorization Profile
      =
Working Fiori Launchpad Application
```

The custom **DJP Travel** SAPUI5 application was successfully launched
from the Fiori Launchpad.

------------------------------------------------------------------------

## Screenshots Included

The accompanying `screenshots` folder contains the screenshots captured
during the configuration:

-   `screenshots/01_bsp_application_se80.png`
-   `screenshots/02_bsp_network_200_ok.png`
-   `screenshots/03_system_status.png`
-   `screenshots/04_installed_software_versions.png`
-   `screenshots/05_fiori_launchpad.png`
-   `screenshots/06_flpd_catalog.png`
-   `screenshots/07_create_catalog.png`
-   `screenshots/08_create_target_mapping.png`
-   `screenshots/09_create_tile.png`
-   `screenshots/10_generate_authorization_profile.png`
