# Create the Design-Time Destination for SAP Build Work Zone

## Goal 🎯

The goal of this file is to document how to create the design-time destination. The design-time destination is used to fetch the federated content from the content provider system during design-time.

## References 📝
- [3416482 - Configure design time destination for on-prem solution SAP Build Work Zone, standard edition](https://me.sap.com/notes/3416482/E)
- [3501486 - Launchpad Content Exposed with version 2 not updated in SAP Build Workzone, Standard](https://me.sap.com/notes/3501486/E)
- [Create Design-Time destination](https://help.sap.com/docs/SUPPORT_CONTENT/fioritech/5173040119.html?locale=en-US)
- [SAP Help: Design-Time Destination](https://help.sap.com/docs/joule/integrating-joule-with-sap/configure-destinations?locale=en-US#design-time-destination)

## Prerequisites 📝
- Decide on the usage of version 1 or version 2 for Content Exposure
- You need to know the Virtual Host of the Cloud Connector

---

### Create Design-Time Destination 🛠️
1. Create a destination in the SAP Build Work Zone subaccount.
2. Enter following information:

    | Field Name        | Value                                                                 | Remarks                                                                                                                  |
    |-------------------|-----------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|
    | Names             | 4hanadt                                     | SAP recommends to add the suffix `dt` to the name. The name must contain only lowercase letters and must not contain the underscore (_) character. |  
    | Type              | HTTP                                        |                                                                                                                                                    |
    | Description       | SAP S/4HANA system                          |                                                                                                                                                    |
    | URL               |                                             | The URL of the Virtual Host of your Cloud Connector: `https://<host>:<port>/sap/bc/ui2/cdm3/entities` for Version 1 and `https://<host>:<port>/sap/bc/http/ui2/flp_content_exposure/entities` for Version 2    |
    | Proxy Type        | OnPremise                                   |                                                                                                                                                    |
    | Authentication    | BasicAuthentication                         |                                                                                                                                                    |
    | User              | <User ID on your SAP S/4HANA system>        |                                                                                                                                                    |
    | Password          | <Password of the user>                      |                                                                                                                                                    |

3. Enter following additional properties:

    | Field Name        | Value                                                                 |
    |-------------------|-----------------------------------------------------------------------|
    | sap-client        | <client of your SAP S/4HANA system>                                   |


> [!Note]
> If version 2 is to be used, create a new* design time destination and include the service endpoint URL <host>/sap/bc/http/ui2/flp_content_exposure/entities.
> Create a new content channel that uses the new design time destination. Since this is an HTTP service, you might need to activate it first in the S/4 system using transaction UCON_HTTP_SERVICES. The name of the service is /UI2/FLP_CONTENT_EXPOSURE.
>
> Make sure that ICF node /sap/bc/http is activated using transaction SICF.
> (*) Do not use the same Design time destination (and content channel) to switch between different exposure versions. This can lead to undesired behaviors during content exposure (i.e. malfunction of the Content Change Notifications).
