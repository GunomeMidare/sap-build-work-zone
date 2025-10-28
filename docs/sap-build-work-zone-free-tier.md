# SAP Build Work Zone Free Tier

## Goal 🎯

The goal of this file is to document the SAP Build WOrk Zone Free Tier application

## References 📝
- [3434634 - SAP Build Work Zone Free Sevice Plan limit reached](https://me.sap.com/notes/0003434634)
- [Discovery Center: SAP Build Work Zone, standard edition](https://discovery-center.cloud.sap/serviceCatalog/sap-build-work-zone-standard-edition?region=all)
- [Using an Account with a Free Service Plan](https://help.sap.com/docs/build-work-zone-standard-edition/sap-build-work-zone-standard-edition/using-account-with-free-service-plan?locale=en-US)

### SAP Build Work Zone Free Tier
SAP Build Work Zone's free tier is part of the SAP Business Technology Platform (BTP) free tier model and applies specifically to the standard edition. The advanced edition is not available in the free tier or trial accounts. 
Below is a summary of the key restrictions based on official SAP documentation and support resources. These limits are enforced per subaccount and are designed for development, testing, or small-scale use:
- User and Access Limits
  - Maximum Users: Limited to 20 named users (end-users who access the launchpad) and 2 admin users per month per subaccount. Exceeding this triggers an error like "Sorry, this subaccount has reached its limit," blocking logins until the next month or an upgrade.
  - Admin Role Assignment: Only 2 users can be assigned roles like Launchpad_Admin. Assigning more immediately hits the limit, even if no end-users are active.
 
> [!Note]
> The account limitation: There is a counter that counts the users who access the site (Designtime- admin / Runtime- enduser) from the beginning of the month and it fills in a spot until it reaches 2 admin users and 20 endusers. 
> For the 20 users, it's not necessary for them to have any roles assigned to access the site. All they need is the Launchpad site URL and they can access the site.
> The 2 admin users need the Launchpad_Admin role to access the Designtime site.
> This method basically it's like: "first come first served".
>
> This configuration will be reset on 1st of every month.


