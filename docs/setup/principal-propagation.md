# Principal Propagation

## Goal 🎯

The goal of this file is to document how to setup Prinicpal Propagation for **SAP Build Work Zone** with an on-premise SAP S/4HANA Content Provider. Principal Propagation enables seamless SSO by forwarding the authenticated user identity from SAP Build Work Zone (via SAP BTP) through the Cloud Connector to your S/4HANA on-premise system. This ensures users access their role-specific data in embedded Fiori apps without re-authenticatn, while respecting backend authorizations.

## References 📝
- [SAP Help: Configure SSO](https://help.sap.com/docs/build-work-zone-standard-edition/sap-build-work-zone-standard-edition/configure-sso?locale=en-US)
- [Principal Propagation from SAP BTP Applications to On-Premise Systems Using Corporate IdP Tokens](https://help.sap.com/docs/btp/sap-business-technology-platform/configure-principal-propagation-from-btp-applications-to-on-premise-systems-using-corporate-idp-tokens?locale=en-US)

#### SAP Notes
- [3580783 - Logon Popup Appears in SAP Build Work Zone](https://me.sap.com/notes/3580783/E)
- [3509758 - The Cloud Connector has not been configured for principal propagation. Please configure it or](https://me.sap.com/notes/3509758/E)
- [3416510 - Configure Runtime destination for on-prem solution SAP Build Work Zone, standard edition](https://me.sap.com/notes/3416510/E)

## Prerequisites 📝
- SAP IAS (Identity Authentication Service) configured as the IdP for BTP.
- Cloud Connector is installed and linked to the BTP subaccount.
- Access to transactions RZ10, STRUST, CERTRULE on the on-premise ABAP system.
- Administrator access to the Cloud Connector.
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
    - [ ] Set the profile parameter in the instance profile: `login/certificate_mapping_rulebased` to `1`. ⚪

#### SAP BTP Subaccount
- [ ] The runtime destination authentication method has been set to `Principal Propagation`. ⚪

#### Identity Provider (IdP)
- [ ] XXX

---

## Setup Principal Propagation 🛠️

### Cloud Connector

##### XXXX
1. XXXX
2. XXXX

### ABAP System

##### Configure an ABAP System to Trust the Cloud Connector's System Certificate:
To enable secure HTTPS communication from the SAP BTP ABAP Environment (or any ABAP system) through the Cloud Connector, the ABAP system must trust the Cloud Connector’s system certificate so it can validate the certificate presented during mutual TLS (mTLS) authentication when opening the tunnel to on-premise systems.
- [Configure Identity Propagation for HTTPS]()
- [S/4HANA: Adding the Certificates to the Trust List](https://help.sap.com/docs/CIAS%20FES%202020/ecb81b5bfce440ca8e7e7c9ad58fcf3a/63005e5ce2a9429db671fda70aa7ad3c.html?locale=en-US)

1. Open the Trust Manager using transaction `STRUST`.
2. Double-click the `SSL-Server Standard` folder in the menu tree on the left.
3. In the displayed screen, click the Import certificate button.
4. In the dialog window, choose the certificate file representing the public key of the issuer of the system certificate, for example, in DER format. Typically, this is a CA certificate. If you decide to use a self-signed system certificate, it is the system certificate itself.
5. The details of this certificate are shown in the section above. Using the example certificate data, you would see CN=MyCompany CA, O=Trust Community, C=DE as subject.
6. If you are sure that you are importing the correct certificate, you can integrate the certificate into the certificate list by choosing the `Add to Certificate List` button.





##### Set or verify profile parameter `icm/HTTPS/verify_client`:
Cloud Connector must present a valid certificate (short-lived user cert) for Principal Propagation to work.
- [S/4HANA: Enabling the Profile Parameters for Rule-based Certificate Mapping](https://help.sap.com/docs/CIAS%20FES%202020/ecb81b5bfce440ca8e7e7c9ad58fcf3a/604e55b0c1c54f259bf48a198dacbc1f.html?locale=en-US)
1. Use ABAP Report `RSPFPAR` (For Selective Parameters)
2. Input parameter name: `icm/HTTPS/verify_client`.
3. Click `Execute`.
4. Verify that the value is `Verify_Client=1`.

> [!Note]
> Client certificate is required — the server demands a valid X.509 certificate from the client (Cloud Connector) during TLS handshake. 

##### Set or verify profile parameter `login/certificate_mapping_rulebased`: 
Rule-based certificate mapping (transaction CERTRULE) enables the mapping of users from parts of the subject or the subject alternative name of an X.509 certificate for a given issuer to the user ID or alias of a user master record.
- [Creating Rules for Certificate Mapping](https://help.sap.com/docs/SAP_NETWEAVER_AS_ABAP_751_IP/d528eef3dca14679bcb47b069aa17a9d/7c6d4b04370e40319ad790b554aa9a0b.html?version=7.51.0&locale=en-US)

1. Use ABAP Report `RSPFPAR` (For Selective Parameters)
2. Input parameter name: `login/certificate_mapping_rulebased`.
3. Click `Execute`.
4. Verify that the value is `1` (enable rule-based X.509 certificate mapping).

> [!Note]
> When a user logs on to an ABAP system via HTTPS with a personal X.509 client certificate, the system must map the certificate to an SAP user ID.
With the parameter set to 1, the system evaluates rules you define in transaction CERTRULE instead of looking up the old manual table USREXTID.

---

#### SAP BTP Subaccount
##### Configure Runtime destination for SSO
1. Navigate to the runtime destination.
2. As `Authentication Method` select `Principal Propagation`.
