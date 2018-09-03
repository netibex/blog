---
title: "Make ServicePrincipal visible as Enterprise App in the Azure Portal"
date: 2018-08-28
draft: = false
#slug = "alt-title-slug"
categories: ["Tech"]
tags: ["AzureAD","Azure", "SSO" ,"Cloud", "Tech"]
image: "images/tech/basics_of_auth_in_aad.png"
comments: true
share: true        
#menu: ""

---


I recently came across the issue at a customer at implementing SSO using Azure AD with a 3rd party application (in this case on-premises Dynamics NAV).
They used the PowerShell script that ships with the Application to register it in Azure AD. In this case the script only created a ServicePrincipal in Azure AD, thus
the application was not visible neither under "Registered Applications" nor under "Enterprise Applications" in the Azure Portal.
This means it was also not possible to assign users and groups to the application through the portal, which is not nice.

![AzureAD](/images/tech/basics_of_auth_in_aad.png)

I found out that you can make the ServicePrincipal visible in the portal by assigning a specific tag, which does all the magic.
Sign in via Azure PowerShell and you are good to go:

```powershell
Set-AzureADServicePrincipal -ObjectId "21fe0292-6104-4437-bab6-85069936eb38" -Tags @("WindowsAzureActiveDirectoryIntegratedApp")
```
After this you will be able to see the application in the Azure Portal, and assign users to it.
