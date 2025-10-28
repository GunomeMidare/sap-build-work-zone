## 📋 Project Setup Checklist SAP Build Work Zone

### 🔧 Technical Prerequisites for Content Federation for an S/4HANA System
> Configure the allow list to enable SAP S/4HANA applications to be run in an iFrame in an SAP Build Work Zone site and set an SAP Fiori launchpad parameter for the exposure of classic apps.
#### S/4HANA System
- [ ] Configure an allowlist to protect your system against clickjacking attacks since the SAP S/4HANA apps are integrated into SAP Build Work Zone using iFrames, you need to configure an allowlist within UCON Framework) ⚪
- [ ] Set customizing parameter `EXPOSURE_SYSTEM_ALIASES_MODE` ⚪
- [ ] Check activation status of `cdm3` (cFLP Exposure) service ⚪
