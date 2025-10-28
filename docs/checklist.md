## 📋 Project Setup Checklist

### 🔧 Technical Prerequisites for Content Federation for an S/4HANA System
#### S/4HANA System
- [ ] Configure an allowlist to protect your system against clickjacking attacks since the SAP S/4HANA apps are integrated into SAP Build Work Zone using iFrames, you need to configure an allowlist within UCON Framework) ⚪
- [ ] Set customizing parameter `EXPOSURE_SYSTEM_ALIASES_MODE` ⚪
- [ ] Check activation status of `cdm3` (cFLP Exposure) service ⚪

### 🔧 Authorization for working with Content Exposure for an S/4HANA System
#### S/4HANA System
- [ ] Make sure that the user which does the content exposure has the right role and that the page cache is turned on for them:
  - [ ] Assign permissions to run the content exposure (delivered with the role SAP_FLP_ADMIN). ⚪
  - [ ] Go to tab Parameters and make sure that the parameter `/UI2/PAGE_CACHE_OFF` does not show up here. If it does, remove it. ⚪

### 🔧 Connect Your SAP BTP Account to SAP S/4HANA for Content Consumption
#### Cloud Connector
- [ ] Add subaccount in Cloud Connector. ⚪
- [ ] Configure SAP Cloud Connector to trust the SAP S/4HANA system.
  - [ ] Setup trust via allowlist where teh backend is represented by it's X.509 certificate. ⚪
- [ ] Configure access control
- [ ] Add resources
#### BTP Subaccount
- [ ] Create the design-time destination (to fetch the federated content from the content provider system during design-time). ⚪
- [ ] Create the runtime destination (to launch federated applications at runtime). ⚪
