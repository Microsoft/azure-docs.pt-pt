---
title: List Azure nega atribuições usando Azure PowerShell - Azure RBAC
description: Saiba como listar os utilizadores, grupos, diretores de serviçoe identidades geridas que foram negados acesso a ações específicas de recursos Azure em âmbitos específicos utilizando o Controlo de Acesso baseado em papéis Azure PowerShell e Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cd852d19b284f97995855fe06c97ea0ea69be293
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733967"
---
# <a name="list-azure-deny-assignments-using-azure-powershell"></a>List Azure nega atribuições usando Azure PowerShell

[O Azure nega que as atribuições](deny-assignments.md) impeçam os utilizadores de realizarem ações específicas de recursos Do Azure, mesmo que uma atribuição de funções lhes conceda acesso. Este artigo descreve como listar missões de negação usando o Azure PowerShell.

> [!NOTE]
> Não podes criar diretamente as tuas próprias missões de negação. Para obter informações sobre como as missões de negação são criadas, consulte [Azure negar atribuições.](deny-assignments.md)

## <a name="prerequisites"></a>Pré-requisitos

Para obter informações sobre uma atribuição de negação, deve ter:

- `Microsoft.Authorization/denyAssignments/read`permissão, que está incluída na maioria das [funções azure incorporadas](built-in-roles.md)
- [PowerShell em Azure Cloud Shell](/azure/cloud-shell/overview) ou [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Listar atribuições de negação

### <a name="list-all-deny-assignments"></a>Enumerar todas as missões de negação

Para listar todas as atribuições de negar para a subscrição atual, utilize [a Atribuição Get-AzDeny](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Lista nega atribuições num âmbito de grupo de recursos

Para listar todas as atribuições de negar num âmbito de grupo de recursos, utilize [a Atribuição Get-AzDeny](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Lista nega atribuições num âmbito de subscrição

Para listar todas as atribuições de negar num âmbito de subscrição, utilize [a Atribuição Get-AzDeny](/powershell/module/az.resources/get-azdenyassignment). Para obter o ID de subscrição, pode encontrá-lo na lâmina **de Subscrições** no portal Azure ou pode utilizar [o Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Passos seguintes

- [Entenda Azure negar atribuições](deny-assignments.md)
- [List Azure nega atribuições usando o portal Azure](deny-assignments-portal.md)
- [List Azure nega atribuições usando a API REST](deny-assignments-rest.md)