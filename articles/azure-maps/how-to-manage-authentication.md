---
title: Manage authentication in Microsoft Azure Maps
titleSuffix: Azure Maps
description: Become familiar with Azure Maps authentication. See which approach works best in which scenario. Learn how to use the portal to view authentication settings.
author: stevemunk
ms.author: v-munksteve
ms.date: 12/3/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
custom.ms: subject-rbac-steps
---

# Manage authentication in Azure Maps

When you create an Azure Maps account, your client ID is automatically generated along with primary and secondary keys that are required for authentication when using [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) or [Shared Key authentication](./azure-maps-authentication.md#shared-key-authentication).

## Prerequisites

Sign in to the [Azure portal](https://portal.azure.com). If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/) before you begin.
- A familiarization with [managed identities for Azure resources](../active-directory/managed-identities-azure-resources/overview.md). Be sure to understand the two [Managed identity types](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) and how they differ.
- [An Azure Maps account](quick-demo-map-app.md#create-an-azure-maps-account).
- A familiarization with [Azure Maps Authentication](./azure-maps-authentication.md).

## View authentication details

> [!IMPORTANT]
> We recommend that you use the primary key as the subscription key when you use Shared Key authentication to call Azure Maps. It's best to use the secondary key in scenarios like rolling key changes.

To view your Azure Maps authentication details:

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Select **All resources** in the **Azure services** section, then select your Azure Maps account.

   :::image type="content" border="true" source="./media/how-to-manage-authentication/select-all-resources.png" alt-text="Select Azure Maps account.":::

3. Select **Authentication** in the settings section of the left pane.

   :::image type="content" border="true" source="./media/how-to-manage-authentication/view-authentication-keys.png" alt-text="Authentication details.":::

## Choose an authentication category

Depending on your application needs, there are specific pathways to application security. Azure AD defines specific authentication categories to support a wide range of authentication flows. To choose the best category for your application, see [application categories](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories).

> [!NOTE]
> Understanding categories and scenarios will help you secure your Azure Maps application, whether you use Azure Active Directory or shared key authentication.

## How to add and remove managed identities

To enable [Shared access signature (SAS) token authentication](./azure-maps-authentication.md#shared-access-signature-token-authentication) with the Azure Maps REST API you need to add a user-assigned managed identity to your Azure Maps account.

### Create a managed identity

You can create a user-assigned managed identity before or after creating a map account. You can add the managed identity through the portal, Azure management SDKs, or the Azure Resource Manager (ARM) template. To add a user-assigned managed identity through an ARM template, specify the resource identifier of the user-assigned managed identity. See example below:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/example/providers/Microsoft.ManagedIdentity/userAssignedIdentities/exampleidentity": {}
    }
}
```

### Remove a managed identity

You can remove a system-assigned identity by disabling the feature through the portal or the Azure Resource Manager template in the same way that it was created. User-assigned identities can be removed individually. To remove all identities, set the identity type to `"None"`.

Removing a system-assigned identity in this way will also delete it from Azure AD. System-assigned identities are also automatically removed from Azure AD when the Azure Maps account is deleted.

To remove all identities by using the Azure Resource Manager template, update this section:

```json
"identity": {
    "type": "None"
}
```

## Choose an authentication and authorization scenario

This table outlines common authentication and authorization scenarios in Azure Maps. Each scenario describes a type of app which can be used to access Azure Maps REST API. Use the links to learn detailed configuration information for each scenario.

> [!IMPORTANT]
> For production applications, we recommend implementing Azure AD with Azure role-based access control (Azure RBAC).

| Scenario                                                                            | Authentication | Authorization | Development effort | Operational effort |
| ----------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Trusted daemon app or non-interactive client app](./how-to-secure-daemon-app.md)   | Shared Key     | N/A           | Medium             | High               |
| [Trusted daemon or non-interactive client app](./how-to-secure-daemon-app.md)       | Azure AD       | High          | Low                | Medium             |
| [Web single page app with interactive single-sign-on](./how-to-secure-spa-users.md) | Azure AD       | High          | Medium             | Medium             |
| [Web single page app with non-interactive sign-on](./how-to-secure-spa-app.md)      | Azure AD       | High          | Medium             | Medium             |
| [Web app, daemon app, or non-interactive sign-on app](./how-to-secure-sas-app.md)   | SAS Token      | High          | Medium             | Low                |
| [Web application with interactive single-sign-on](./how-to-secure-webapp-users.md)  | Azure AD       | High          | High               | Medium             |
| [IoT device or an input constrained application](./how-to-secure-device-code.md)    | Azure AD       | High          | Medium             | Medium             |

## View built-in Azure Maps role definitions

To view the built-in Azure Maps role definition:

1. In the left pane, select **Access control (IAM)**.

2. Select the **Roles** tab.

3. In the search box, enter **Azure Maps**.

The results display the available built-in role definitions for Azure Maps.

:::image type="content" border="true" source="./media/how-to-manage-authentication/view-role-definitions.png" alt-text="View built-in Azure Maps role definitions.":::

## View role assignments

To view users and apps that have been granted access for Azure Maps, go to **Access Control (IAM)**. There, select **Role assignments**, and then filter by **Azure Maps**.

1. In the left pane, select **Access control (IAM)**.

2. Select the **Role assignments** tab.

3. In the search box, enter **Azure Maps**.

The results display the current Azure Maps role assignments.

:::image type="content" border="true" source="./media/how-to-manage-authentication/view-amrbac.png" alt-text="View built-in View users and apps that have been granted access.":::

## Request tokens for Azure Maps

Request a token from the Azure AD token endpoint. In your Azure AD request, use the following details:

| Azure environment      | Azure AD token endpoint             | Azure resource ID              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Azure public cloud     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government cloud | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

For more information about requesting access tokens from Azure AD for users and service principals, see [Authentication scenarios for Azure AD](../active-directory/develop/authentication-vs-authorization.md). To view specific scenarios, see [the table of scenarios](./how-to-manage-authentication.md#choose-an-authentication-and-authorization-scenario).

## Manage and rotate shared keys

Your Azure Maps subscription keys are similar to a root password for your Azure Maps account. Always be careful to protect your subscription keys. Use Azure Key Vault to securely manage and rotate your keys. Avoid distributing access keys to other users, hard-coding them, or saving them anywhere in plain text that's accessible to others. If you believe that your keys may have been compromised, rotate them.

> [!NOTE]
> If possible, we recommend using Azure AD instead of Shared Key to authorize requests. Azure AD has better security than Shared Key, and it's easier to use.

### Manually rotate subscription keys

To help keep your Azure Maps account secure, we recommend periodically rotating your subscription keys. If possible, use Azure Key Vault to manage your access keys. If you aren't using Key Vault, you'll need to manually rotate your keys.

Two subscription keys are assigned so that you can rotate your keys. Having two keys ensures that your application maintains access to Azure Maps throughout the process.

To rotate your Azure Maps subscription keys in the Azure portal:

1. Update your application code to reference the secondary key for the Azure Maps account and deploy.
2. In the [Azure portal](https://portal.azure.com/), navigate to your Azure Maps account.
3. Under **Settings**, select **Authentication**.
4. To regenerate the primary key for your Azure Maps account, select the **Regenerate** button next to the primary key.
5. Update your application code to reference the new primary key and deploy.
6. Regenerate the secondary key in the same manner.

> [!WARNING]
> We recommend using only one of the keys in all of your applications at the same time. If you use Key 1 in some places and Key 2 in others, you won't be able to rotate your keys without some applications losing access.

## Next steps

Find the API usage metrics for your Azure Maps account:

> [!div class="nextstepaction"]
> [View usage metrics](how-to-view-api-usage.md)

Explore samples that show how to integrate Azure AD with Azure Maps:

> [!div class="nextstepaction"]
> [Azure AD authentication samples](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
