---
title: Adicione ou remova atribuições de funções Azure utilizando o Azure CLI - Azure RBAC
description: Saiba como conceder acesso aos recursos do Azure para utilizadores, grupos, diretores de serviços ou identidades geridas utilizando o controlo de acesso baseado em papel Azure CLI e Azure (Azure RBAC).
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3a66482aeee7832baa91fe98357b870e2a280912
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735781"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-cli"></a>Adicione ou remova atribuições de funções Azure utilizando o Azure CLI

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Este artigo descreve como atribuir funções usando o Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de funções, deve ter:

- `Microsoft.Authorization/roleAssignments/write`e `Microsoft.Authorization/roleAssignments/delete` permissões, tais como Administrador de [Acesso ao Utilizador](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)
- [Bash em Azure Cloud Shell](/azure/cloud-shell/overview) ou [Azure CLI](/cli/azure)

## <a name="get-object-ids"></a>Obter iDs de objeto

Para adicionar ou remover atribuições de funções, poderá ser necessário especificar a identificação única de um objeto. O ID tem `11111111-1111-1111-1111-111111111111`o formato: . Pode obter o ID utilizando o portal Azure ou o Azure CLI.

### <a name="user"></a>Utilizador

Para obter o ID do objeto para um utilizador de Anúncio saque, pode utilizar o [az ad user show](/cli/azure/ad/user#az-ad-user-show).

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Grupo

Para obter o ID do objeto para um grupo Azure AD, você pode usar [az ad group show](/cli/azure/ad/group#az-ad-group-show) ou [az ad group list](/cli/azure/ad/group#az-ad-group-list).

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Aplicação

Para obter o ID do objeto para um diretor de serviço Azure AD (identidade usada por uma aplicação), pode utilizar a [lista az ad sp](/cli/azure/ad/sp#az-ad-sp-list). Para um diretor de serviço, utilize o ID do objeto e **não** o ID da aplicação.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No Azure RBAC, para conceder acesso, adiciona-se uma atribuição de funções.

### <a name="user-at-a-resource-group-scope"></a>Utilizador num âmbito de grupo de recursos

Para adicionar uma atribuição de funções para um utilizador num âmbito de grupo de recursos, use a criação de [atribuição de funções az](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

O exemplo seguinte atribui o papel de *Colaborador da Máquina Virtual* a *patlong\@contoso.com* utilizador no âmbito do grupo de recursos de venda de *pharma:*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>Usando o ID de função única

Há algumas vezes em que um nome de papel pode mudar, por exemplo:

- Está a usar o seu próprio papel personalizado e decide mudar o nome.
- Está a utilizar uma função de pré-visualização que tem **(Pré-visualização)** no nome. Quando o papel é lançado, o papel é renomeado.

> [!IMPORTANT]
> Uma versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Mesmo que um papel seja renomeado, o papel id não muda. Se estiver a usar scripts ou automação para criar as suas atribuições de papéis, é uma boa prática usar o ID de função único em vez do nome de papel. Portanto, se um papel for renomeado, os seus scripts são mais propensos a funcionar.

Para adicionar uma atribuição de funções usando o ID de função único em vez do nome de papel, use [az role assignment criar](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

O exemplo seguinte atribui o papel de [Colaborador da Máquina Virtual](built-in-roles.md#virtual-machine-contributor) ao *patlong\@contoso.com* utilizador no âmbito do grupo de recursos de venda de *pharma.* Para obter o ID de função único, você pode usar a lista de definição de [papel az](/cli/azure/role/definition#az-role-definition-list) ou ver [papéis azure incorporados](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Grupo num âmbito de subscrição

Para adicionar uma atribuição de funções para um grupo, use a [z role assignment criar](/cli/azure/role/assignment#az-role-assignment-create). Para obter informações sobre como obter a identificação do objeto do grupo, consulte [Obter iDs](#get-object-ids)de objeto .

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

O exemplo seguinte atribui o papel *de Leitor* ao grupo Ann *Mack Team* com ID 2222222-2222-2222-2222-2222222222222222222222222 22222 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 222 2222222222 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 no âmbito da subscrição.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Grupo num âmbito de recurso

Para adicionar uma atribuição de funções para um grupo, use a [z role assignment criar](/cli/azure/role/assignment#az-role-assignment-create). Para obter informações sobre como obter a identificação do objeto do grupo, consulte [Obter iDs](#get-object-ids)de objeto .

O exemplo seguinte atribui o papel de *Colaborador da Máquina Virtual* ao grupo Ann Mack *Team* com ID 22222222-2222-22222222222222222222222222222222222222 22222 222222 22222222222222 22222222222222222 2222222222222 2222222222222 22 22 numa área de recurso para uma rede virtual chamada *pharma-sales-project-network.*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Aplicação num âmbito de grupo de recursos

Para adicionar uma atribuição de funções para uma aplicação, use a criação de [atribuição de funções az](/cli/azure/role/assignment#az-role-assignment-create). Para obter informações sobre como obter a identificação do objeto da aplicação, consulte [Obter iDs](#get-object-ids)de objeto .

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

O exemplo seguinte atribui a função de *Colaborador da Máquina Virtual* a uma aplicação com o id 444444444-4444-4444-4444-4444444 44 44444 444 444 44444 44 444 44 444 444 4444 no âmbito do grupo de recursos de venda de *pharma.*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Utilizador num âmbito de subscrição

Para adicionar uma atribuição de funções para um utilizador num âmbito de subscrição, utilize a criação de [atribuição de funções az](/cli/azure/role/assignment#az-role-assignment-create). Para obter o ID de subscrição, pode encontrá-lo na lâmina **de Subscrições** no portal Azure ou pode utilizar a lista de [conta az](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

O exemplo seguinte atribui o papel *do Leitor* ao *utilizador annm\@example.com* num âmbito de subscrição.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Utilizador num âmbito de grupo de gestão

Para adicionar uma atribuição de funções para um utilizador num âmbito de grupo de gestão, use a [criação de atribuição de funções az](/cli/azure/role/assignment#az-role-assignment-create). Para obter o ID do grupo de gestão, você pode encontrá-lo na lâmina de **grupos de gestão** no portal Azure ou você pode usar a lista de [grupos de gestão de conta az](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

O exemplo seguinte atribui o papel do *Leitor de Faturação* ao *utilizador alain\@example.com* num âmbito de grupo de gestão.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Novo diretor de serviço

Se criar um novo diretor de serviço e tentar imediatamente atribuir um papel a esse diretor de serviço, essa atribuição de funções pode falhar em alguns casos. Por exemplo, se usar um script para criar uma nova identidade gerida e tentar atribuir um papel a esse diretor de serviço, a atribuição de funções pode falhar. A razão para esta falha é provavelmente um atraso de replicação. O diretor de serviço é criado numa região; no entanto, a atribuição de funções pode ocorrer em uma região diferente que ainda não replicao o diretor de serviço. Para abordar este cenário, deve especificar o tipo principal ao criar a atribuição de funções.

Para adicionar uma atribuição de funções, use a criação `--assignee-principal-type` de `ServicePrincipal`atribuição de [funções az,](/cli/azure/role/assignment#az-role-assignment-create)especifique um valor para, `--assignee-object-id`e depois definido para .

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

O exemplo seguinte atribui o papel de *Colaborador da Máquina Virtual* à identidade gerida pelo *msi-test* no âmbito do grupo de recursos de *venda de pharma:*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função

No Azure RBAC, para remover o acesso, remove-se uma atribuição de funções utilizando a [atribuição de funções az:](/cli/azure/role/assignment#az-role-assignment-delete)

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

O exemplo seguinte remove a atribuição da função de Colaborador da *Máquina Virtual* do *patlong\@contoso.com* utilizador no grupo de recursos de venda de *pharma:*

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

O exemplo seguinte remove o papel *de Leitor* do grupo Ann *Mack Team* com ID 2222222-2222-2222-2222-2222222222222222222222222 22222 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 22 222 222 222222222 22 22 22 22 22 22 22 22 22 22 22 22 22 no âmbito da subscrição. Para obter informações sobre como obter a identificação do objeto do grupo, consulte [Obter iDs](#get-object-ids)de objeto .

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

O exemplo seguinte remove o papel do Leitor de *Faturação* do *utilizador example.com alain\@* no âmbito do grupo de gestão. Para obter a identificação do grupo de gestão, pode utilizar a lista de [grupos de gestão de conta az](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Passos seguintes

- [Atribuição de funções da List Azure utilizando o Azure CLI](role-assignments-list-cli.md)
- [Utilize o Azure CLI para gerir os recursos e grupos de recursos do Azure](../azure-resource-manager/cli-azure-resource-manager.md)
