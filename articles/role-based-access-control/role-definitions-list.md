---
title: Lista de definições de funções no Azure RBAC utilizando o portal Azure, Azure PowerShell, Azure CLI ou REST API ; Microsoft Docs
description: Saiba como listar papéis incorporados e personalizados no Azure RBAC utilizando o portal Azure, Azure PowerShell, Azure CLI ou REST API.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: aa888eedc81ceb3188f801e273c70722207bf512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062993"
---
# <a name="list-role-definitions-in-azure-rbac"></a>Lista de definições de papel no Azure RBAC

Uma definição de papel é uma coleção de permissões que podem ser realizadas, tais como ler, escrever e excluir. Normalmente chama-se um papel. O [controlo de acesso baseado em funções azure (RBAC)](overview.md) tem mais de 120 [funções incorporadas](built-in-roles.md) ou pode criar as suas próprias funções personalizadas. Este artigo descreve como listar as funções incorporadas e personalizadas que pode usar para dar acesso aos recursos do Azure.

Para ver a lista de funções de administrador para o Diretório Ativo do Azure, consulte [permissões de funções de administrador no Diretório Ativo do Azure](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="azure-portal"></a>Portal do Azure

### <a name="list-all-roles"></a>Enumerar todas as funções

Siga estes passos para enumerar todas as funções no portal Azure.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, selecione qualquer âmbito. Por exemplo, pode selecionar grupos de **Gestão**, **Subscrições, Grupos** **de Recursos**ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controlo de acesso (IAM)**.

1. Clique no separador **Roles** para ver uma lista de todas as funções incorporadas e personalizadas.

   Pode ver o número de utilizadores e grupos que são atribuídos a cada função no âmbito atual.

   ![Lista de funções](./media/role-definitions-list/roles-list.png)

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Enumerar todas as funções

Para listar todas as funções no Azure PowerShell, utilize [a Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

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

### <a name="list-a-role-definition"></a>Enumerar uma definição de papel

Para listar os detalhes de uma função específica, utilize [a Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name>
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

### <a name="list-a-role-definition-in-json-format"></a>Enumerar uma definição de papel no formato JSON

Para listar uma função no formato JSON, utilize [a Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
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

### <a name="list-permissions-of-a-role-definition"></a>Lista de permissões de uma definição de papel

Para listar as permissões para uma função específica, utilize [a Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
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

## <a name="azure-cli"></a>CLI do Azure

### <a name="list-all-roles"></a>Enumerar todas as funções

Para listar todas as funções no Azure CLI, utilize a [lista de definição de papéis az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition list
```

O exemplo seguinte enumera o nome e a descrição de todas as definições de funções disponíveis:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

O exemplo seguinte enumera todas as funções incorporadas.

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

### <a name="list-a-role-definition"></a>Enumerar uma definição de papel

Para listar detalhes de um papel, use a lista de definição de [papéis az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition list --name <role_name>
```

O exemplo seguinte enumera a definição de função *do Contribuinte:*

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-permissions-of-a-role-definition"></a>Lista de permissões de uma definição de papel

O exemplo seguinte enumera apenas as *ações* e *não as ações* do papel de *Contribuinte.*

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

O exemplo seguinte enumera apenas as ações do papel de Colaborador de *Máquina Virtual.*

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="rest-api"></a>API REST

### <a name="list-role-definitions"></a>Listar definições de função

Para listar definições de papéis, utilize as [Definições](/rest/api/authorization/roledefinitions/list) de Papel - Lista REST API. Para refinar os seus resultados, especifice um âmbito e um filtro opcional.

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{scope}* com o âmbito para o qual pretende enumerar as definições de função.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Recurso |

    No exemplo anterior, o microsoft.web é um fornecedor de recursos que se refere a uma instância do Serviço de Aplicações. Da mesma forma, pode utilizar quaisquer outros fornecedores de recursos e especificar o âmbito. Para mais informações, consulte [os fornecedores e tipos de recursos da Azure](../azure-resource-manager/management/resource-providers-and-types.md) e apoiou as operações de fornecedor de recursos do Gestor de [Recursos Azure.](resource-provider-operations.md)  
     
1. Substitua *{filter}* com a condição que pretende aplicar para filtrar a lista de definição de funções.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrição |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | Enumera as definições de funções para o âmbito especificado e quaisquer subscópios. |
    > | `$filter=type+eq+'{type}'` | Lista as definições de funções do tipo especificado. Tipo de papel `CustomRole` `BuiltInRole`pode ser ou . |

### <a name="list-a-role-definition"></a>Enumerar uma definição de papel

Para listar os detalhes de uma função específica, utilize as [Definições](/rest/api/authorization/roledefinitions/get) de Papel - Obter ou [Definições](/rest/api/authorization/roledefinitions/getbyid) de Papel - Obter Por Id REST API.

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    Para uma definição de função de nível de diretório, pode utilizar este pedido:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{scope}* com o âmbito para o qual pretende enumerar a definição de função.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Recurso |
     
1. Substitua *{roleDefinitionId}* com o identificador de definição de funções.

## <a name="next-steps"></a>Passos seguintes

- [Built-in roles for Azure resources](built-in-roles.md) (Funções incorporadas para recursos do Azure)
- [Custom roles for Azure resources](custom-roles.md) (Funções personalizadas para recursos do Azure)
- [Lista de atribuições de funções utilizando o Azure RBAC e o portal Azure](role-assignments-list-portal.md)
- [Adicionar ou remover atribuições de funções utilizando o Azure RBAC e o portal Azure](role-assignments-portal.md)
