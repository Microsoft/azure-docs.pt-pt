---
title: List Azure fun assignments using Azure PowerShell - Azure RBAC
description: Saiba como determinar quais os recursos que os utilizadores, grupos, principais de serviços ou identidades geridas têm acesso à utilização do controlo de acesso baseado em funções Azure PowerShell e Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/28/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 53f31bca59bf316b6664a6c9daec886c84c1b072
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648349"
---
# <a name="list-azure-role-assignments-using-azure-powershell"></a>List Azure fun assignments using Azure PowerShell

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Este artigo descreve como listar atribuições de funções usando Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> Se a sua organização tiver funções de gestão subcontratadas a um prestador de serviços que utilize [a Azure delegada em gestão de recursos,](../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de funções autorizadas por esse prestador de serviços não serão mostradas aqui.

## <a name="prerequisites"></a>Pré-requisitos

- [PowerShell em Azure Cloud Shell](../cloud-shell/overview.md) ou [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-role-assignments-for-the-current-subscription"></a>Atribuições de funções de lista para a subscrição atual

A maneira mais fácil de obter uma lista de todas as atribuições de papel na subscrição atual (incluindo atribuições de papéis herdados de grupos de raiz e gestão) é usar [a Assinatura Get-AzRole](/powershell/module/az.resources/get-azroleassignment) sem parâmetros.

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Listar atribuições de funções para uma subscrição

Para listar todas as atribuições de funções num âmbito de subscrição, utilize [o Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Para obter o ID de subscrição, pode encontrá-lo na lâmina **de subscrições** no portal Azure ou pode utilizar [a subscrição Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>Listar atribuições de funções para um utilizador

Para listar todas as funções atribuídas a um utilizador especificado, utilize [o Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

Para listar todas as funções atribuídas a um utilizador especificado e as funções atribuídas aos grupos a que o utilizador pertence, utilize [a assinatura Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>Listar atribuições de funções para um grupo de recursos

Para listar todas as atribuições de funções num âmbito de grupo de recursos, utilize [o Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>Atribuições de funções de lista para um grupo de gestão

Para listar todas as atribuições de funções num âmbito de grupo de gestão, utilize [o Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Para obter o ID do grupo de gestão, pode encontrá-lo na lâmina dos **grupos de Gestão** no portal Azure ou pode utilizar o [Grupo Get-AzManagement](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-a-resource"></a>Listar atribuições de funções para um recurso

Para listar atribuições de funções para um recurso específico, utilize [a Assinatura Get-AzRole](/powershell/module/az.resources/get-azroleassignment) e o `-Scope` parâmetro. O âmbito será diferente dependendo do recurso. Para obter o âmbito, pode correr `Get-AzRoleAssignment` sem parâmetros para listar todas as atribuições de funções e, em seguida, encontrar o âmbito que pretende listar.

```azurepowershell
Get-AzRoleAssignment -Scope "/subscriptions/<subscription_id>/resourcegroups/<resource_group_name>/providers/<provider_name>/<resource_type>/<resource>
```

Este exemplo que se segue mostra como listar as atribuições de funções para uma conta de armazenamento. Note que este comando também lista atribuições de funções em âmbitos mais elevados, tais como grupos de recursos e subscrições, que se aplicam a esta conta de armazenamento.

```Example
PS C:\> Get-AzRoleAssignment -Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"
```

Se quiser apenas listar atribuições de funções que são atribuídas diretamente num recurso, pode utilizar o comando [Where-Object](/powershell/module/microsoft.powershell.core/where-object) para filtrar a lista.

```Example
PS C:\> Get-AzRoleAssignment | Where-Object {$_.Scope -eq "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"}
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Listar atribuições de funções para administrador de serviço clássico e coadministradores

Para listar atribuições de funções para o administrador de subscrição clássico e coadministradores, utilize [a assinatura Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Listar atribuições de funções para uma identidade gerida

1. Obtenha a identificação do objeto da identidade gerida atribuída pelo sistema ou atribuída pelo utilizador. 

    Para obter o ID do objeto de uma identidade gerida atribuída pelo utilizador, pode utilizar [o Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. Para listar as atribuições de funções, utilize [o Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>Próximos passos

- [Utilizar o Azure PowerShell para adicionar ou remover atribuições de funções do Azure](role-assignments-powershell.md)