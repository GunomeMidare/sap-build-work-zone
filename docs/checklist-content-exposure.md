## 📋 Project Setup Checklist Content Federation from an S/4HANA System

### 🔧 Technical Prerequisites for Content Federation for an S/4HANA System
> Configure the allow list to enable SAP S/4HANA applications to be run in an iFrame in an SAP Build Work Zone site and set an SAP Fiori launchpad parameter for the exposure of classic apps.
#### S/4HANA System
- [x] Configure an allowlist to protect your system against clickjacking attacks since the SAP S/4HANA apps are integrated into SAP Build Work Zone using iFrames, you need to configure an allowlist within UCON Framework). 🟢
- [x] Set customizing parameter `EXPOSURE_SYSTEM_ALIASES_MODE`. 🟢
- [x] Check activation status of `cdm3` (cFLP Exposure) service (Required for version 1). 🟢
- [x] Activate HTTP Service: `/UI2/FLP_CONTENT_EXPOSURE` via transaction `UCON_HTTP_SERVICES` (Required for version 2). 🟢
- [x] Make sure that ICF node `/sap/bc/http` is activated using transaction SICF (Required for version 2). 🟢

### 🔧 Authorization for working with Content Exposure for an S/4HANA System
#### S/4HANA System
- [x] Make sure that the user which does the content exposure has the right role and that the page cache is turned on for them:
  - [x] Assign permissions to run the content exposure (delivered with the role SAP_FLP_ADMIN). 🟢
  - [x] Go to tab Parameters and make sure that the parameter `/UI2/PAGE_CACHE_OFF` does not show up here. If it does, remove it. 🟢

### 🔧 Connect Your SAP BTP Account to SAP S/4HANA for Content Consumption
> Set up SAP Cloud Connector to give the SAP BTP subaccount access to the SAP S/4HANA system that you configured for content exposure and create runtime and design-time destinations for the SAP S/4HANA system on SAP BTP.
#### Cloud Connector
- [x] Add subaccount in Cloud Connector. 🟢
- [x] Configure SAP Cloud Connector to trust the SAP S/4HANA system. 
  - [x] Setup trust via allowlist where the backend is represented by it's X.509 certificate. 🟢
- [x] Configure access control. 🟢
- [x] Add resources. 🟢
#### BTP Subaccount
- [x] Create the design-time destination (to fetch the federated content from the content provider system during design-time). 🟢
- [x] Create the runtime destination (to launch federated applications at runtime). 🟢

### 🔧 Expose Federation Content from SAP S/4HANA
#### S/4HANA System
- [x] Select SAP Fiori Content for Exposure using transaction `/n/ui2/cdm3_exp_scope`. 🟢
- [x] Expose Content to start exposing the selected roles and their assigned content like apps, catalogs, spaces and pages. 🟢
- [x] Check exposure log. 🟢

### 🔧 Add Federated SAP S/4HANA Roles to Your SAP Build Work Zone Site
> Create a content provider for your SAP S/4HANA system in SAP Build Work Zone provider manager and add the exposed roles to the My Content area and to the Work Zone site, so that end users can access the federated apps and groups.
#### SAP Build Work Zone Application
- [x] Create a new Content Provider. 🟢
  - [x] For version 2: Map alias FLP_STANDALONE to the default runtime destination. 🟢
- [x] Select roles to usage. 🟢
- [x] Check content items in selected roles. 🟢
- [x] Assign roles to site. 🟢
- [x] Assign roles to user. 🟢
- [x] Access the federated content. 🟢
