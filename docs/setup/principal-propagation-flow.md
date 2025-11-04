# Principal Propagation Flow: SAP Build Work Zone → Cloud Connector → On-Prem S/4HANA

## Goal 🎯

The goal of this file is to document the Principal Propagation Flow for SAP BTP to an S/4HANA On Premise system within a SAP Build Work Zone scenario.

## References 📝
- []()


---

## 1. User Authentication in SAP Build Work Zone (`Alice_BTP`)

When a user (`Alice_BTP`) authenticates and accesses an **on-premise-integrated app** via **SAP Build Work Zone**:

- SAP BTP Identity Authentication Service (**IAS**) validates the user.
- A **JWT** is issued (by IAS/IDS) and attached to the backend request in the **invisible underlying layer**.
  - Contains:
    - User ID
    - User Name
    - Email
    - Client ID
    - etc.

> _This JWT is automatically propagated when calling backend systems via Destination with Principal Propagation._

---

## 2. Runtime Destination (Principal Propagation)

| Setting | Value |
|-------|--------|
| **Proxy Type** | `On-Premise` |
| **Authentication** | `Principal Propagation` |

- SAP Build Work Zone / SAP CIS passes the **JWT token** to:
  1. **BTP Connectivity Service**
  2. **Cloud Connector**

---

## 3. Cloud Connector (SCC) Actions

### 3.1 Validate JWT
- Validates **integrity & validity** of the JWT
- **Requires**: Trust Configuration in SCC

### 3.2 Extract User Identifier
- Extracts one property (e.g., `email: alice@company.com`) as user identifier
- **Requires**: **Subject Patterns** configured in SCC

### 3.3 Generate Short-Lived Certificate
- Creates a **short-lived X.509 certificate**:
  - **Subject CN** = extracted identifier (`alice@company.com`)
  - **Issuer** = SCC CA Certificate
- **Requires**: SCC CA Certificate configured

### 3.4 Call S/4HANA System
- **Protocol**: `HTTPS` (not HTTP)
- **SSL Handshake**: Uses **SCC System Certificate**
  - **Requires**: System Certificate in SCC
- **HTTP Header**: Includes short-lived cert in `ssl_client_cert`
- **Payload**: Original data from CPI message

---

## 4. On-Premise S/4HANA Actions

### 4.1 Authenticate Cloud Connector
- Validates **SCC System Certificate** during SSL handshake
- **Requires**:
  1. `icm/trusted_reverse_proxy_<x>` profile parameter
  2. **Root CA** of SCC System Certificate imported into:
     - **STRUST** → **SSL Server PSE**

### 4.2 Parse `ssl_client_cert` Header
- Extracts from certificate:
  - **Subject CN** = `alice@company.com`
  - **Issuer** = SCC CA Certificate

### 4.3 Map Identity to S/4 User (`Alice_S4`)
- Uses **Rule-Based Certificate Mapping**
- **Requires**:
  1. Mapping rule for the user identity (e.g., email)
  2. Target user has **same unique identifier** (e.g., `alice@company.com`)

### 4.4 Execute Service as Mapped User
- Runs the invoked service under **`Alice_S4`**
- **Requires**: Mapped user has necessary **roles/authorizations**

---

**End-to-End Identity: `Alice_BTP` → `alice@company.com` → `Alice_S4`**

---
