# Principal Propagation

## Goal 🎯

The goal of this file is to document how to setup Prinicpal Propagation for **SAP Build Work Zone** with an on-premise SAP S/4HANA Content Provider. Principal Propagation enables seamless SSO by forwarding the authenticated user identity from SAP Build Work Zone (via SAP BTP) through the Cloud Connector to your S/4HANA on-premise system. This ensures users access their role-specific data in embedded Fiori apps without re-authenticatn, while respecting backend authorizations.

## References 📝
- [SAP Help: Configure SSO](https://help.sap.com/docs/build-work-zone-standard-edition/sap-build-work-zone-standard-edition/configure-sso?locale=en-US)
- [Principal Propagation from SAP BTP Applications to On-Premise Systems Using Corporate IdP Tokens](https://help.sap.com/docs/btp/sap-business-technology-platform/configure-principal-propagation-from-btp-applications-to-on-premise-systems-using-corporate-idp-tokens?locale=en-US)
- [How to troubleshoot Cloud Connector principal propagation over HTTPS](https://help.sap.com/docs/SUPPORT_CONTENT/appservices/3361376259.html?locale=en-US)

#### SAP Notes
- [3580783 - Logon Popup Appears in SAP Build Work Zone](https://me.sap.com/notes/3580783/E)
- [3509758 - The Cloud Connector has not been configured for principal propagation. Please configure it or](https://me.sap.com/notes/3509758/E)
- [3416510 - Configure Runtime destination for on-prem solution SAP Build Work Zone, standard edition](https://me.sap.com/notes/3416510/E)
- [3657871 - [Cloud Integration] Principal Propagation for on-premise S4, explained](https://me.sap.com/notes/3657871/E)
- [3209209 - Client Certificate Authentication(X.509) fails with error: 'no applicable certificate mapping rule ](https://me.sap.com/notes/3209209/E)

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
- [ ] Configure the pattern for the Common Name (CN) that is used for the short-lived certificates for principal propagation. ⚪

System Level
- [ ] Make sure the system certificate exists. The SSL handshake between SAP Cloud Connector and the ABAP system is performed through the system certificate. ⚪
- [ ] Configure a CA certificate (or make sure it exists). The SAP Cloud Connector uses the configured CA certificate to issue short-lived certificates for logging on to the same identity in the backend as is logged on to in the cloud.

#### ABAP System
- [ ] Configure the ABAP system to trust the system certificate of SAP Cloud Connector. If the system certificate is signed by CA, then you need to import the certificate into the SSL trust service list. ⚪
- [ ] Set the profile parameter in the instance profile: `icm/HTTPS/verify_client` to `Verify_Client=1`. 🟢
- [ ] Setup Rule Based User mapping – rule-based mapping of certificates is the recommended approach (based on the `CERTRULE` transaction). ⚪
    - [ ] Set the profile parameter in the instance profile: `login/certificate_mapping_rulebased` to `1`. ⚪
- [ ] icm/trusted_reverse_proxy_<x> in S4 must be configured. ⚪
- [ ] Rule Based Certificate Mapping for the user identity must be configured. ⚪
- [ ] Mapped user must has the same, unique identifier. ⚪

#### SAP BTP Subaccount
- [ ] The runtime destination authentication method has been set to `Principal Propagation`. ⚪

#### Identity Provider (IdP)
- [ ] XXX

---

## Setup Principal Propagation 🛠️

### Cloud Connector

##### Set Up Trust for Principal Propagation > Configure Trusted Entities in the Cloud Connector
In this task, you synchronize the SAP BTP subaccount to support principal propagation. By default, the Cloud Connector does not trust any entity that issues tokens for principal propagation. You establish trust to the Identity Authentication service of your SAP BTP subaccount.
- [CC: Setting Up Trust for Principal Propagation](https://help.sap.com/docs/CIAS%20FES%202020/ecb81b5bfce440ca8e7e7c9ad58fcf3a/b559acddd01241c0ab0cce5a61862be3.html?locale=en-US)
- [Set Up Trust](https://help.sap.com/docs/connectivity/sap-btp-connectivity-cf/set-up-trust-for-principal-propagation?locale=en-US)
1. Log on to Cloud Connector.
2. Select the <SCC_SUBACCOUNT_DISPLAY_NAME> subaccount.
3. In the navigation area, choose `Cloud To On-Premise` and open the `Principal Propagation` tab.
4. Choose `Synchronise`.
5. Verify that the SAP BTP subaccount is synchronized and trusted.

##### Configure Access Control
Configure Access Control (HTTP) is a specific configuration step within the Cloud Connector administration interface. It defines the mappings and permissions for HTTP/HTTPS-based communications to on-premise systems. The goal is to specify which on-premise hosts, ports, and URL paths (resources) can be accessed from SAP BTP, while enforcing security controls like authentication and authorization.
In this configuration you define amongst other settings the Principal Type. This determines how user identity (principal) is handled during the request—critical for features like single sign-on. Options include:

| **Principal Type**         | **Description**                                                                 | **Use Case**                                      | **Backend Requirements**                     |
|----------------------------|---------------------------------------------------------------------------------|---------------------------------------------------|----------------------------------------------|
| **None**                   | No identity propagation; requests are anonymous or use shared credentials.      | Public APIs, technical access, non-user-specific | None                                         |
| **X.509 Client Certificate** | Propagates identity via client certificates for certificate-based authentication. | Mutual TLS (mTLS), high-security integrations   | Backend must validate client certs (e.g., STRUST) |
| **Assertion**              | Uses SAML-like assertion tickets (encoded in HTTP headers) for token-based propagation. | SSO with SAP BTP, ABAP, Enterprise Portal       | Trust Cloud Connector CA; parse assertion headers |
| **SNC**                    | SAP-specific secure channel for Kerberos-like propagation.                     | Secure internal SAP-to-SAP communication        | SNC enabled on backend (e.g., Kerberos, NTLM) |

- [Configure Access Control (HTTP)](https://help.sap.com/docs/connectivity/sap-btp-connectivity-cf/configure-access-control-http?locale=en-US)

1. Log on to Cloud Connector.
2. Select the <SCC_SUBACCOUNT_DISPLAY_NAME> subaccount.
3. In the navigation area, choose `Cloud To On-Premise` and open the `Access Control` tab.
4. Open the existing settings
5. Select `Allow Principal Propagation`.
    <img width="684" height="310" alt="image" src="https://github.com/user-attachments/assets/6fa44279-464f-4515-895c-f426661f99ea" />
6. in the next step select as `Authentication Type` select: `X.509 Client Certificate`.

> [!Note]
> Defines what kind of principal is sent to the backend within the HTTP request. For the principal type X.509 Certificate (if a principal is sent from the cloud side), the principal is injected as an HTTP header (SSL_CLIENT_CERT) and forwarded to the backend. There, depending on the backed configuration, it can be used for authentication.

##### Configure Subject Patern
In this task, you configure the pattern for the Common Name (CN) that is used for the short-lived certificates for principal propagation.
- [Configure Subject Patern](https://help.sap.com/docs/connectivity/sap-btp-connectivity-cf/configure-subject-patterns-for-principal-propagation?locale=en-US)
- [CC: Configuring the Principal Propagation Subject Pattern](https://help.sap.com/docs/CIAS%20FES%202020/ecb81b5bfce440ca8e7e7c9ad58fcf3a/bb66d36ae7184a8f811ffb7d9ee3d135.html?locale=en-US)

##### 
- [Initial Configuration (HTTP)](https://help.sap.com/docs/connectivity/sap-btp-connectivity-cf/initial-configuration-http?locale=en-US#loio3f974eae3cba4dafa274ec59f69daba6__section_N1001A_N10011_N10001)

---

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
When a user logs on to an ABAP system via HTTPS with a personal X.509 client certificate, the system must map the certificate to an SAP user ID. With the parameter set to 1, the system evaluates rules you define in transaction CERTRULE instead of looking up the old manual table USREXTID.
- [Creating Rules for Certificate Mapping](https://help.sap.com/docs/SAP_NETWEAVER_AS_ABAP_751_IP/d528eef3dca14679bcb47b069aa17a9d/7c6d4b04370e40319ad790b554aa9a0b.html?version=7.51.0&locale=en-US)

1. Use ABAP Report `RSPFPAR` (For Selective Parameters)
2. Input parameter name: `login/certificate_mapping_rulebased`.
3. Click `Execute`.
4. Verify that the value is `1` (enable rule-based X.509 certificate mapping).

##### Setup Rule Based Certificate Mapping: 
Rule-based certificate mapping (transaction CERTRULE) enables the mapping of users from parts of the subject or the subject alternative name of an X.509 certificate for a given issuer to the user ID or alias of a user master record.
- [S/4HANA: Configuring Rule-Based Certificate Mapping](https://help.sap.com/docs/CIAS%20FES%202020/ecb81b5bfce440ca8e7e7c9ad58fcf3a/c339e699f657460088d5b1290ebf8115.html?locale=en-US)
- [Rule-Based Certificate Mapping](https://help.sap.com/docs/ABAP_PLATFORM_NEW/e815bb97839a4d83be6c4fca48ee5777/c830fd902dc8473b9e59db1576cc784b.html?locale=en-US)
1. Log on to the SAP S/4HANA system and open the Rule-Based Certificate Mapping `CERTRULE` transaction.

---

#### SAP BTP Subaccount
##### Configure Runtime destination for SSO
1. Navigate to the runtime destination.
2. As `Authentication Method` select `Principal Propagation`.
