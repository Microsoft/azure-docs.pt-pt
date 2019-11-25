---
title: Troubleshoot RBAC for Azure resources | Microsoft Docs
description: Troubleshoot issues with role-based access control (RBAC) for Azure resources.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 2351e6a63723156cce646a6a1cdda837b18a8f91
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456820"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Troubleshoot RBAC for Azure resources

This article answers common questions about role-based access control (RBAC) for Azure resources, so that you know what to expect when using the roles in the Azure portal and can troubleshoot access problems.

## <a name="problems-with-rbac-role-assignments"></a>Problemas com atribuições de funções do RBAC

- If you are unable to add a role assignment in the Azure portal on **Access control (IAM)** because the **Add** > **Add role assignment** option is disabled or because you get the permissions error "The client with object id does not have authorization to perform action", check that you are currently signed in with a user that is assigned a role that has the `Microsoft.Authorization/roleAssignments/write` permission such as [Owner](built-in-roles.md#owner) or [User Access Administrator](built-in-roles.md#user-access-administrator) at the scope you are trying to assign the role.
- If you get the error message "No more role assignments can be created (code: RoleAssignmentLimitExceeded)" when you try to assign a role, try to reduce the number of role assignments by assigning roles to groups instead. O Azure suporta até **duas mil** atribuições de funções por subscrição. This role assignments limit is fixed and cannot be increased.

## <a name="problems-with-custom-roles"></a>Problemas com funções personalizadas

- If you need steps for how to create a custom role, see the custom role tutorials using [Azure PowerShell](tutorial-custom-role-powershell.md) or [Azure CLI](tutorial-custom-role-cli.md).
- If you are unable to update an existing custom role, check that you are currently signed in with a user that is assigned a role that has the `Microsoft.Authorization/roleDefinition/write` permission such as [Owner](built-in-roles.md#owner) or [User Access Administrator](built-in-roles.md#user-access-administrator).
- If you are unable to delete a custom role and get the error message "There are existing role assignments referencing role (code: RoleDefinitionHasAssignments)", then there are role assignments still using the custom role. Remova essas atribuições e experimente eliminar a função personalizada novamente.
- Se receber a mensagem de erro “Limite de definição de função excedido. No more role definitions can be created (code: RoleDefinitionLimitExceeded)" when you try to create a new custom role, delete any custom roles that aren't being used. Azure supports up to **5000** custom roles in a tenant. (para clouds especializadas, como o Azure Government, o Azure Alemanha e o Azure China 21Vianet, o limite é de 2000 funções personalizadas).
- If you get an error similar to "The client has permission to perform action 'Microsoft.Authorization/roleDefinitions/write' on scope '/subscriptions/{subscriptionid}', however the linked subscription was not found" when you try to update a custom role, check whether one or more [assignable scopes](role-definitions.md#assignablescopes) have been deleted in the tenant. Se o âmbito tiver sido eliminado, crie um pedido de suporte, pois não existe nenhuma solução self-service atualmente.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Recuperar o RBAC quando as subscrições são movidas entre inquilinos

- If you need steps for how to transfer a subscription to a different Azure AD tenant, see [Transfer ownership of an Azure subscription to another account](../billing/billing-subscription-transfer.md).
- Se transferir uma subscrição para outro inquilino do Azure AD, todas as atribuições de funções são eliminadas permanentemente do inquilino do Azure AD de origem e não são migradas para o inquilino do Azure AD de destino. Tem de recriar as atribuições de funções no inquilino de destino. You also have to manually recreate managed identities for Azure resources. For more information, see [FAQs and known issues with managed identities](../active-directory/managed-identities-azure-resources/known-issues.md).
- If you are an Azure AD Global Administrator and you don't have access to a subscription after it was moved between tenants, use the **Access management for Azure resources** toggle to temporarily [elevate your access](elevate-access-global-admin.md) to get access to the subscription.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemas relacionados com administradores ou coadministradores de serviços

- If you are having issues with Service administrator or Co-administrators, see [Add or change Azure subscription administrators](../billing/billing-add-change-azure-subscription-administrator.md) and [Classic subscription administrator roles, Azure RBAC roles, and Azure AD administrator roles](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Access denied or permission errors

- If you get the permissions error "The client with object id does not have authorization to perform action over scope (code: AuthorizationFailed)" when you try to create a resource, check that you are currently signed in with a user that is assigned a role that has write permission to the resource at the selected scope. Por exemplo, para gerir máquinas virtuais num grupo de recursos, deve ter a função [Contribuidor de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) no grupo de recursos (ou no âmbito principal). Para obter uma lista das permissões de cada função incorporada, veja [Built-in roles for Azure resources](built-in-roles.md) (Funções incorporadas dos recursos do Azure)
- If you get the permissions error "You don't have permission to create a support request" when you try to create or update a support ticket, check that you are currently signed in with a user that is assigned a role that has the `Microsoft.Support/supportTickets/write` permission, such as [Support Request Contributor](built-in-roles.md#support-request-contributor).

## <a name="role-assignments-with-unknown-security-principal"></a>Role assignments with Unknown security principal

If you assign a role to a security principal (user, group, service principal, or managed identity) and then you later delete that security principal without removing the role assignment, the security principal type for the role assignment will be listed as **Unknown**. The following screenshot shows an example in the Azure portal. The security principal name is listed as **Identity deleted** and **Identity no longer exists**. 

![Web app resource group](./media/troubleshooting/unknown-security-principal.png)

If you list this role assignment using Azure PowerShell, you will see an empty `DisplayName` and an `ObjectType` set to Unknown. For example, [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) returns a role assignment that is similar to the following:

```azurepowershell
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Similarly, if you list this role assignment using Azure CLI, you will see an empty `principalName`. For example, [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) returns a role assignment that is similar to the following:

```azurecli
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

It isn't a problem to leave these role assignments, but you can remove them using steps that are similar to other role assignments. For information about how to remove role assignments, see [Azure portal](role-assignments-portal.md#remove-role-assignments), [Azure PowerShell](role-assignments-powershell.md#remove-access), or [Azure CLI](role-assignments-cli.md#remove-access)

In PowerShell, if you try to remove the role assignments using the object ID and role definition name, and more than one role assignment matches your parameters, you will get the error message: "The provided information does not map to a role assignment". The following shows an example of the error message:

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

If you get this error message, make sure you also specify the `-Scope` or `-ResourceGroupName` parameters.

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="rbac-changes-are-not-being-detected"></a>RBAC changes are not being detected

Azure Resource Manager sometimes caches configurations and data to improve performance. When creating or deleting role assignments, it can take up to 30 minutes for changes to take effect. If you are using the Azure portal, Azure PowerShell, or Azure CLI, you can force a refresh of your role assignment changes by signing out and signing in. If you are making role assignment changes with REST API calls, you can force a refresh by refreshing your access token.

## <a name="web-app-features-that-require-write-access"></a>Web app features that require write access

If you grant a user read-only access to a single web app, some features are disabled that you might not expect. The following management capabilities require **write** access to a web app (either Contributor or Owner), and aren't available in any read-only scenario.

* Commands (like start, stop, etc.)
* Changing settings like general configuration, scale settings, backup settings, and monitoring settings
* Accessing publishing credentials and other secrets like app settings and connection strings
* Streaming logs
* Diagnostic logs configuration
* Console (command prompt)
* Active and recent deployments (for local git continuous deployment)
* Estimated spend
* Web tests
* Virtual network (only visible to a reader if a virtual network has previously been configured by a user with write access).

If you can't access any of these tiles, you need to ask your administrator for Contributor access to the web app.

## <a name="web-app-resources-that-require-write-access"></a>Web app resources that require write access

Web apps are complicated by the presence of a few different resources that interplay. Here is a typical resource group with a couple of websites:

![Web app resource group](./media/troubleshooting/website-resource-model.png)

As a result, if you grant someone access to just the web app, much of the functionality on the website blade in the Azure portal is disabled.

These items require **write** access to the **App Service plan** that corresponds to your website:  

* Viewing the web app's pricing tier (Free or Standard)  
* Scale configuration (number of instances, virtual machine size, autoscale settings)  
* Quotas (storage, bandwidth, CPU)  

These items require **write** access to the whole **Resource group** that contains your website:  

* SSL Certificates and bindings (SSL certificates can be shared between sites in the same resource group and geo-location)  
* Regras de alerta  
* Autoscale settings  
* Application insights components  
* Web tests  

## <a name="virtual-machine-features-that-require-write-access"></a>Virtual machine features that require write access

Similar to web apps, some features on the virtual machine blade require write access to the virtual machine, or to other resources in the resource group.

Virtual machines are related to Domain names, virtual networks, storage accounts, and alert rules.

These items require **write** access to the **Virtual machine**:

* Pontos Finais  
* Endereços IP  
* Discos  
* Extensões  

These require **write** access to both the **Virtual machine**, and the **Resource group** (along with the Domain name) that it is in:  

* Conjunto de disponibilidade  
* Load balanced set  
* Regras de alerta  

If you can't access any of these tiles, ask your administrator for Contributor access to the Resource group.

## <a name="azure-functions-and-write-access"></a>Azure Functions and write access

Some features of [Azure Functions](../azure-functions/functions-overview.md) require write access. For example, if a user is assigned the [Reader](built-in-roles.md#reader) role, they will not be able to view the functions within a function app. The portal will display **(No access)** .

![Function apps no access](./media/troubleshooting/functionapps-noaccess.png)

A reader can click the **Platform features** tab and then click **All settings** to view some settings related to a function app (similar to a web app), but they can't modify any of these settings. To access these features, you will need the [Contributor](built-in-roles.md#contributor) role.

## <a name="next-steps"></a>Passos seguintes

- [Troubleshoot for guest users](role-assignments-external-users.md#troubleshoot)
- [Manage access to Azure resources using RBAC and the Azure portal](role-assignments-portal.md) (Gerir o acesso a recursos do Azure com RBAC e o portal do Azure)
- [View activity logs for RBAC changes to Azure resources](change-history-report.md)

