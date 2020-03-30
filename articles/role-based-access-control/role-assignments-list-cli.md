---
title: Atribuição de papéis de lista utilizando Azure RBAC e Azure CLI
description: Saiba como determinar que recursos utilizadores, grupos, diretores de serviços ou identidades geridas têm acesso a usar o controlo de acesso baseado em funções azure (RBAC) e Azure CLI.
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
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5716e7bb89d017866bd1575256e2d119bb7acbe5
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385066"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Atribuição de papéis de lista utilizando Azure RBAC e Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Este artigo descreve como listar atribuições de papéis usando o Azure CLI.

> [!NOTE]
> Se a sua organização tiver funções de gestão subcontratadas a um prestador de serviços que utiliza a [gestão de recursos delegados do Azure,](../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de funções autorizadas por esse prestador de serviços não serão mostradas aqui.

## <a name="prerequisites"></a>Pré-requisitos

- [Bash em Azure Cloud Shell](/azure/cloud-shell/overview) ou [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Listar atribuições de funções para um utilizador

Para listar as atribuições de funções para um utilizador específico, utilize a [lista de atribuição de funções az:](/cli/azure/role/assignment#az-role-assignment-list)

```azurecli-interactive
az role assignment list --assignee <assignee>
```

Por predefinição, apenas serão apresentadas atribuições de funções para a subscrição atual. Para visualizar as atribuições de funções `--all` para a subscrição atual e abaixo, adicione o parâmetro. Para ver atribuições de papéis herdados, adicione o `--include-inherited` parâmetro.

O exemplo seguinte enumera as atribuições de funções que são atribuídas diretamente ao *utilizador contoso.com\@patlong:*

```azurecli-interactive
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
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

## <a name="list-role-assignments-for-a-resource-group"></a>Listar atribuições de funções para um grupo de recursos

Para listar as atribuições de funções que existem num âmbito de grupo de recursos, utilize a [lista de atribuição de funções az:](/cli/azure/role/assignment#az-role-assignment-list)

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

O exemplo seguinte enumera as atribuições de funções para o grupo de recursos de *venda de pharma:*

```azurecli-interactive
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
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

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Listar atribuições de funções para uma subscrição

Para listar todas as atribuições de funções num âmbito de subscrição, utilize a [lista de atribuição de papéis az](/cli/azure/role/assignment#az-role-assignment-list). Para obter o ID de subscrição, pode encontrá-lo na lâmina **de Subscrições** no portal Azure ou pode utilizar a lista de [conta az](/cli/azure/account#az-account-list).

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

Exemplo:

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Atribuição de funções de lista para um grupo de gestão

Para listar todas as atribuições de funções num âmbito de grupo de gestão, utilize a [lista de atribuição de papéis az](/cli/azure/role/assignment#az-role-assignment-list). Para obter o ID do grupo de gestão, você pode encontrá-lo na lâmina de **grupos de gestão** no portal Azure ou você pode usar a lista de [grupos de gestão de conta az](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Exemplo:

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Atribuição de papéis de lista para uma identidade gerida

1. Obtenha a identificação do objeto da identidade gerida atribuída pelo sistema ou atribuída ao utilizador.

    Para obter a identificação do objeto de uma identidade gerida atribuída pelo utilizador, pode utilizar a [lista az ad sp](/cli/azure/ad/sp#az-ad-sp-list) ou [alista de identidade az](/cli/azure/identity#az-identity-list).

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    Para obter a identificação do objeto de uma identidade gerida atribuída pelo sistema, você pode usar a [lista az ad sp](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. Para listar as atribuições de funções, utilize a [lista de atribuição de papéis az](/cli/azure/role/assignment#az-role-assignment-list).

    Por predefinição, apenas serão apresentadas atribuições de funções para a subscrição atual. Para visualizar as atribuições de funções `--all` para a subscrição atual e abaixo, adicione o parâmetro. Para ver atribuições de papéis herdados, adicione o `--include-inherited` parâmetro.

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>Passos seguintes

- [Adicionar ou remover atribuições de funções utilizando o Azure RBAC e o Azure CLI](role-assignments-cli.md)
