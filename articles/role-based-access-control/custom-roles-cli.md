---
title: Criar ou atualizar funções personalizadas Azure usando Azure CLI - Azure RBAC
description: Saiba como listar, criar, atualizar ou eliminar funções personalizadas Azure usando o controlo de acesso baseado em funções Azure CLI e Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 31dabcf77f0db76047919fa76d00f1c5ed3c96d6
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369145"
---
# <a name="create-or-update-azure-custom-roles-using-azure-cli"></a>Criar ou atualizar funções personalizadas Azure usando O Azure CLI

> [!IMPORTANT]
> A adição de um grupo de gestão `AssignableScopes` está atualmente em pré-visualização.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se os [papéis incorporados do Azure](built-in-roles.md) não corresponderem às necessidades específicas da sua organização, pode criar os seus próprios papéis personalizados. Este artigo descreve como listar, criar, atualizar ou eliminar funções personalizadas usando o Azure CLI.

Para um tutorial passo a passo sobre como criar um papel personalizado, consulte [Tutorial: Crie um papel personalizado Azure usando O Azure CLI](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Pré-requisitos

Para criar funções personalizadas, é necessário:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso do Utilizador](built-in-roles.md#user-access-administrator)
- [Concha de nuvem Azure](../cloud-shell/overview.md) ou [CLI Azure](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar funções personalizadas disponíveis para atribuição, utilize [a lista de definição de funções az](/cli/azure/role/definition#az-role-definition-list). O exemplo a seguir lista todas as funções personalizadas na subscrição atual.

```azurecli
az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
```

```json
[
  {
    "roleName": "My Management Contributor",
    "type": "CustomRole"
  },
  {
    "roleName": "My Service Reader Role",
    "type": "CustomRole"
  },
  {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole"
  }
]
```

## <a name="list-a-custom-role-definition"></a>Listar uma definição de função personalizada

Para listar uma definição de função personalizada, utilize [a lista de definição de função az](/cli/azure/role/definition#az-role-definition-list). Este é o mesmo comando que usarias para um papel incorporado.

```azurecli
az role definition list --name {roleName}
```

O exemplo a seguir enumera a definição de função *do Operador de Máquina Virtual:*

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
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

O exemplo a seguir enumera apenas as ações da função *de Operador de Máquina Virtual:*

```azurecli
az role definition list --name "Virtual Machine Operator" --output json --query '[].permissions[0].actions'
```

```json
[
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
]
```

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar um papel personalizado, utilize [a definição de função az create](/cli/azure/role/definition#az-role-definition-create). A definição de função pode ser uma descrição de JSON ou um caminho para um ficheiro que contenha uma descrição do JSON.

```azurecli
az role definition create --role-definition {roleDefinition}
```

O exemplo a seguir cria uma função personalizada chamada *Operador de Máquina Virtual.* Esta função personalizada atribui acesso a todas as operações de leitura de *microsoft.Compute,* *Microsoft.Storage* e *Microsoft.Network* fornecedores de recursos e atribui acesso a máquinas virtuais de arranque, reinicio e monitorização. Esta função personalizada pode ser usada em duas subscrições. Este exemplo utiliza um ficheiro JSON como entrada.

vmoperator.jsem

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
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Para atualizar uma função personalizada, utilize primeiro [a lista de definição de funções az](/cli/azure/role/definition#az-role-definition-list) para recuperar a definição de função. Em segundo lugar, faça as alterações desejadas à definição de papel. Finalmente, utilize [a atualização da definição de função az](/cli/azure/role/definition#az-role-definition-update) para salvar a definição de função atualizada.

```azurecli
az role definition update --role-definition {roleDefinition}
```

O exemplo a seguir adiciona o *Microsoft.Insights/diagnosticSettings/operação* `Actions` e adiciona um grupo de gestão para o papel personalizado do Operador de Máquina `AssignableScopes` *Virtual.* A adição de um grupo de gestão `AssignableScopes` está atualmente em pré-visualização.

vmoperator.jsem

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
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada

Para eliminar uma função personalizada, utilize [a definição de função az delete](/cli/azure/role/definition#az-role-definition-delete). Para especificar a função de eliminar, utilize o nome da função ou o ID da função. Para determinar o ID de função, utilize [a lista de definição de funções az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name {roleNameOrId}
```

O exemplo a seguir elimina a função personalizada *do Operador da Máquina Virtual.*

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Criar um papel personalizado Azure usando O Azure CLI](tutorial-custom-role-cli.md)
- [Funções personalizadas do Azure](custom-roles.md)
- [Operações de fornecedor de recursos Azure](resource-provider-operations.md)