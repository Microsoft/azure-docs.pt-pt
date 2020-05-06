---
title: Criar ou atualizar funções personalizadas azure usando Azure PowerShell - Azure RBAC
description: Saiba como listar, criar, atualizar ou eliminar funções personalizadas utilizando o controlo de acesso baseado em papel Azure PowerShell e Azure (Azure RBAC).
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ffb53bff4e70fbeb80e518fe13aaeaa8b396cfac
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734812"
---
# <a name="create-or-update-azure-custom-roles-using-azure-powershell"></a>Criar ou atualizar funções personalizadas do Azure utilizando o Azure PowerShell

> [!IMPORTANT]
> A adição de `AssignableScopes` um grupo de gestão está atualmente em pré-visualização.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Se os [papéis integrados](built-in-roles.md) do Azure não atenderem às necessidades específicas da sua organização, pode criar os seus próprios papéis personalizados. Este artigo descreve como listar, criar, atualizar ou eliminar funções personalizadas usando o Azure PowerShell.

Para um tutorial passo a passo sobre como criar um papel personalizado, consulte [Tutorial: Crie uma função personalizada Azure usando o Azure PowerShell](tutorial-custom-role-powershell.md).

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para criar papéis personalizados, precisa de:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso do Utilizador](built-in-roles.md#user-access-administrator)
- [Casca de Nuvem Azure](../cloud-shell/overview.md) ou [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar as funções disponíveis para atribuição num âmbito, utilize o comando [Get-AzRoleDefinition.](/powershell/module/az.resources/get-azroledefinition) O exemplo seguinte lista todas as funções disponíveis para atribuição na subscrição selecionada.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

O exemplo seguinte lista apenas as funções personalizadas disponíveis para atribuição na subscrição selecionada.

```azurepowershell
Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Se a subscrição selecionada `AssignableScopes` não estiver no papel, o papel personalizado não será listado.

## <a name="list-a-custom-role-definition"></a>Enumerar uma definição de papel personalizada

Para listar uma definição de função personalizada, utilize [get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition). Este é o mesmo comando que usas para um papel incorporado.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | ConvertTo-Json

{
  "Name": "Virtual Machine Operator",
  "Id": "00000000-0000-0000-0000-000000000000",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

O exemplo seguinte enumera apenas as ações do papel:

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Operator").Actions

"Microsoft.Storage/*/read",
"Microsoft.Network/*/read",
"Microsoft.Compute/*/read",
"Microsoft.Compute/virtualMachines/start/action",
"Microsoft.Compute/virtualMachines/restart/action",
"Microsoft.Authorization/*/read",
"Microsoft.ResourceHealth/availabilityStatuses/read",
"Microsoft.Resources/subscriptions/resourceGroups/read",
"Microsoft.Insights/alertRules/*",
"Microsoft.Insights/diagnosticSettings/*",
"Microsoft.Support/*"
```

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar uma função personalizada, utilize o comando [New-AzRoleDefinition.](/powershell/module/az.resources/new-azroledefinition) Existem dois métodos de estruturar `PSRoleDefinition` o papel, usando objeto ou um modelo JSON. 

### <a name="get-operations-for-a-resource-provider"></a>Obtenha operações para um fornecedor de recursos

Quando cria funções personalizadas, é importante conhecer todas as operações possíveis dos fornecedores de recursos.
Pode visualizar a lista de [operações](resource-provider-operations.md) do fornecedor de recursos ou pode utilizar o comando [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) para obter esta informação.
Por exemplo, se quiser verificar todas as operações disponíveis para máquinas virtuais, utilize este comando:

```azurepowershell
Get-AzProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Criar uma função personalizada com o objeto PSRoleDefinition

Quando utilizar o PowerShell para criar um papel personalizado, pode utilizar uma das [funções incorporadas](built-in-roles.md) como ponto de partida ou pode começar do zero. O primeiro exemplo nesta secção começa com um papel incorporado e depois personaliza-o com mais permissões. Editar os atributos `NotActions`para `AssignableScopes` adicionar o, `Actions`ou que deseja, e, em seguida, guardar as alterações como um novo papel.

O exemplo seguinte começa com a função [virtual de colaborador](built-in-roles.md#virtual-machine-contributor) da máquina incorporada para criar uma função personalizada chamada Operador de Máquina *Virtual*. A nova função permite o acesso a todas as operações de leitura da *Microsoft.Compute*, *Microsoft.Storage*e *Microsoft.Network* fornecedores de recursos e concede acesso para iniciar, reiniciar e monitorizar máquinas virtuais. O papel personalizado pode ser usado em duas subscrições.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.ResourceHealth/availabilityStatuses/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzRoleDefinition -Role $role
```

O exemplo que se segue mostra outra forma de criar a função personalizada do *Operador de Máquina virtual.* Começa por criar `PSRoleDefinition` um novo objeto. As operações de ação `perms` são especificadas `Actions` na variável e definidas na propriedade. A `NotActions` propriedade é definida `NotActions` lendo o papel integrado do [Virtual Machine Contributor.](built-in-roles.md#virtual-machine-contributor) Uma vez que o `NotActions`Virtual Machine [Contributor](built-in-roles.md#virtual-machine-contributor) não tem nenhuma, esta linha não é necessária, mas mostra como a informação pode ser recuperada de outra função.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read'
$perms += 'Microsoft.ResourceHealth/availabilityStatuses/read'
$perms += 'Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Criar um papel personalizado com o modelo JSON

Um modelo JSON pode ser usado como a definição de origem para o papel personalizado. O exemplo seguinte cria uma função personalizada que permite ler o acesso aos recursos de armazenamento e computação, o acesso ao suporte, e acrescenta esse papel a duas subscrições. Crie um `C:\CustomRoles\customrole1.json` novo ficheiro com o seguinte exemplo. O ID deve `null` ser definido na criação inicial de funções, uma vez que um novo ID é gerado automaticamente. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Para adicionar o papel às subscrições, execute o seguinte comando PowerShell:

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Semelhante a criar uma função personalizada, pode modificar `PSRoleDefinition` uma função personalizada existente usando o objeto ou um modelo JSON.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Atualizar um papel personalizado com o objeto PSRoleDefinition

Para modificar uma função personalizada, em primeiro lugar, utilize o comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) para recuperar a definição de função. Em segundo lugar, faça as alterações desejadas à definição de função. Por fim, utilize o comando [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) para salvar a definição de função modificada.

O exemplo seguinte `Microsoft.Insights/diagnosticSettings/*` adiciona a operação à função personalizada *do Operador de Máquinavirtual.*

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

O exemplo seguinte adiciona uma subscrição Azure aos âmbitos atribuíveis da função personalizada do Operador de *Máquina virtual.*

```azurepowershell
Get-AzSubscription -SubscriptionName Production3

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

O exemplo seguinte adiciona `AssignableScopes` um grupo de gestão à função personalizada do Operador de *Máquinavirtual.* A adição de `AssignableScopes` um grupo de gestão está atualmente em pré-visualização.

```azurepowershell
Get-AzManagementGroup

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/{groupId1}")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzManagementGroup

Id          : /providers/Microsoft.Management/managementGroups/marketing-group
Type        : /providers/Microsoft.Management/managementGroups
Name        : marketing-group
TenantId    : 99999999-9999-9999-9999-999999999999
DisplayName : Marketing group

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/marketing-group")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222,
                   /providers/Microsoft.Management/managementGroups/marketing-group}
```

### <a name="update-a-custom-role-with-a-json-template"></a>Atualizar um papel personalizado com um modelo JSON

Utilizando o modelo JSON anterior, pode modificar facilmente uma função personalizada existente para adicionar ou remover Ações. Atualize o modelo JSON e adicione a ação de leitura para a rede, como mostra o seguinte exemplo. As definições listadas no modelo não são aplicadas cumulativamente a uma definição existente, o que significa que a função aparece exatamente como especifica no modelo. Também precisa atualizar o campo id com a identificação do papel. Se não tiver a certeza do valor deste valor, pode utilizar o cmdlet [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) para obter esta informação.

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Para atualizar a função existente, execute o seguinte comando PowerShell:

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada

Para eliminar uma função personalizada, utilize o comando [Remove-AzRoleDefinition.](/powershell/module/az.resources/remove-azroledefinition)

O exemplo seguinte remove a função personalizada *do Operador de Máquina virtual.*

```azurepowershell
Get-AzRoleDefinition "Virtual Machine Operator"
Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Criar uma função personalizada azure usando o Azure PowerShell](tutorial-custom-role-powershell.md)
- [Papéis personalizados do Azure](custom-roles.md)
- [Operações de fornecedor de recursos do Gestor de Recursos Azure](resource-provider-operations.md)
