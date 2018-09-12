---
title: "Remove Oauth Delegated Application Permissions in Azure AD via PowerShell"
date: 2018-09-12
draft: = false
#slug = "alt-title-slug"
categories: ["Tech"]
tags: ["AzureAD","Oauth", "Permissions", "Azure","Cloud", "Tech"]
image: "images/user-graph.jpg"
comments: true
share: true        
#menu: ""
---

If you have ever asked on how to remove grant permissions in Azure AD, below is how to do it in PowerShell.

Azure AD has the concept of granting delegated and/or applications permissions for OAuth and OpenID Connect flows to control permissions in the directory.
You can get more details in the docs: https://docs.microsoft.com/en-us/azure/active-directory/develop/v1-permissions-and-consent

The way it works is, if configured, the user is asked if an application registered in Azure AD can access certain information in the directory (like user profile etc...), when he logs into an Azure AD integrated application. If the user accepts, he is granted delegated permissions in the directory for the specific application. This means that an application can read certain information on behalf of the user (for example).

E.g. this may look like the following:
![Azure Ad permissions grant](/images/tech/azure-ad-permissions-grant.jpg)

But, dependent on how the application was registered in Azure AD (unfortunately this is a very inconsistent experience at the moment), you may not be able to remove permissions through the portal, such as I experienced it when registering an application through the new App Registration Portal (apps.dev.microsoft.com).

First you need all the OAuth permissions for your specific application. You will need the ObjectID of the application, which you can find easily in the portal.

```powershell
# Login to Azure AD
Connect-AzureAd

#Get OAuth permissions for the application (using objectid)
Get-AzureADOAuth2PermissionGrant | where ClientId -EQ 81131bc8-xxxx-xxxx-xxxx-d4fb165a7a8f | fl *
```

The youput will look like the following:

```powershell
#Output
...
ClientId    : 81131bc8-xxxx-xxxx-xxxx-d4fb165a7a8f
ConsentType : AllPrincipals
ExpiryTime  : 3/11/2019 7:57:03 AM
ObjectId    : yBsTgSDiqkavP9T7Flp6j1hgDoNT9UZLh_qtgQB60Zk
PrincipalId : 45591aae-xxxx-xxxx-xxxx-3597b070b70f          # <--- This is the user Object-ID
ResourceId  : 830e6058-xxxx-xxxx-xxxx-ad81007ad199
Scope       : User.Read User.ReadBasic.All User.ReadWrite
StartTime   : 1/1/0001 12:00:00 AM
...
```

Then you need the ObjectID from the user for whom you want to remove the permissions:

```powershell
# Get User
Get-AzureADUser | where displayname -like <usertoberemovedname>

#ObjectId = PrincipalID from the output above
ObjectId                             DisplayName         UserPrincipalName                  UserType
--------                             -----------         -----------------                  --------
45591aae-xxxx-xxxx-xxxx-3597b070b70f usertoberemovedname usertoberemovedname@domain.com     Member 
```

Finally remove the permissions using the id for the application and user:

```powershell
#Remove permissions
Get-AzureADOAuth2PermissionGrant | Where-Object ClientId -EQ <application ClientId> | Where-Object PrincipalId -EQ <user ObjectId> | Remove-AzureADOAuth2PermissionGrant
```

Voila!