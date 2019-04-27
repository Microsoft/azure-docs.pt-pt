---
title: Gerir o acesso aos recursos do Azure através do RBAC e CLI do Azure | Documentos da Microsoft
description: Saiba como gerir o acesso aos recursos do Azure para utilizadores, grupos e aplicações que utilizam o controlo de acesso baseado em funções (RBAC) e a CLI do Azure. Isto inclui como listar, conceder e remover acesso.
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
ms.date: 04/17/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1cc3d3eca4063a8120851a9d3de1a85292eacb11
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344568"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>Gerir o acesso aos recursos do Azure através do RBAC e CLI do Azure

[Controlo de acesso baseado em funções (RBAC)](overview.md) é a maneira que gerencie o acesso aos recursos do Azure. Este artigo descreve como gerir o acesso para utilizadores, grupos e aplicações através do RBAC e CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para gerir o acesso, precisa de um dos seguintes:

* [Bash no Azure Cloud Shell](/azure/cloud-shell/overview)
* [CLI do Azure](/cli/azure)

## <a name="list-roles"></a>Listar funções

Para listar todas as definições de funções disponíveis, utilize [lista de definições de função de az](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

O exemplo seguinte lista o nome e descrição de todas as definições de funções disponíveis:

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

O exemplo seguinte lista todas as definições de função incorporada de:

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

## <a name="list-a-role-definition"></a>Listar uma definição de função

Para listar uma definição de função, utilize [lista de definições de função de az](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

As listas de exemplo a seguir a *contribuinte* definição de função:

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

### <a name="list-actions-of-a-role"></a>Lista de ações de uma função

O exemplo seguinte lista apenas o *ações* e *notActions* do *contribuinte* função:

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

O exemplo seguinte lista apenas as ações do *contribuinte de Máquina Virtual* função:

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

## <a name="list-access"></a>Listar o acesso

No RBAC, para acesso de lista, lista as atribuições de funções.

### <a name="list-role-assignments-for-a-user"></a>Listar atribuições de funções para um utilizador

Para listar as atribuições de funções para um utilizador específico, utilize [lista de atribuições de função de az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Por predefinição, serão apresentadas apenas as atribuições de âmbito para a subscrição. Para ver as atribuições de âmbito por recurso ou grupo, utilize `--all`.

O exemplo seguinte lista as atribuições de funções que são atribuídas diretamente para o *patlong\@contoso.com* utilizador:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>Listar atribuições de funções para um grupo de recursos

Para listar as atribuições de funções que existem para um grupo de recursos, utilize [lista de atribuições de função de az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

O exemplo seguinte lista as atribuições de funções para o *vendas pharma* grupo de recursos:

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="grant-access"></a>Conceder acesso

No RBAC, para conceder acesso, crie uma atribuição de função.

### <a name="create-a-role-assignment-for-a-user"></a>Criar uma atribuição de função para um utilizador

Para criar uma atribuição de função para um utilizador no âmbito do grupo de recursos, utilize [criação da atribuição de função de az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

O exemplo seguinte atribui o *contribuinte de Máquina Virtual* função *patlong\@contoso.com* utilizador no *pharma vendas* âmbito do grupo de recursos:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Criar uma atribuição de função com o ID exclusivo da função

Existem duas vezes, quando um nome de função podem ser alteradas, por exemplo:

- Estiver a utilizar a sua própria função personalizada e optar por alterar o nome.
- Estiver a utilizar uma função de pré-visualização que tenha **(pré-visualização)** no nome. Quando a função for lançada, a função foi mudada.

> [!IMPORTANT]
> Uma versão de pré-visualização é fornecida sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mesmo que uma função for renomeada, o ID de função não é alterada. Se estiver a utilizar scripts ou a automatização para criar as suas atribuições de função, é uma prática recomendada usar o ID exclusivo da função em vez do nome de função. Por conseguinte, se uma função for renomeada, seus scripts têm maior probabilidade de funcionar.

Para criar uma atribuição de função com o ID exclusivo da função em vez do nome de função, utilize [criação da atribuição de função de az](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

O exemplo seguinte atribui o [contribuinte de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) função *patlong\@contoso.com* utilizador no *pharma vendas* âmbito do grupo de recursos. Para obter o ID exclusivo da função, pode usar [lista de definições de função de az](/cli/azure/role/definition#az-role-definition-list) ou consulte [funções incorporadas para recursos do Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>Criar uma atribuição de função para um grupo

Para criar uma atribuição de função para um grupo, utilize [criação da atribuição de função de az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

O exemplo seguinte atribui o *leitor* função para o *Ann Mack equipe* grupo com o ID 22222222-2222-2222-2222-222222222222 no âmbito da subscrição. Para obter o ID do grupo, pode usar [lista de grupos do ad az](/cli/azure/ad/group#az-ad-group-list) ou [show de grupo do ad az](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

O exemplo seguinte atribui o *contribuinte de Máquina Virtual* função para o *Ann Mack equipe* grupo com o ID 22222222-2222-2222-2222-222222222222 num âmbito de recursos para uma rede virtual denominada *pharma vendas-projeto de rede*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Criar uma atribuição de função para uma aplicação

Para criar uma função para uma aplicação, utilize [criação da atribuição de função de az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

O exemplo seguinte atribui o *contribuinte de Máquina Virtual* função a uma aplicação com o ID de objeto 44444444-4444-4444-4444-444444444444 no *vendas pharma* âmbito do grupo de recursos. Para obter o ID de objeto do aplicativo, pode usar [lista de aplicações do ad az](/cli/azure/ad/app#az-ad-app-list) ou [show do az ad app](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

## <a name="remove-access"></a>Remover o acesso

No RBAC, para remover o acesso, remover uma atribuição de função usando [eliminar atribuição de função de az](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

O exemplo seguinte remove o *contribuinte de Máquina Virtual* atribuição de função da *patlong\@contoso.com* utilizador na *pharma vendas* grupo de recursos:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

O exemplo seguinte remove o *leitor* função a partir do *Ann Mack equipe* grupo com o ID 22222222-2222-2222-2222-222222222222 no âmbito da subscrição. Para obter o ID do grupo, pode usar [lista de grupos do ad az](/cli/azure/ad/group#az-ad-group-list) ou [show de grupo do ad az](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Criar uma função personalizada para recursos do Azure com a CLI do Azure](tutorial-custom-role-cli.md)
- [Utilizar a CLI do Azure para gerir recursos do Azure e grupos de recursos](../azure-resource-manager/cli-azure-resource-manager.md)
