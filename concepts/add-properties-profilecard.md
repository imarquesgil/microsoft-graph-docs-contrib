---
title: "Add or remove custom attributes on a profile card using the profile card API"
description: "Learn how to use the profile card API in Microsoft Graph to make additional attributes visible and add or remove custom attributes on a profile card."
author: "rwaithera"
ms.localizationpriority: high
ms.prod: "people"
ms.custom: scenarios:getting-started
---

# Add or remove custom attributes on a profile card using the profile card API

The [profile card](https://support.microsoft.com/en-au/office/profile-cards-in-microsoft-365-e80f931f-5fc4-4a59-ba6e-c1e35a85b501) in Microsoft 365 shows information about a user in an organization. The information shown on the profile card is stored and maintained by the organization, for example, **Job title** or **Office location**.

Organizations can use the [profileCardProperty](/graph/api/resources/profilecardproperty) resource to show additional properties from [Microsoft Entra ID](https://www.microsoft.com/en-us/security/business/identity-access/microsoft-entra-id) on the profile card for a user in an organization by:

* Making additional attributes visible
* Adding custom attributes

Additional properties display in the **Contact** section of the profile card in Microsoft 365.

You can also [remove](/graph/api/profilecardproperty-delete) custom attributes from profile cards of the organization.

[!INCLUDE [profilecardproperty-add-remove-note](../includes/profilecardproperty-add-remove-note.md)]

> [!NOTE]
> Operations on the **profileCardProperty** resource that use delegated permissions require the signed-in user to have a Tenant Administrator or Global Administrator role. 

## Make additional attributes visible on the profile card

You can make the following attributes from Microsoft Entra ID visible on the users' profile cards. These attributes are *not case-sensitive*:

* `UserPrincipalName`
* `Fax`
* `StreetAddress`
* `PostalCode`
* `StateOrProvince`
* `Alias`

The following table shows how the Microsoft Entra ID attributes correspond with properties of the Microsoft Graph [user](/graph/api/resources/user) entity.

| Microsoft Entra ID attribute | User entity property |
| ------------------ | -------------------- |
| UserPrincipalName | userPrincipalName |
| Fax | faxNumber |
| StreetAddress | streetAddress |
| PostalCode | postalCode |
| StateOrProvince | state |
| Alias | mailNickname |

You can add any of these attributes to the profile card by configuring your [people admin settings](/graph/api/resources/peopleadminsettings) and adding the attribute as the **directoryPropertyName** property of a **profileCardProperty** in Microsoft Graph. When you make additional attributes visible, you must use the property names for `en-us`. You don't have to add localized values. The additional properties are automatically shown in the language settings that the user has specified for Microsoft 365.

> **Note:** User entity property values contain user information stored and managed by the organization.

> [!IMPORTANT]
> When adding an attribute to a profile card, it takes up to 24 hours for the addition to be displayed.

## Configure profile card properties using the Microsoft Graph REST API

### Example

The following example displays the `Alias` attribute on the profile card.

``` http
POST https://graph.microsoft.com/v1.0/admin/people/profileCardProperties
Content-Type: application/json

{
  "directoryPropertyName": "Alias"
}
```

> **Note:** The `/organization/{organizationId}/settings` path is deprecated in the beta experience. Going forward, use the `/admin/people` path.

If successful, the response returns a `201 OK` response code and a **profileCardProperty** object in the response body. The value for the `Alias` attribute is displayed on a user's profile card.

``` http
HTTP/1.1 201 OK
Content-type: application/json

{
  "directoryPropertyName": "Alias",
  "annotations": []
}
```

## Add a custom attribute to the profile card

You can add any of the 15 Microsoft Entra ID [custom extension attributes](/graph/api/resources/onpremisesextensionattributes) to users' profile cards by configuring your organization settings and [adding the corresponding value as a profileCardProperty](/graph/api/peopleadminsettings-post-profilecardproperties) in Microsoft Graph. You can add one **profileCardProperty** resource at a time.

It takes up to 24 hours for the changes to show on profile cards.

Custom properties aren't searchable and can't be used to search for people across Microsoft apps and services.

The following table shows how the Microsoft Entra ID custom extension attribute names correspond to the supported values for the **directoryPropertyName** property of the [profileCardProperty](/graph/api/resources/profilecardproperty) resource. These Microsoft Entra ID custom extension attribute names are *not case-sensitive*:

| Microsoft Entra ID custom extension attribute | Value to specify as directoryPropertyName |
| ----------------------------------- | ----------------------------------------- |
| extensionAttribute1 | customAttribute1 |
| extensionAttribute2 | customAttribute2 |
| extensionAttribute3 | customAttribute3 |
| extensionAttribute4 | customAttribute4 |
| extensionAttribute5 | customAttribute5 |
| extensionAttribute6 | customAttribute6 |
| extensionAttribute7 | customAttribute7 |
| extensionAttribute8 | customAttribute8 |
| extensionAttribute9 | customAttribute9 |
| extensionAttribute10 | customAttribute10 |
| extensionAttribute11 | customAttribute11 |
| extensionAttribute12 | customAttribute12 |
| extensionAttribute13 | customAttribute13 |
| extensionAttribute14 | customAttribute14 |
| extensionAttribute15 | customAttribute15 |

> [!IMPORTANT]
> Custom profile card attributes are added for all users in the organization, requiring necessary precautions to prevent accidental exposure of sensitive data.

### Example

The following example adds the first Microsoft Entra ID custom extension attribute to the profile card, using the display name **Cost center**. For users that have set their language settings to German, the display name is **Kostenstelle**.

#### Request

``` http
POST https://graph.microsoft.com/v1.0/admin/people/profileCardProperties
Content-Type: application/json

{
  "directoryPropertyName": "customAttribute1",
  "annotations": [
    {
      "displayName": "Cost center",
      "localizations": [
        {
          "languageTag": "de",
          "displayName": "Kostenstelle"
        }
      ]
    }
  ]
}
```

> **Note:** The `/organization/{organizationId}/settings` path is deprecated in the beta experience. Going forward, use the `/admin/people` path.

If a language isn't supported, the property name is shown with the default value.

If successful, the response returns a `201 OK` response code and a **profileCardProperty** object in the response body. In this example, you can assume that the profile card displays **Kostenstelle** for all users that have set their language settings to German on the profile card. For all other users, **Cost center** is displayed on the profile card.

#### Response

``` http
HTTP/1.1 201 OK
Content-type: application/json

{
  "directoryPropertyName": "customAttribute1",
  "annotations": [
    {
      "displayName": "Cost center",
      "localizations": [
        {
          "languageTag": "de",
          "displayName": "Kostenstelle"
        }
      ]
    }
  ]
}
```

## Remove a custom attribute from the profile card

Following the same mapping between Microsoft Entra ID custom extension attributes and profile card custom attributes (such as `customAttribute1`) as described in the preceding section [Adding a custom attribute](/graph/add-properties-profilecard#adding-a-custom-attribute), you can remove a custom attribute using the [delete](/graph/api/profilecardproperty-delete) operation, as shown in the following example.

### Example

The following example removes the custom attribute `customAttribute5` from the organization settings. If successful, this method returns a `204 No Content` response code.

#### Request

``` http
DELETE https://graph.microsoft.com/v1.0/admin/people/profileCardProperties/customAttribute5
```

> **Note:** The `/organization/{organizationId}/settings` path is deprecated in the beta experience. Going forward, use the `/admin/people` path.

#### Response

``` http
HTTP/1.1 204 No Content
```

## Configure profile card properties using PowerShell

You can use the [Microsoft Graph PowerShell SDK](/powershell/microsoftgraph/installation) to configure profile card properties in your organization.

### Prerequisites

- **PowerShell module** - Install [module version 1.24.0 or higher](https://www.powershellgallery.com/packages/Microsoft.Graph).
- **.NET Framework** - Install [.NET Framework 4.7.2](https://dotnet.microsoft.com/download/dotnet-framework) or a higher version.

> [!NOTE]
> The PowerShell commands for the profile card properties are only available in beta. Switch to the beta profile before you run the following commands.
> ```powershell
>    Select-MgProfile beta
> ```

### Confirm your current settings

To get profile card properties configuration for an organization, use the following command.

```powershell
   Get-MgBetaAdminPeopleProfileCardProperty
```

To get a specific profile card property configuration in an organization, use the following command.

```powershell
   Get-MgBetaAdminPeopleProfileCardProperty -ProfileCardPropertyId $profileCardPropertyId
```

> [!NOTE]
> The get commands require the `PeopleSettings.Read.All` permission. To create a Microsoft Graph session with a specific required scope, use the following command and consent to requested permissions.
>
> ```powershell
>    Connect-MgGraph -Scopes "PeopleSettings.Read.All"
> ```

### Add profile card properties in your organization

You can use the Microsoft Graph PowerShell module to make both additional Microsoft Entra ID profile card properties, and the 15 customizable Microsoft Entra ID profile card properties available in your organization.

> [!NOTE]
> The new command requires the `PeopleSettings.ReadWrite.All` permission. To create a Microsoft Graph session with a specific required scope, use the following command and consent to requested permissions.
>
> ```powershell
>    Connect-MgGraph -Scopes "PeopleSettings.ReadWrite.All","PeopleSettings.Read.All"
> ```

Use the following command.

```powershell
$params = @{
	directoryPropertyName = "CustomAttribute1"
	annotations = @(
		@{
			displayName = "Cost Center"
			localizations = @(
				@{
					languageTag = "ru-RU"
					displayName = "центр затрат"
				}
			)
		}
	)
}

New-MgAdminPeopleProfileCardProperty -BodyParameter $params
```

### Update profile card properties in your organization

You can use the Microsoft Graph PowerShell module to update profile card properties available in your organization.

> [!NOTE]
> The update command requires the `PeopleSettings.ReadWrite.All` permission. To create a Microsoft Graph session with a specific required scope, use the following command and consent to requested permissions.
>
> ```powershell
>    Connect-MgGraph -Scopes "PeopleSettings.ReadWrite.All","PeopleSettings.Read.All"
> ```

Use the following command, where you replace `$profileCardPropertyId` with the ID of the property to be updated.

```powershell
$params = @{
	annotations = @(
		@{
      displayName = "Cost Center"
			localizations = @(
				@{
					languageTag = "nb-NO"
					displayName = "Kostnads Senter"
				}
			)
		}
	)
}

Update-MgAdminPeopleProfileCardProperty -ProfileCardPropertyId $profileCardPropertyId -BodyParameter $params
```

### Remove profile card properties in your organization

You can use the Microsoft Graph PowerShell module to remove profile card properties from your organization.

> [!NOTE]
> The remove command requires the `PeopleSettings.ReadWrite.All` permission. To create a Microsoft Graph session with a specific required scope, use the following command and consent to requested permissions.
>
> ```powershell
>    Connect-MgGraph -Scopes "PeopleSettings.ReadWrite.All","PeopleSettings.Read.All"
> ```

Use the following command, where you replace `$profileCardPropertyId` with the ID of the property to be removed.

```powershell
 Remove-MgAdminPeopleProfileCardProperty -ProfileCardPropertyId $profileCardPropertyId
```

## Related content

- [onPremisesExtensionAttributes resource type](/graph/api/resources/onpremisesextensionattributes)
- [User resource type](/graph/api/resources/user)
- [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
- [Get profileCardProperty](/graph/api/profilecardproperty-get)
- [Azure Data Subject Requests for the GDPR and CCPA](/compliance/regulatory/gdpr-dsr-azure)
