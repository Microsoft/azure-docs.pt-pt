---
title: Adicione ou remova atribuições de funções Azure usando Azure PowerShell - Azure RBAC
description: Saiba como conceder acesso aos recursos Azure para utilizadores, grupos, principais serviços ou identidades geridas utilizando o controlo de acesso baseado em funções Azure PowerShell e Azure (Azure RBAC).
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9b0df4337a5e5faff3427222fb66caf8e02184a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86146658"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>Utilizar o Azure PowerShell para adicionar ou remover atribuições de funções do Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Este artigo descreve como atribuir funções usando a Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de funções, você deve ter:

- `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete` permissões, tais como [Administrador de Acesso ao Utilizador](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)
- [PowerShell em Azure Cloud Shell](/azure/cloud-shell/overview) ou [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Obtenha iDs de objeto

Para adicionar ou remover atribuições de funções, pode ser necessário especificar o ID único de um objeto. O ID tem o formato: `11111111-1111-1111-1111-111111111111` . Pode obter o ID usando o portal Azure ou Azure PowerShell.

### <a name="user"></a>Utilizador

Para obter o ID do objeto para um utilizador AD Azure, pode utilizar [o Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Group

Para obter o ID do objeto para um grupo AD Azure, pode utilizar [o Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Aplicação

Para obter o ID do objeto para um principal de serviço AZure (identidade utilizada por uma aplicação), pode utilizar [o Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). Para um principiante de serviço, utilize o ID do objeto e **não** o ID da aplicação.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No Azure RBAC, para conceder acesso, adiciona-se uma atribuição de papéis.

### <a name="user-at-a-resource-group-scope"></a>Utilizador no âmbito do grupo de recursos

Para adicionar uma atribuição de funções para um utilizador num âmbito de grupo de recursos, utilize [o New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="using-the-unique-role-id"></a>Usando o ID de papel único

Há um par de vezes em que um nome pode mudar, por exemplo:

- Está a usar o seu próprio papel personalizado e decide mudar o nome.
- Está a utilizar um papel de pré-visualização que tem **(Pré-visualização)** no nome. Quando o papel é lançado, o papel é renomeado.

> [!IMPORTANT]
> Uma versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mesmo que um papel seja renomeado, o iD do papel não muda. Se estiver a usar scripts ou automação para criar as suas atribuições de papéis, é uma boa prática usar o ID de função único em vez do nome de função. Portanto, se um papel for renomeado, os seus scripts são mais propensos a funcionar.

Para adicionar uma tarefa de função utilizando o ID de função único em vez do nome de função, utilize [o Design New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -Scope <resource_group_name/resource/management groups>
```

O exemplo a seguir atribui o papel [de Contribuinte de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) ao *alain \@ example.com* utilizador no âmbito do grupo de recursos *de vendas de pharma.* Para obter o ID de papel único, você pode usar [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) ou ver [funções incorporadas Azure](built-in-roles.md).

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="group-at-a-resource-scope"></a>Grupo em um âmbito de recursos

Para adicionar uma atribuição de funções para um grupo num âmbito de recursos, utilize [a Assinatura New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Para obter informações sobre como obter a identificação do objeto do grupo, consulte [obter iDs de objetos](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
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

### <a name="application-at-a-subscription-scope"></a>Aplicação no âmbito de subscrição

Para adicionar uma atribuição de função para uma aplicação no âmbito de subscrição, utilize [o New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Para obter informações sobre como obter o ID do objeto da aplicação, consulte [obter iDs de objetos](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
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

### <a name="user-at-a-management-group-scope"></a>Utilizador no âmbito de um grupo de gestão

Para adicionar uma atribuição de funções para um utilizador no âmbito de um grupo de gestão, utilize [o New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Para obter o ID do grupo de gestão, pode encontrá-lo na lâmina dos **grupos de Gestão** no portal Azure ou pode utilizar o [Grupo Get-AzManagement](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função

No Azure RBAC, para remover o acesso, remove-se uma atribuição de funções utilizando [o Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

O exemplo a seguir remove a atribuição de função *de contribuinte de máquina virtual* do utilizador *alain \@ example.com* no grupo de recursos *de vendas de pharma:*

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

O exemplo a seguir remove o papel <role_name> de <object_id> num âmbito de subscrição.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

O exemplo a seguir elimina o papel <role_name> da <object_id> no âmbito do grupo de gestão.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Se receber a mensagem de erro: "As informações fornecidas não mapeiam para uma atribuição de funções", certifique-se de que também especifica os `-Scope` parâmetros ou `-ResourceGroupName` parâmetros. Para mais informações, consulte [Troubleshoot Azure RBAC](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="next-steps"></a>Passos seguintes

- [List Azure fun assignments using Azure PowerShell](role-assignments-list-powershell.md)
- [Tutorial: Conceder a um grupo acesso aos recursos da Azure utilizando a Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Tutorial: Criar um papel personalizado Azure usando Azure PowerShell](tutorial-custom-role-powershell.md)
- [Gerir recursos com a Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
