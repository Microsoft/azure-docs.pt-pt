---
title: List Azure fun assignments using Azure CLI - Azure RBAC
description: Saiba como determinar quais os recursos que os utilizadores, grupos, principais de serviços ou identidades geridas têm acesso à utilização do controlo de acesso baseado em funções Azure CLI e Azure (Azure RBAC).
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
ms.date: 10/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cc64e314a8acb035736df0521987cb78a7297326
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556930"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>ListE Azure atribui funções usando Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Este artigo descreve como listar atribuições de funções usando Azure CLI.

> [!NOTE]
> Se a sua organização tiver funções de gestão subcontratadas a um prestador de serviços que utilize [a Azure delegada em gestão de recursos,](../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de funções autorizadas por esse prestador de serviços não serão mostradas aqui.

## <a name="prerequisites"></a>Pré-requisitos

- [Bash em Azure Cloud Shell](../cloud-shell/overview.md) ou [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Listar atribuições de funções para um utilizador

Para listar as atribuições de funções para um utilizador específico, utilize [a lista de atribuições de funções az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee {assignee}
```

Por predefinição, apenas serão apresentadas atribuições de funções para a subscrição atual. Para visualizar as atribuições de funções para a subscrição atual e abaixo, adicione o `--all` parâmetro. Para ver as atribuições de funções herdadas, adicione o `--include-inherited` parâmetro.

O exemplo a seguir enumera as atribuições de funções que são atribuídas diretamente ao *utilizador \@ patlong contoso.com:*

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  }
]
```

## <a name="list-role-assignments-for-a-resource-group"></a>Listar atribuições de funções para um grupo de recursos

Para listar as atribuições de funções que existem num âmbito de grupo de recursos, utilize [a lista de atribuição de funções az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group {resourceGroup}
```

O exemplo a seguir enumera as atribuições de funções para o grupo de recursos *de vendas de pharma:*

```azurecli
az role assignment list --resource-group pharma-sales --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  
  ...

]
```

## <a name="list-role-assignments-for-a-subscription"></a>Listar atribuições de funções para uma subscrição

Para listar todas as atribuições de funções num âmbito de subscrição, utilize [a lista de atribuição de funções az](/cli/azure/role/assignment#az-role-assignment-list). Para obter o ID de subscrição, pode encontrá-lo na lâmina **de Subscrições** no portal Azure ou pode utilizar [a lista de conta az](/cli/azure/account#az-account-list).

```azurecli
az role assignment list --subscription {subscriptionNameOrId}
```

Exemplo:

```azurecli
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "Subscription Admins",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },

  ...

]
```

## <a name="list-role-assignments-for-a-management-group"></a>Atribuições de funções de lista para um grupo de gestão

Para listar todas as atribuições de funções num âmbito de grupo de gestão, utilize [a lista de atribuição de funções az](/cli/azure/role/assignment#az-role-assignment-list). Para obter o ID do grupo de gestão, pode encontrá-lo na lâmina dos **grupos de Gestão** no portal Azure ou pode utilizar [a lista de grupos de gestão de conta az](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/{groupId}
```

Exemplo:

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/sales-group --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  }
]
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Listar atribuições de funções para uma identidade gerida

1. Obtenha a identificação principal da identidade gerida atribuída pelo sistema ou atribuída ao utilizador.

    Para obter o ID principal de uma identidade gerida atribuída ao utilizador, pode utilizar [a lista de anúncios az ou](/cli/azure/ad/sp#az-ad-sp-list) a lista de [identidades az.](/cli/azure/identity#az-identity-list)

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    Para obter a identificação principal de uma identidade gerida atribuída ao sistema, você pode usar [a lista de anúncios az sp](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. Para listar as atribuições de funções, utilize [a lista de atribuições de funções az](/cli/azure/role/assignment#az-role-assignment-list).

    Por predefinição, apenas serão apresentadas atribuições de funções para a subscrição atual. Para visualizar as atribuições de funções para a subscrição atual e abaixo, adicione o `--all` parâmetro. Para ver as atribuições de funções herdadas, adicione o `--include-inherited` parâmetro.

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções Azure usando Azure CLI](role-assignments-cli.md)