---
title: Adicione ou remova atribuições de funções Azure usando Azure CLI - Azure RBAC
description: Saiba como conceder acesso aos recursos Azure para utilizadores, grupos, diretores de serviços ou identidades geridas utilizando o controlo de acesso baseado em funções Azure CLI e Azure (Azure RBAC).
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 95ec9a25f97154d8e2d0e2e5b5f9cd29cf7a9c31
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84983330"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-cli"></a>Adicione ou remova atribuições de funções Azure usando Azure CLI

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Este artigo descreve como atribuir funções usando Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de funções, você deve ter:

- `Microsoft.Authorization/roleAssignments/write`e `Microsoft.Authorization/roleAssignments/delete` permissões, tais como [Administrador de Acesso ao Utilizador](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)
- [Bash em Azure Cloud Shell](/azure/cloud-shell/overview) ou [Azure CLI](/cli/azure)

## <a name="get-object-ids"></a>Obtenha iDs de objeto

Para adicionar ou remover atribuições de funções, pode ser necessário especificar o ID único de um objeto. O ID tem o formato: `11111111-1111-1111-1111-111111111111` . Pode obter o ID usando o portal Azure ou Azure CLI.

### <a name="user"></a>Utilizador

Para obter o ID do objeto para um utilizador AZure AD, pode utilizar [o az ad user show](/cli/azure/ad/user#az-ad-user-show).

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Grupo

Para obter o ID do objeto para um grupo AZure AD, você pode usar [a lista de grupos de anúncios az](/cli/azure/ad/group#az-ad-group-show) ou [az ad group](/cli/azure/ad/group#az-ad-group-list).

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Aplicação

Para obter o ID do objeto para um principal de serviço AZure (identidade utilizada por uma aplicação), pode utilizar [a lista ad sp](/cli/azure/ad/sp#az-ad-sp-list). Para um principiante de serviço, utilize o ID do objeto e **não** o ID da aplicação.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No Azure RBAC, para conceder acesso, adiciona-se uma atribuição de papéis.

### <a name="user-at-a-resource-group-scope"></a>Utilizador no âmbito do grupo de recursos

Para adicionar uma atribuição de funções para um utilizador num âmbito de grupo de recursos, utilize [a az role assignment create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role {roleNameOrId} --assignee {assignee} --resource-group {resourceGroup}
```

O exemplo a seguir atribui o papel *de Contribuinte de Máquina Virtual* ao utilizador * \@ patlong contoso.com* no âmbito do grupo de recursos de vendas de *pharma:*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>Usando o ID de papel único

Há um par de vezes em que um nome pode mudar, por exemplo:

- Está a usar o seu próprio papel personalizado e decide mudar o nome.
- Está a utilizar um papel de pré-visualização que tem **(Pré-visualização)** no nome. Quando o papel é lançado, o papel é renomeado.

> [!IMPORTANT]
> Uma versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mesmo que um papel seja renomeado, o iD do papel não muda. Se estiver a usar scripts ou automação para criar as suas atribuições de papéis, é uma boa prática usar o ID de função único em vez do nome de função. Portanto, se um papel for renomeado, os seus scripts são mais propensos a funcionar.

Para adicionar uma tarefa de função utilizando o ID de função único em vez do nome de função, use [a az role assignment create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role {roleId} --assignee {assignee} --resource-group {resourceGroup}
```

O exemplo a seguir atribui o papel [de Contribuinte de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) ao utilizador * \@ patlong contoso.com* no âmbito do grupo de recursos de vendas de *pharma.* Para obter o ID de papel único, você pode usar a lista de definição de [funções az](/cli/azure/role/definition#az-role-definition-list) ou ver [funções incorporadas Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Grupo no âmbito de subscrição

Para adicionar uma atribuição de funções para um grupo, utilize [a az role assignment create](/cli/azure/role/assignment#az-role-assignment-create). Para obter informações sobre como obter a identificação do objeto do grupo, consulte [obter iDs de objetos](#get-object-ids).

```azurecli
az role assignment create --role {roleNameOrId} --assignee-object-id {assigneeObjectId} --resource-group {resourceGroup} --scope /subscriptions/{subscriptionId}
```

O exemplo a seguir atribui o papel de *Reader* ao grupo *Ann Mack Team* com iD 22222222-2222-2222-2222-2222-2222222222 num âmbito de subscrição.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Grupo em um âmbito de recursos

Para adicionar uma atribuição de funções para um grupo, utilize [a az role assignment create](/cli/azure/role/assignment#az-role-assignment-create). Para obter informações sobre como obter a identificação do objeto do grupo, consulte [obter iDs de objetos](#get-object-ids).

O exemplo a seguir atribui o papel *de Contribuinte de Máquina Virtual* ao grupo Ann Mack *Team* com o ID 2222222-2222-2222-2222-222222222222 numa área de recursos para uma rede virtual chamada *pharma-sales-project-network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Aplicação no âmbito de grupo de recursos

Para adicionar uma atribuição de função para uma aplicação, use [a az role assignment create](/cli/azure/role/assignment#az-role-assignment-create). Para obter informações sobre como obter o ID do objeto da aplicação, consulte [obter iDs de objetos](#get-object-ids).

```azurecli
az role assignment create --role {roleNameOrId} --assignee-object-id {assigneeObjectId} --resource-group {resourceGroup}
```

O exemplo a seguir atribui a função *de Contribuinte de Máquina Virtual* a uma aplicação com iD de objeto 44444444-4444-4444-4444444444444444444444444444444 no âmbito do grupo de recursos de vendas de *pharma.*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Utilizador no âmbito de subscrição

Para adicionar uma atribuição de funções para um utilizador num âmbito de subscrição, utilize [a az role assignment create](/cli/azure/role/assignment#az-role-assignment-create). Para obter o ID de subscrição, pode encontrá-lo na lâmina **de Subscrições** no portal Azure ou pode utilizar [a lista de conta az](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role {roleNameOrId} --assignee {assignee} --subscription {subscriptionNameOrId}
```

O exemplo a seguir atribui o papel *de Leitor* ao *utilizador annm \@ example.com* num âmbito de subscrição.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Utilizador no âmbito de um grupo de gestão

Para adicionar uma atribuição de funções para um utilizador num âmbito de grupo de gestão, utilize [a az role assignment create](/cli/azure/role/assignment#az-role-assignment-create). Para obter o ID do grupo de gestão, pode encontrá-lo na lâmina dos **grupos de Gestão** no portal Azure ou pode utilizar [a lista de grupos de gestão de conta az](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment create --role {roleNameOrId} --assignee {assignee} --scope /providers/Microsoft.Management/managementGroups/{groupId}
```

O exemplo a seguir atribui o papel de *Leitor de Faturação* ao *utilizador \@ alain example.com* num âmbito de grupo de gestão.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Novo diretor de serviços

Se criar um novo diretor de serviço e tentar imediatamente atribuir um papel a esse diretor de serviço, essa atribuição de funções pode falhar em alguns casos. Por exemplo, se usar um script para criar uma nova identidade gerida e tentar atribuir um papel a esse principal de serviço, a atribuição de funções pode falhar. A razão para esta falha é provavelmente um atraso de replicação. O principal serviço é criado numa região; no entanto, a atribuição de funções pode ocorrer em uma região diferente que ainda não replicou o principal serviço. Para abordar este cenário, deve especificar o tipo principal ao criar a atribuição de funções.

Para adicionar uma atribuição de funções, utilize [a az role assignment assignment create](/cli/azure/role/assignment#az-role-assignment-create), especifique um valor para , `--assignee-object-id` e, em seguida, definido `--assignee-principal-type` para `ServicePrincipal` .

```azurecli
az role assignment create --role {roleNameOrId} --assignee-object-id {assigneeObjectId} --assignee-principal-type {assigneePrincipalType} --resource-group {resourceGroup} --scope /subscriptions/{subscriptionId}
```

O exemplo a seguir atribui o papel *de Contribuinte de Máquina Virtual* à identidade gerida pelo *msi-teste* no âmbito do grupo de recursos *de vendas de pharma:*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função

No Azure RBAC, para remover o acesso, remove-se uma atribuição de funções utilizando [a atribuição de funções az:](/cli/azure/role/assignment#az-role-assignment-delete)

```azurecli
az role assignment delete --assignee {assignee} --role {roleNameOrId} --resource-group {resourceGroup}
```

O exemplo a seguir remove a atribuição de função *de contribuinte de máquina virtual* do utilizador * \@ patlong contoso.com* no grupo de recursos de vendas de *pharma:*

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

O exemplo a seguir remove o papel de *Leitor* do grupo *Ann Mack Team* com iD 22222222-2222-2222-2222-2222-2222222222 num âmbito de subscrição. Para obter informações sobre como obter a identificação do objeto do grupo, consulte [obter iDs de objetos](#get-object-ids).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

O exemplo a seguir remove o papel de Leitor de *Faturação* do *alain \@ example.com* utilizador no âmbito do grupo de gestão. Para obter o ID do grupo de gestão, você pode usar [a lista de grupo de gestão de conta az](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Próximos passos

- [ListE Azure atribui funções usando Azure CLI](role-assignments-list-cli.md)
- [Utilize o CLI Azure para gerir recursos e grupos de recursos da Azure](../azure-resource-manager/cli-azure-resource-manager.md)
