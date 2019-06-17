---
title: Lista negar atribuições para recursos do Azure com o Azure PowerShell | Documentos da Microsoft
description: Saiba como listar os utilizadores, grupos, os principais de serviço e identidades geridas que quais foi negadas o acesso a recursos do Azure específica de ações em escopos específicos com o Azure PowerShell.
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
ms.openlocfilehash: c1ea26fdb4d60262f89ea6ab0f87220a08c01e68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110488"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Lista negar atribuições para recursos do Azure com o Azure PowerShell

[Negar atribuições](deny-assignments.md) impedir que os utilizadores a efetuar ações de recursos do Azure específica, mesmo se uma atribuição de função lhes concede acesso. Este artigo descreve como listar negar atribuições com o Azure PowerShell.

> [!NOTE]
> Não é possível diretamente criar seu próprio negar atribuições. Para obter informações sobre como negar atribuições são criadas, consulte [negar atribuições](deny-assignments.md).

## <a name="prerequisites"></a>Pré-requisitos

Para obter informações sobre uma atribuição de negar, tem de ter:

- `Microsoft.Authorization/denyAssignments/read` permissão, o que está incluído na maioria [funções incorporadas para recursos do Azure](built-in-roles.md)
- [PowerShell no Azure Cloud Shell](/azure/cloud-shell/overview) ou [o Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Lista de atribuições de negação

### <a name="list-all-deny-assignments"></a>Lista todas as atribuições de negação

Para listar todos negar atribuições para a subscrição atual, utilize [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

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

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Lista de atribuições com um âmbito de grupo de recursos de negação

Para listar todas as atribuições com um âmbito de grupo de recursos, utilize de negação [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

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

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Lista negar atribuições no âmbito da subscrição

Para listar todos negar atribuições no âmbito da subscrição, utilize [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). Para obter o ID de subscrição, pode encontrá-lo sobre a **subscrições** painel no portal do Azure, ou pode usar [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Passos Seguintes

- [Compreender negar atribuições para recursos do Azure](deny-assignments.md)
- [Lista negar atribuições para recursos do Azure no portal do Azure](deny-assignments-portal.md)
- [Lista de atribuições para recursos do Azure com a API REST de negação](deny-assignments-rest.md)