# Configure Your SAP S/4HANA System for Content Federation

## Goal 🎯

The goal of this file is to document how to configure an S/4HANA System for Content Federation to **SAP Build Work Zone**. 

## References 📝
- 

#### SAP Notes
- [2389051 - ICF service for Clickjacking Framing Protection is not active](https://me.sap.com/notes/2389051/E)
- [2142551 - Whitelist service for Clickjacking Framing Protection in AS ABAP](https://me.sap.com/notes/2142551)


## Prerequisites 📝
- XXXX

---

### Verify Clickjacking Protection Settings 🛠️


Since the SAP S/4HANA apps are integrated into SAP Build Work Zone using iFrames, you need to configure an allowlist to protect your system against clickjacking attacks. The allowlist service is an ABAP-wide service to implement protections. You can manage such allowlist scenarios with the Unified Connectivity Framework (UCON Framework) to optimize the protection of your RFC and HTTP(S) communication against unauthorized access. To allow SAP Build Work Zone to consume data from your SAP S/4HANA system, you should add your BTP account to the allowlist for Clickjacking Framing Protection.

If you do not activate the allowlist, SAP Build Work Zone defaults to a more restrictive clickjacking protection mechanism. It will then only allow framing if the host of the application is part of the same domain as the embedding application, i.e. Work Zone (same origin policy).

> [!Note]
> Clickjacking Framing Protection scenario should be already available in the list, This should be the case the SAP S/4HANA 2023 FPS01 Fully-Activated Appliance.

<img width="1723" height="507" alt="image" src="https://github.com/user-attachments/assets/2e6da797-a23a-4567-a882-74d450b94bb3" />

In this example you see it is currently set to `Logging mode`. This means that connections are only logged, but not checked. With this setting, connectivity from your SAP BTP trial account will work. However, this is not a secure setting. In a productive environment, you would need to add the patterns for your SAP Build Work Zone to the allowlist and then set the scenario to Active check mode.


