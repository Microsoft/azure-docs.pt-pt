---
title: Gerir o acesso aos recursos do Azure através do RBAC e o Azure PowerShell | Documentos da Microsoft
description: Saiba como gerir o acesso ao resoruces do Azure para utilizadores, grupos e aplicações com o controlo de acesso baseado em funções (RBAC) e o Azure PowerShell. Isto inclui como listar, conceder e remover acesso.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6d74ee8ee144667df49ba4226d1db720969e19bd
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588488"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-powershell"></a>Gerir o acesso aos recursos do Azure através do RBAC e o Azure PowerShell

[Controlo de acesso baseado em funções (RBAC)](overview.md) é a maneira que gerencie o acesso aos recursos do Azure. Este artigo descreve como gerir o acesso para utilizadores, grupos e aplicações através do RBAC e o Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para gerir o acesso, precisa de um dos seguintes:

* [PowerShell no Azure Cloud Shell](/azure/cloud-shell/overview)
* [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-roles"></a>Listar funções

### <a name="list-all-available-roles"></a>Listar todas as funções disponíveis

Para as funções do RBAC de lista que estão disponíveis para atribuição de em para inspecionar as operações para que eles concedem acesso, utilizam [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-specific-role"></a>Listar uma função específica

Para listar uma função específica, utilize [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

## <a name="list-a-role-definition"></a>Listar uma definição de função

### <a name="list-a-role-definition-in-json-format"></a>Listar uma definição de função no formato JSON

Para listar uma definição de função no formato JSON, utilize [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-actions-of-a-role"></a>Lista de ações de uma função

Para listar as ações para uma função específica, utilize [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="list-access"></a>Listar o acesso

No RBAC, para acesso de lista, lista as atribuições de funções.

### <a name="list-role-assignments-at-a-specific-scope"></a>Listar atribuições de função com um âmbito específico

Pode ver todas as atribuições de função para uma subscrição especificada, o grupo de recursos ou o recurso. Por exemplo, para ver as todas as active atribuições para um grupo de recursos, utilize [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource group name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales-projectforecast | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

### <a name="list-role-assignments-for-a-user"></a>Listar atribuições de funções para um utilizador

Para listar todas as funções que são atribuídas a um utilizador especificado, utilize [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email, userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

Para listar todas as funções que são atribuídas a um utilizador especificado e as funções que são atribuídas a grupos a que pertence o utilizador, utilize [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email, userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

### <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Lista de atribuições de funções de administrador de serviços clássico e coadministradores

Para listar atribuições de funções para o administrador de subscrição clássica e coadministradores, utilize [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>Conceder acesso

No RBAC, para conceder acesso, crie uma atribuição de função.

### <a name="search-for-object-ids"></a>Procure IDs de objeto

Para atribuir uma função, precisa identificar o objeto (utilizador, grupo ou aplicação) e o escopo.

Se não souber o ID de subscrição, pode encontrá-lo na **subscrições** painel no portal do Azure, ou pode usar [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

Para obter o ID de objeto para um grupo do Azure AD, utilize [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup):

```azurepowershell
Get-AzADGroup -SearchString <group name in quotes>
```

Para obter o ID de objeto para um aplicativo ou principal de serviço do Azure AD, utilize [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

```azurepowershell
Get-AzADServicePrincipal -SearchString <service name in quotes>
```

### <a name="create-a-role-assignment-for-an-application-at-a-subscription-scope"></a>Criar uma atribuição de função para uma aplicação com um âmbito de subscrição

Para conceder acesso a uma aplicação no âmbito da subscrição, utilize [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope /subscriptions/<subscription id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Criar uma atribuição de função para um utilizador com um âmbito de grupo de recursos

Para conceder acesso a um utilizador no âmbito do grupo de recursos, utilize [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -SignInName <email, userprincipalname> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-a-group-at-a-resource-scope"></a>Criar uma atribuição de função para um grupo com um âmbito de recursos

Para conceder acesso a um grupo no âmbito do recurso, utilize [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

## <a name="remove-access"></a>Remover o acesso

No RBAC, para remover o acesso, remover uma atribuição de função usando [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>
```

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast
```

## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Conceder um acesso de grupo de recursos do Azure através do RBAC e o Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Tutorial: Criar uma função personalizada para recursos do Azure com o Azure PowerShell](tutorial-custom-role-powershell.md)
- [Gerir recursos com o Azure PowerShell](../azure-resource-manager/powershell-azure-resource-manager.md)
