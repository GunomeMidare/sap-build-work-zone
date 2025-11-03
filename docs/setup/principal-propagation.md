# Principal Propagation

## Goal 🎯

The goal of this file is to document how to setup Prinicpal Propagation for **SAP Build Work Zone** with an on-premise SAP S/4HANA Content Provider. Principal Propagation enables seamless SSO by forwarding the authenticated user identity from SAP Build Work Zone (via SAP BTP) through the Cloud Connector to your S/4HANA on-premise system. This ensures users access their role-specific data in embedded Fiori apps without re-authenticatn, while respecting backend authorizations.

## References 📝
- [SAP Help: Configure SSO](https://help.sap.com/docs/build-work-zone-standard-edition/sap-build-work-zone-standard-edition/configure-sso?locale=en-US)
- [Principal Propagation from SAP BTP Applications to On-Premise Systems Using Corporate IdP Tokens](https://help.sap.com/docs/btp/sap-business-technology-platform/configure-principal-propagation-from-btp-applications-to-on-premise-systems-using-corporate-idp-tokens?locale=en-US)

#### SAP Notes
- [3580783 - Logon Popup Appears in SAP Build Work Zone](https://me.sap.com/notes/3580783/E)
- [3416510 - Configure Runtime destination for on-prem solution SAP Build Work Zone, standard edition](https://me.sap.com/notes/3416510/E)

## Prerequisites 📝
- SAP IAS (Identity Authentication Service) configured as the IdP for BTP.
- Cloud Connector is installed and linked to the BTP subaccount.

---

## Quick Check List 📝
#### Cloud Connector
Subaccount Level
- [ ] Connect Cloud Connector to SAP BTP subaccount. 🟢
- [ ] Set Up Trust for Principal Propagation - Configure Trusted Entities in the Cloud Connector. ⚪
- [ ] Make sure you choose the principal type X.509 Certificate (strict usage) in the corresponding system mapping. ⚪

System Level
- [ ] Make sure the system certificate exists. The SSL handshake between SAP Cloud Connector and the ABAP system is performed through the system certificate. ⚪
- [ ] Configure a CA certificate (or make sure it exists). The SAP Cloud Connector uses the configured CA certificate to issue short-lived certificates for logging on to the same identity in the backend as is logged on to in the cloud.

#### ABAP System
- [ ] Configure the ABAP system to trust the system certificate of SAP Cloud Connector. If the system certificate is signed by CA, then you need to import the certificate into the SSL trust service list. ⚪
- [ ] Set the profile parameter in the instance profile: `icm/HTTPS/verify_client` to `Verify_Client=1`. 🟢
- [ ] Setup Rule Based User mapping – rule-based mapping of certificates is the recommended approach (based on the `CERTRULE` transaction). ⚪

#### SAP BTP Subaccount
- [ ] The runtime destination authentication method has been set to `Principal Propagation`. ⚪

#### Identity Provider (IdP)
- [ ] XXX



---

### Setup Principal Propagation 🛠️

#### Cloud Connector
1. XXXX
2. XXXX

#### ABAP System
##### Set or verify profile parameter `icm/HTTPS/verify_client`:
1. Use ABAP Report `RSPFPAR` (For Selective Parameters)
2. Input parameter name: `icm/HTTPS/verify_client`.
3. Click `Execute`.
4. Verify that the value is `Verify_Client=1`.

> [!Note]
> Client certificate is required — the server demands a valid X.509 certificate from the client (Cloud Connector) during TLS handshake. Cloud Connector must present a valid certificate (short-lived user cert) for Principal Propagation to work.

#### SAP BTP Subaccount
##### Configure Runtime destination for SSO
1. Navigate to the runtime destination.
2. As `Authentication Method` select `Principal Prpagation`.
