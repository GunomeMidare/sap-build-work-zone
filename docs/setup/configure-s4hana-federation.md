
# Configure Your SAP S/4HANA System for Content Federation

## Goal 🎯

The goal of this file is to document how to configure an S/4HANA System for Content Federation to **SAP Build Work Zone**. 

## References 📝
- [Configure Your SAP S/4HANA System for Content Federation](https://developers.sap.com/tutorials/cp-launchpad-federation-prepares4hana.html)

#### SAP Notes
- [2389051 - ICF service for Clickjacking Framing Protection is not active](https://me.sap.com/notes/2389051/E)
- [2142551 - Whitelist service for Clickjacking Framing Protection in AS ABAP](https://me.sap.com/notes/2142551)


## Prerequisites 📝
- Access to UCOCCOCKPIT transaction: `UCON_CHW` HTTP Allowlist Scenario.

---

### Verify Clickjacking Protection Settings 🛠️

Since the SAP S/4HANA apps are integrated into SAP Build Work Zone using iFrames, you need to configure an allowlist to protect your system against clickjacking attacks. The allowlist service is an ABAP-wide service to implement protections. You can manage such allowlist scenarios with the Unified Connectivity Framework (UCON Framework) to optimize the protection of your RFC and HTTP(S) communication against unauthorized access. To allow SAP Build Work Zone to consume data from your SAP S/4HANA system, you should add your BTP account to the allowlist for Clickjacking Framing Protection.

If you do not activate the allowlist, SAP Build Work Zone defaults to a more restrictive clickjacking protection mechanism. It will then only allow framing if the host of the application is part of the same domain as the embedding application, i.e. Work Zone (same origin policy).

> [!Note]
> Clickjacking Framing Protection scenario should be already available in the list, This should be the case the SAP S/4HANA 2023 FPS01 Fully-Activated Appliance.

<img width="1723" height="507" alt="image" src="https://github.com/user-attachments/assets/2e6da797-a23a-4567-a882-74d450b94bb3" />

In this example you see it is currently set to `Logging mode`. This means that connections are only logged, but not checked. With this setting, connectivity from your SAP BTP trial account will work. However, this is not a secure setting. In a productive environment, you would need to add the patterns for your SAP Build Work Zone to the allowlist and then set the scenario to Active check mode.

---

### Set customizing parameter EXPOSURE_SYSTEM_ALIASES_MODE 🛠️
The parameter `EXPOSURE_SYSTEM_ALIASES_MODE` defines how to handle system aliases during content exposure. In an embedded deployment of the SAP Fiori front-end server, all apps run on the same server. Therefore, system aliases can be cleared during exposure. In a hub deployment in contrast, they might come from different back-end systems and each back-end system may have several aliases. Therefore, you need to map these aliases to the runtime destinations manually after creating the content provider.

> [!Important]
> This parameter must only be set in an embedded scenario where the SAP Fiori front-end server is deployed into the AS ABAP of the SAP S/4HANA system. This is the case in the SAP S/4HANA trial system.

#### Prerequisites
- A customizing request

#### Aditional Information
- [3203309 - "Do not make any change" info occurred when change parameter EXPOSURE_SYSTEM_ALIASES_MODE  to CLE](https://me.sap.com/notes/3203309/E)

#### Set the Parameter
1. Start transaction `/n/UI2/FLP_CUS_CO`.
2. Click `New Entries` to create one new entry.
3. Enter the FLP Property ID: `EXPOSURE_SYSTEM_ALIASES_MODE` with property value: `CLEAR`.

    <img width="703" height="333" alt="image" src="https://github.com/user-attachments/assets/ad5e86c1-beb2-4bb8-bfef-437c688b4c60" />

4. Click `Save`.

    <img width="1495" height="534" alt="image" src="https://github.com/user-attachments/assets/60339a1b-b2a4-40ad-b00b-a4e566ac8861" />


### Check activation status of Content service 🛠️
- [3501486 - Launchpad Content Exposed with version 2 not updated](https://me.sap.com/notes/3501486/E)
- 

#### Version 1
- [3602795 - Not able to pull the content to SAP BTP from SAP S/4 syste](https://me.sap.com/notes/3602795/E)
Check the activation status of service `/sap/bc/ui2/cdm3` is activated in the SAP S/4HANA system.
1. Start transaction `SICF`.
2. Enter `cdm3` in `Service Name` and click the `Execute` icon.
3. Verify if the service is active.

#### Version 2
Version 2 uses a HTTP service, you might need to activate it first in the S/4 system using transaction `UCON_HTTP_SERVICES`. The name of the service is `/UI2/FLP_CONTENT_EXPOSURE`.

<img width="1486" height="391" alt="image" src="https://github.com/user-attachments/assets/12558938-a61e-4685-9ba0-e288dec2f974" />


