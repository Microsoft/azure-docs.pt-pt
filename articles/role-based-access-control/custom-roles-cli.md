---
title: Criar ou atualizar funções personalizadas para os recursos do Azure utilizando o Azure CLI [ Microsoft Docs
description: Aprenda a listar, criar, atualizar ou eliminar funções personalizadas com controlo de acesso baseado em papéis (RBAC) para recursos Azure utilizando o Azure CLI.
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 44676f7b92c2bcd30612295840054ab2f0c0cf12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062216"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-azure-cli"></a>Criar ou atualizar funções personalizadas para os recursos do Azure utilizando o Azure CLI

> [!IMPORTANT]
> A adição de `AssignableScopes` um grupo de gestão está atualmente em pré-visualização.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Se as [funções incorporadas para os recursos Azure](built-in-roles.md) não atenderem às necessidades específicas da sua organização, pode criar os seus próprios papéis personalizados. Este artigo descreve como listar, criar, atualizar ou eliminar funções personalizadas usando o Azure CLI.

Para um tutorial passo a passo sobre como criar um papel personalizado, consulte [Tutorial: Crie um papel personalizado para os recursos Azure usando o Azure CLI](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Pré-requisitos

Para criar papéis personalizados, precisa de:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso do Utilizador](built-in-roles.md#user-access-administrator)
- [Casca de Nuvem Azure](../cloud-shell/overview.md) ou [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar as funções personalizadas disponíveis para atribuição, utilize a [lista de definição de papéis az](/cli/azure/role/definition#az-role-definition-list). Os seguintes exemplos listam todas as funções personalizadas na subscrição atual.

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

## <a name="list-a-custom-role-definition"></a>Enumerar uma definição de papel personalizada

Para listar uma definição de função personalizada, use a [lista de definição de papéis az](/cli/azure/role/definition#az-role-definition-list). Este é o mesmo comando que usaria para um papel incorporado.

```azurecli
az role definition list --name <role_name>
```

O exemplo seguinte enumera a definição de função *de Operador de Máquina Virtual:*

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

O exemplo seguinte enumera apenas as ações da função *de Operador de Máquina Virtual:*

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

Para criar um papel personalizado, use a definição de [função az criar](/cli/azure/role/definition#az-role-definition-create). A definição de função pode ser uma descrição jSON ou um caminho para um ficheiro que contenha uma descrição JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

O exemplo seguinte cria uma função personalizada chamada *Operador de Máquina Virtual*. Esta função personalizada atribui acesso a todas as operações de leitura da *Microsoft.Compute*, *Microsoft.Storage*, e *Microsoft.Network* fornecedores de recursos e atribui acesso para iniciar, reiniciar e monitorizar máquinas virtuais. Esta função personalizada pode ser usada em duas subscrições. Este exemplo utiliza um ficheiro JSON como entrada.

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

Para atualizar uma função personalizada, utilize primeiro a lista de definição de [papel az](/cli/azure/role/definition#az-role-definition-list) para recuperar a definição de função. Em segundo lugar, faça as alterações desejadas à definição de função. Finalmente, utilize a atualização da [definição de papel az](/cli/azure/role/definition#az-role-definition-update) para salvar a definição de função atualizada.

```azurecli
az role definition update --role-definition <role_definition>
```

O exemplo seguinte adiciona o *Microsoft.Insights/diagnósticoSettings/operação* `Actions` e `AssignableScopes` adiciona um grupo de gestão para a função personalizada *do Operador de Máquina virtual.* A adição de `AssignableScopes` um grupo de gestão está atualmente em pré-visualização.

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
    "/subscriptions/33333333-3333-3333-3333-333333333333",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada

Para eliminar uma função personalizada, utilize a [definição de função az eliminar](/cli/azure/role/definition#az-role-definition-delete). Para especificar a função a eliminar, utilize o nome do papel ou o ID de função. Para determinar o ID de função, utilize a [lista de definição de funções az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

O exemplo que se segue elimina a função personalizada *do Operador de Máquina virtual.*

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Criar uma função personalizada para os recursos Azure usando o Azure CLI](tutorial-custom-role-cli.md)
- [Custom roles for Azure resources](custom-roles.md) (Funções personalizadas para recursos do Azure)
- [Operações de fornecedor de recursos do Gestor de Recursos Azure](resource-provider-operations.md)