---
title: Manage user-assigned managed identity - Azure CLI - Azure AD
description: Step by step instructions on how to create, list and delete a user-assigned managed identity using the Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/15/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 760de0d0bd1f11a34aa5c3998c923d1c7c80185d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232270"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Create, list or delete a user-assigned managed identity using the Azure CLI


Managed identities for Azure resources provides Azure services with a managed identity in Azure Active Directory. You can use this identity to authenticate to services that support Azure AD authentication, without needing credentials in your code. 

In this article, you learn how to create, list and delete a user-assigned managed identity using Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

- If you're unfamiliar with managed identities for Azure resources, check out the [overview section](overview.md). **Be sure to review the [difference between a system-assigned and user-assigned managed identity](overview.md#how-does-it-work)** .
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- To run the CLI script examples, you have three options:
    - Use [Azure Cloud Shell](../../cloud-shell/overview.md) from the Azure portal (see next section).
    - Use the embedded Azure Cloud Shell via the "Try It" button, located in the top right corner of each code block.
    - [Install the latest version of the Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 or later) if you prefer to use a local CLI console. Sign in to Azure using `az login`, using an account that is associated with the Azure subscription under which you would like to deploy the user-assigned managed identity.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador 

To create a user-assigned managed identity, your account needs the [Managed Identity Contributor](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role assignment.

Use the [az identity create](/cli/azure/identity#az-identity-create) command to create a user-assigned managed identity. The `-g` parameter specifies the resource group where to create the user-assigned managed identity, and the `-n` parameter specifies its name. Replace the `<RESOURCE GROUP>` and `<USER ASSIGNED IDENTITY NAME>` parameter values with your own values:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>List user-assigned managed identities

To list/read a user-assigned managed identity, your account needs the [Managed Identity Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) or [Managed Identity Contributor](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role assignment.

To list user-assigned managed identities, use the [az identity list](/cli/azure/identity#az-identity-list) command. Replace the `<RESOURCE GROUP>` with your own value:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
In the json response, user-assigned managed identities have `"Microsoft.ManagedIdentity/userAssignedIdentities"` value returned for key, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Delete a user-assigned managed identity

To delete a user-assigned managed identity, your account needs the [Managed Identity Contributor](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role assignment.

To delete a user-assigned managed identity, use the [az identity delete](/cli/azure/identity#az-identity-delete) command.  The -n parameter specifies its name and the -g parameter specifies the resource group where the user-assigned managed identity was created. Replace the `<USER ASSIGNED IDENTITY NAME>` and `<RESOURCE GROUP>` parameters values with your own values:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Deleting a user-assigned managed identity will not remove the reference, from any resource it was assigned to. Please remove those from VM/VMSS using the `az vm/vmss identity remove` command

## <a name="next-steps"></a>Passos seguintes

For a full list of Azure CLI identity commands, see [az identity](/cli/azure/identity).

For information on how to assign a user-assigned managed identity to an Azure VM see, [Configure managed identities for Azure resources on an Azure VM using Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
