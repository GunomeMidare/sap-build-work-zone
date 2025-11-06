# Principal Propagation

## Goal 🎯

The goal of this file is to document how to setup Prinicpal Propagation for **SAP Build Work Zone** with an on-premise SAP S/4HANA Content Provider. Principal Propagation enables seamless SSO by forwarding the authenticated user identity from SAP Build Work Zone (via SAP BTP) through the Cloud Connector to your S/4HANA on-premise system. This ensures users access their role-specific data in embedded Fiori apps without re-authenticatn, while respecting backend authorizations.

## References 📝
- [SAP Help: Configure SSO](https://help.sap.com/docs/build-work-zone-standard-edition/sap-build-work-zone-standard-edition/configure-sso?locale=en-US)
- [Principal Propagation from SAP BTP Applications to On-Premise Systems Using Corporate IdP Tokens](https://help.sap.com/docs/btp/sap-business-technology-platform/configure-principal-propagation-from-btp-applications-to-on-premise-systems-using-corporate-idp-tokens?locale=en-US)
- [How to troubleshoot Cloud Connector principal propagation over HTTPS](https://help.sap.com/docs/SUPPORT_CONTENT/appservices/3361376259.html?locale=en-US)
- [Explaining the Authentication Flow of SAP Build Work Zone](https://learning.sap.com/learning-journeys/implement-and-administer-sap-build-work-zone/explaining-the-authentication-flow-of-sap-build-work-zone)
- 

#### SAP Notes General
- [3580783 - Logon Popup Appears in SAP Build Work Zone](https://me.sap.com/notes/3580783/E)
- [3509758 - The Cloud Connector has not been configured for principal propagation. Please configure it or](https://me.sap.com/notes/3509758/E)
- [3416510 - Configure Runtime destination for on-prem solution SAP Build Work Zone, standard edition](https://me.sap.com/notes/3416510/E)
- [3657871 - [Cloud Integration] Principal Propagation for on-premise S4, explained](https://me.sap.com/notes/3657871/E)
- [3209209 - Client Certificate Authentication(X.509) fails with error: 'no applicable certificate mapping rule ](https://me.sap.com/notes/3209209/E)
- [2462533 - Configuring Principal Propagation to an ABAP System for HTTPS in SAP Business Technology Pl](https://me.sap.com/notes/2462533/E)

#### Troubleshooting
- [2333324 - Tutorial - "Tracing and troubleshooting security events in http communication with the AS ABAP" [VIDEO]](https://me.sap.com/notes/0002333324)

## Prerequisites 📝
- SAP IAS (Identity Authentication Service) configured as the IdP for BTP.
- Cloud Connector is installed and linked to the BTP subaccount.
- Access to transactions RZ10, STRUST, CERTRULE, SMICM on the on-premise ABAP system.
- Administrator access to the Cloud Connector.
- Authorization restart Internet Communication Manager (ICM) to enable the ICM parameters.
  
---

## Quick Check List 📝
#### Cloud Connector
System Level
- [ ] Make sure the system certificate exists. The SSL handshake between SAP Cloud Connector and the ABAP system is performed through the system certificate. ⚪
- [ ] Configure a CA certificate (or make sure it exists). The SAP Cloud Connector uses the configured CA certificate to issue short-lived certificates for logging on to the same identity in the backend as is logged on to in the cloud.

Subaccount Level
- [ ] Connect Cloud Connector to SAP BTP subaccount. 🟢
- [ ] Set Up Trust for Principal Propagation - Configure Trusted Entities in the Cloud Connector. ⚪
- [ ] Make sure you choose the principal type X.509 Certificate (strict usage) in the corresponding system mapping. ⚪
- [ ] Configure the pattern for the Common Name (CN) that is used for the short-lived certificates for principal propagation. ⚪

#### ABAP System
- [ ] Configure the ABAP system to trust the system certificate of SAP Cloud Connector. If the system certificate is signed by CA, then you need to import the certificate into the SSL trust service list. ⚪
- [ ] Set the profile parameter in the instance profile: `icm/HTTPS/verify_client` to `Verify_Client=1`. 🟢
- [ ] Setup Rule Based User mapping – rule-based mapping of certificates is the recommended approach (based on the `CERTRULE` transaction). ⚪
    - [ ] Set the profile parameter in the instance profile: `login/certificate_mapping_rulebased` to `1`. ⚪
- [ ] Set the profile parameter `icm/trusted_reverse_proxy_<x>` with values of the Cloud Connector system certificate imported in STRUST. ⚪
- [ ] Rule Based Certificate Mapping for the user identity must be configured. ⚪
- [ ] Mapped user must has the same, unique identifier. ⚪

#### SAP BTP Subaccount
- [ ] The runtime destination authentication method has been set to `Principal Propagation`. ⚪

#### Identity Provider (IdP)
- [ ] XXX

---

## Setup Principal Propagation 🛠️

### Cloud Connector - System Level

#### Install Cloud Connector System Certificate
To set up a mutual authentication between the Cloud Connector and any backend system it connects to, you can import an X.509 client certificate into the Cloud Connector. The Cloud Connector then uses the so-called system certificate for all HTTPS requests to backends that request or require a client certificate. The certificate authority (CA) that signed the Cloud Connector's system certificate must be trusted by all backend systems to which the Cloud Connector is supposed to connect.
- [Initial Configuration (HTTP)](https://help.sap.com/docs/connectivity/sap-btp-connectivity-cf/initial-configuration-http?locale=en-US#loio3f974eae3cba4dafa274ec59f69daba6__section_N1001A_N10011_N10001)

#### Configure a CA certificate (or make sure it exists)
Install and configure an X.509 certificate to enable support for principal propagation in the Cloud Connector.
- [Configure a CA Certificate](https://help.sap.com/docs/connectivity/sap-btp-connectivity-cf/configure-ca-certificate-for-principal-propagation?locale=en-US)
- [3390881 - SAP Cloud Connector: "No CA certificate available, which is required for supporting Principal ](https://me.sap.com/notes/3390881/E)

### Cloud Connector - Subaccount Level

####  Set Up Trust for Principal Propagation > Configure Trusted Entities in the Cloud Connector
In this task, you synchronize the SAP BTP subaccount to support principal propagation. By default, the Cloud Connector does not trust any entity that issues tokens for principal propagation. You establish trust to the Identity Authentication service of your SAP BTP subaccount.
- [CC: Setting Up Trust for Principal Propagation](https://help.sap.com/docs/CIAS%20FES%202020/ecb81b5bfce440ca8e7e7c9ad58fcf3a/b559acddd01241c0ab0cce5a61862be3.html?locale=en-US)
- [Set Up Trust](https://help.sap.com/docs/connectivity/sap-btp-connectivity-cf/set-up-trust-for-principal-propagation?locale=en-US)
1. Log on to Cloud Connector.
2. Select the <SCC_SUBACCOUNT_DISPLAY_NAME> subaccount.
3. In the navigation area, choose `Cloud To On-Premise` and open the `Principal Propagation` tab.
4. Choose `Synchronise`.
5. Verify that the SAP BTP subaccount is synchronized and trusted.

    Example:
    <img width="1277" height="380" alt="image" src="https://github.com/user-attachments/assets/f4ccc11c-c2f2-4b49-a61c-e20f76ba97fb" />

####  Configure Access Control
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

#### Configure Subject Patern
In this task, you configure the pattern for the Common Name (CN) that is used for the short-lived certificates for principal propagation.
- [Configure Subject Patern](https://help.sap.com/docs/connectivity/sap-btp-connectivity-cf/configure-subject-patterns-for-principal-propagation?locale=en-US)
- [CC: Configuring the Principal Propagation Subject Pattern](https://help.sap.com/docs/CIAS%20FES%202020/ecb81b5bfce440ca8e7e7c9ad58fcf3a/bb66d36ae7184a8f811ffb7d9ee3d135.html?locale=en-US)

1. Log on to the Cloud Connector .
2. In the navigation area, choose `Configuration` and open the `On Premise` tab.
3. Scroll to the `Pricipal Propagation` section and choode `Edit`.
4. On the Edit Pricipal Propagation screen, enter the following values:

| Setting | Value | Notes |
|-------|--------|-------|
| **Common Name (CN)** | `${email}` |  |
| **Expiration Tolerance (h)** | `2` | The length of time in hours, that an application can use a principal issued for a user after the token from cloud side has expired. The minimum value for this attribute is 0, and the maximum 336 (14 days). |
| **Certificate Validity (min)** | `60` | The length of time in minutes, that a certificate generated for principal propagation can authenticate against the back end. You can reuse a previously generated certificate to improve performance. The minimum value for this attribute is 0, and the maximum 43200 (30 days).  | 

5. Click `Save`.

    Example:
    <img width="1513" height="178" alt="image" src="https://github.com/user-attachments/assets/96fc7913-7e19-472a-a5d6-ed14a93d672c" />


#### Create & Download Short-Lived Sample Certificate
By choosing Generate Sample Certificate you can create a sample certificate that looks like one of the short-lived certificates created at runtime. You can use this certificate to, for example, generate user mapping rules in the target system, via transaction CERTRULE in an ABAP system. If your subject pattern contains variable fields, a wizard lets you provide meaningful values for each of them and eventually you can save the sample certificate in DER format. This certificate is not the actual short-lived certificate used in runtime (which is dynamically generated per user session); instead, it's a sample for configuration purposes. 
- [CC: Exporting the Principal Propagation Sample Certificate](https://help.sap.com/docs/CIAS%20FES%202020/ecb81b5bfce440ca8e7e7c9ad58fcf3a/283f6f6942244a4fbe15a44f18ca081b.html?locale=en-US)


1. Click `Generate Sample Certificate`.

    Example:
    <img width="1267" height="286" alt="image" src="https://github.com/user-attachments/assets/6d140b4b-cbf2-4943-9c95-72ab2ff0e3e9" />

---

### ABAP System

#### Configure an ABAP System to Trust the Cloud Connector's System Certificate:
To enable secure HTTPS communication from the SAP BTP ABAP Environment (or any ABAP system) through the Cloud Connector, the ABAP system must trust the Cloud Connector’s system certificate so it can validate the certificate presented during mutual TLS (mTLS) authentication when opening the tunnel to on-premise systems.
- [Configure Identity Propagation for HTTPS]()
- [S/4HANA: Adding the Certificates to the Trust List](https://help.sap.com/docs/CIAS%20FES%202020/ecb81b5bfce440ca8e7e7c9ad58fcf3a/63005e5ce2a9429db671fda70aa7ad3c.html?locale=en-US)

1. Open the Trust Manager using transaction `STRUST`.
2. Double-click the `SSL-Server Standard` folder in the menu tree on the left.
3. In the displayed screen, click the Import certificate button.
4. In the dialog window, choose the certificate file representing the public key of the issuer of the system certificate, for example, in DER format. Typically, this is a CA certificate. If you decide to use a self-signed system certificate, it is the system certificate itself.
5. The details of this certificate are shown in the section above. Using the example certificate data, you would see CN=MyCompany CA, O=Trust Community, C=DE as subject.
6. If you are sure that you are importing the correct certificate, you can integrate the certificate into the certificate list by choosing the `Add to Certificate List` button.


#### Set or verify profile parameter `icm/HTTPS/verify_client`:
Cloud Connector must present a valid certificate (short-lived user cert) for Principal Propagation to work.
- [S/4HANA: Enabling the Profile Parameters for Rule-based Certificate Mapping](https://help.sap.com/docs/CIAS%20FES%202020/ecb81b5bfce440ca8e7e7c9ad58fcf3a/604e55b0c1c54f259bf48a198dacbc1f.html?locale=en-US)
1. Use ABAP Report `RSPFPAR` (For Selective Parameters)
2. Input parameter name: `icm/HTTPS/verify_client`.
3. Click `Execute`.
4. Verify that the value is `Verify_Client=1`.

> [!Note]
> Client certificate is required — the server demands a valid X.509 certificate from the client (Cloud Connector) during TLS handshake. 

#### Set or verify profile parameter `icm/trusted_reverse_proxy_<x>`: 
In SAP Cloud Connector scenarios, principal propagation relies on the X.509 client certificate (carrying the user's identity) being forwarded from SAP Build Work Zone to an on-premise ABAP system. The Cloud Connector acts as a reverse proxy for this tunnel, so the ABAP system's icm/trusted_reverse_proxy profile parameter must be configured (typically set to PREFIX=<connector-cert-thumbprint>). This explicitly designates the Cloud Connector as a trusted proxy, allowing ABAP to accept and propagate the original client certificate instead of treating the connector's request as anonymous. Without it, certificate forwarding fails, breaking user context propagation.
- [S/4HANA: Enabling the Profile Parameters for Rule-based Certificate Mapping](https://help.sap.com/docs/CIAS%20FES%202020/ecb81b5bfce440ca8e7e7c9ad58fcf3a/604e55b0c1c54f259bf48a198dacbc1f.html?locale=en-US)

1. Log on to the SAP S/4HANA system and open the Maintain Profile Parameter `RZ10` transaction.
2. Select the `DEFAULT` profile.
3. In the Edit Profile group, select the `Extended maintenance` radio button and choose `Change`.
4. On the Maintain Profile 'DEFAULT' screen, create or update the following parameter `icm/trusted_reverse_proxy_<x>`.

> [!NOTE]
> Enter a free index for <x>, for example icm/trusted_reverse_proxy_1.
   
5. Enter <TRUSTED_REVERSE_PROXY_DETAILS>

```
SUBJECT="CN=<common_name>, [other_DN_parts]", ISSUER="CN=<issuer_common_name>, [other_DN_parts]"
```
| Component | Description | Example |
|---------|-------------|---------|
| **SUBJECT** | The Distinguished Name (DN) of the certificate's **subject** — usually the Cloud Connector hostname. | `CN=scc-host.example.com, L=WDF, O=SAP, C=DE` |
| **ISSUER** | The DN of the **issuing authority**. For self-signed SCC certificates, it matches the SUBJECT. | `CN=scc-host.example.com, L=WDF, O=SAP, C=DE` |
| **Quotes** | Must use **straight double quotes** (`"`) around each DN string. | `SUBJECT="CN=scc-host.example.com, ..."` |
| **DN Components** | Include **all** parts (CN, L, O, C, etc.) for exact match. Partial matches may work but are less secure. | `CN=..., L=WDF, O=SAP, C=DE` |

> [!TIP]
> Always copy **SUBJECT** and **ISSUER** directly from **STRUST** after importing the SCC certificate — never guess or shorten! The SUBJECT and ISSUER values you use in icm/trusted_reverse_proxy_<x> must be exactly the same as those shown in the Cloud Connector system certificate when imported into STRUST on the ABAP backend.

6. Restart the Internet Communication Manager (ICM) to apply the changes. Click on `Administration -> ICM -> Exit Soft -> Global`. This will restart ICM and make the parameters take effect.

> [!NOTE]
> The placeholder <TRUSTED_REVERSE_PROXY_DETAILS> refers to the value string that defines the trusted proxy's identity based on its system certificate's Subject and Issuer Distinguished Names (DNs). This tells ICM: "Accept certificate-forwarding only from this specific proxy."

> [!NOTE]
> Log on to the SAP S/4HANA system and open the ICM Monitor `SMICM` transaction. Choose `Goto > Parameters` and verify that the parameters `icm/HTTPS/verify_client` and `icm/trusted_reverse_proxy_<x>` are active.

#### Set or verify profile parameter `login/certificate_mapping_rulebased`: 
When a user logs on to an ABAP system via HTTPS with a personal X.509 client certificate, the system must map the certificate to an SAP user ID. With the parameter set to 1, the system evaluates rules you define in transaction CERTRULE instead of looking up the old manual table USREXTID.
- [Creating Rules for Certificate Mapping](https://help.sap.com/docs/SAP_NETWEAVER_AS_ABAP_751_IP/d528eef3dca14679bcb47b069aa17a9d/7c6d4b04370e40319ad790b554aa9a0b.html?version=7.51.0&locale=en-US)

1. Use ABAP Report `RSPFPAR` (For Selective Parameters)
2. Input parameter name: `login/certificate_mapping_rulebased`.
3. Click `Execute`.
4. Verify that the value is `1` (enable rule-based X.509 certificate mapping).

#### Setup Rule Based Certificate Mapping: 
Rule-based certificate mapping (transaction CERTRULE) enables the mapping of users from parts of the subject or the subject alternative name of an X.509 certificate for a given issuer to the user ID or alias of a user master record.
- [S/4HANA: Configuring Rule-Based Certificate Mapping](https://help.sap.com/docs/CIAS%20FES%202020/ecb81b5bfce440ca8e7e7c9ad58fcf3a/c339e699f657460088d5b1290ebf8115.html?locale=en-US)
- [Rule-Based Certificate Mapping](https://help.sap.com/docs/ABAP_PLATFORM_NEW/e815bb97839a4d83be6c4fca48ee5777/c830fd902dc8473b9e59db1576cc784b.html?locale=en-US)

1. Log on to the SAP S/4HANA system and open the Rule-Based Certificate Mapping `CERTRULE` transaction.
2. Switch to Edit Mode.
3. Choose `Import certificate`.
4. Select the sample certificate that you downloaded from the Cloud Connector and choose `Open`. The details of the certificate will be displayed.
5. Check whether the Mapping Status changes to `Certificate mapped with rule <x>` and the User Status changes to `Mapped email exists`.

---

### SAP BTP Subaccount
#### Configure Runtime destination for SSO
1. Navigate to the runtime destination.
2. As `Authentication Method` select `Principal Propagation`.
