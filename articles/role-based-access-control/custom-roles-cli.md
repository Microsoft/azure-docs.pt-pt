---
title: Criar funções personalizadas para recursos do Azure com a CLI do Azure | Documentos da Microsoft
description: Saiba como criar funções personalizadas com controlo de acesso baseado em funções (RBAC) para recursos do Azure com a CLI do Azure. Isto inclui como listar, criar, atualizar e eliminar funções personalizadas.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ebced83346a7b130598e4a5f49a72d51ffd18e4f
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56584901"
---
# <a name="create-custom-roles-for-azure-resources-using-azure-cli"></a>Criar funções personalizadas para recursos do Azure com a CLI do Azure

Se o [funções incorporadas para recursos do Azure](built-in-roles.md) não atenderem às necessidades específicas da sua organização, pode criar suas próprias funções personalizadas. Este artigo descreve como criar e gerir funções personalizadas com a CLI do Azure.

Para obter um tutorial passo a passo sobre como criar uma função personalizada, consulte [Tutorial: Criar uma função personalizada para recursos do Azure com a CLI do Azure](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Pré-requisitos

Para criar funções personalizadas, terá de:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso do Utilizador](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) ou [da CLI do Azure](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar as funções personalizadas que estão disponíveis para atribuição, utilize [lista de definições de função de az](/cli/azure/role/definition#az-role-definition-list). Os exemplos a seguir listam todas as funções personalizadas na subscrição atual.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="list-a-custom-role-definition"></a>Listar uma definição de função personalizada

Para listar uma definição de função personalizada, utilize [lista de definições de função de az](/cli/azure/role/definition#az-role-definition-list). Este é o mesmo comando que utilizaria para uma função incorporada.

```azurecli
az role definition list --name <role_name>
```

As listas de exemplo a seguir a *operador de Máquina Virtual* definição de função:

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```Output
[
  {
    "assignableScopes": [
      "/subscriptions/11111111-1111-1111-1111-111111111111"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
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
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

O exemplo seguinte lista apenas as ações do *operador de Máquina Virtual* função:

```azurecli
az role definition list --name "Virtual Machine Operator" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
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
]
```

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar uma função personalizada, utilize [criação da definição de função de az](/cli/azure/role/definition#az-role-definition-create). A definição de função pode ser uma descrição de JSON ou um caminho para um ficheiro que contém uma descrição de JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

O exemplo seguinte cria uma função personalizada com o nome *operador de Máquina Virtual*. Esta função personalizada atribui o acesso a todas as operações de leitura de *Microsoft. Compute*, *Microsoft. Storage*, e *Network* fornecedores e atribui acesso a recursos para iniciar, reiniciar e monitorizar máquinas virtuais. Esta função personalizada pode ser utilizada em duas subscrições. Este exemplo utiliza um ficheiro JSON como entrada.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
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
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Para atualizar uma função personalizada, utilize [lista de definições de função de az](/cli/azure/role/definition#az-role-definition-list) para obter a definição de função. Segundo, efetue as alterações pretendidas para a definição de função. Por último, utilize [atualização da definição de função az](/cli/azure/role/definition#az-role-definition-update) para guardar a definição de função atualizada.

```azurecli
az role definition update --role-definition <role_definition>
```

O exemplo seguinte adiciona o *Microsoft.Insights/diagnosticSettings/* operação para o *ações* do *operador de Máquina Virtual* função personalizada.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
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
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada

Para eliminar uma função personalizada, utilize [eliminar definição de função de az](/cli/azure/role/definition#az-role-definition-delete). Para especificar a função para eliminar, utilize o nome da função ou o ID de função. Para determinar o ID de função, utilize [lista de definições de função de az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

O exemplo seguinte elimina a *operador de Máquina Virtual* função personalizada.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Criar uma função personalizada para recursos do Azure com a CLI do Azure](tutorial-custom-role-cli.md)
- [Funções personalizadas para recursos do Azure](custom-roles.md)
- [Operações de fornecedor de recursos do Azure Resource Manager](resource-provider-operations.md)