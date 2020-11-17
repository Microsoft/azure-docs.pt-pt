---
title: Adicione ou remova atribuições de funções Azure usando Azure CLI - Azure RBAC
description: Saiba como conceder acesso aos recursos Azure para utilizadores, grupos, diretores de serviços ou identidades geridas utilizando o controlo de acesso baseado em funções Azure CLI e Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2020
ms.author: rolyon
ms.custom: contperfq1
ms.openlocfilehash: 75f2eb90e2418b57e5e0a358fbd6282b20ddc9e7
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648366"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-cli"></a>Utilizar a CLI do Azure para adicionar ou remover atribuições de funções do Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Este artigo descreve como atribuir funções usando Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de funções, você deve ter:

- `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete` permissões, tais como [Administrador de Acesso ao Utilizador](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)
- [Bash em Azure Cloud Shell](../cloud-shell/overview.md) ou [Azure CLI](/cli/azure)

## <a name="steps-to-add-a-role-assignment"></a>Passos para adicionar uma atribuição de função

No Azure RBAC, para conceder acesso, adiciona-se uma atribuição de papéis. Uma atribuição de função é composta por três elementos: principal de segurança, definição de função e âmbito. Para adicionar uma tarefa de papel, siga estes passos.

### <a name="step-1-determine-who-needs-access"></a>Passo 1: Determinar quem precisa de acesso

Pode atribuir uma função a um utilizador, grupo, principal de serviço ou identidade gerida. Para adicionar uma atribuição de funções, pode ser necessário especificar o ID único do objeto. O ID tem o formato: `11111111-1111-1111-1111-111111111111` . Pode obter o ID usando o portal Azure ou Azure CLI.

**Utilizador**

Para um utilizador AZure AD, obtenha o nome principal do utilizador, como *patlong \@ contoso.com* ou o ID do objeto de utilizador. Para obter o ID do objeto, você pode usar [a az show de usuário de anúncios](/cli/azure/ad/user#az_ad_user_show).

```azurecli
az ad user show --id "{principalName}" --query "objectId" --output tsv
```

**Grupo**

Para um grupo AD Azure, você precisa da identificação do objeto de grupo. Para obter o ID do objeto, você pode usar [a az ad group show](/cli/azure/ad/group#az_ad_group_show) ou a lista de [grupos de anúncios az](/cli/azure/ad/group#az_ad_group_list).

```azurecli
az ad group show --group "{groupName}" --query "objectId" --output tsv
```

**Service principal (Principal de serviço)**

Para um principal de serviço Azure AD (identidade utilizada por uma aplicação), precisa do iD principal do objeto de serviço. Para obter o ID do objeto, você pode usar [a lista az ad sp](/cli/azure/ad/sp#az_ad_sp_list). Para um principiante de serviço, utilize o ID do objeto e **não** o ID da aplicação.

```azurecli
az ad sp list --all --query "[].{displayName:displayName, objectId:objectId}" --output tsv
az ad sp list --display-name "{displayName}"
```

**Identidade gerida**

Para uma identidade gerida atribuída pelo sistema ou para uma identidade gerida atribuída pelo utilizador, precisa do ID do objeto. Para obter o ID do objeto, você pode usar [a lista az ad sp](/cli/azure/ad/sp#az_ad_sp_list).

```azurecli
az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
```

Para listar apenas as identidades geridas atribuídas pelo utilizador, pode utilizar [a lista de identidades az](/cli/azure/identity#az_identity_list).

```azurecli
az identity list
```
    
### <a name="step-2-find-the-appropriate-role"></a>Passo 2: Encontrar o papel adequado

As permissões são agrupadas em papéis. Pode selecionar a partir de uma lista de várias [funções incorporadas do Azure](built-in-roles.md) ou pode usar as suas próprias funções personalizadas. É uma boa prática conceder acesso com o menor privilégio que for necessário, por isso evite atribuir um papel mais amplo.

Para listar papéis e obter o ID de papel único, você pode usar [a lista de definição de função az](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition list --query "[].{name:name, roleType:roleType, roleName:roleName}" --output tsv
```

Aqui está como listar os detalhes de um papel particular.

```azurecli
az role definition list --name "{roleName}"
```

Para obter mais informações, consulte as definições de [funções list Azure](role-definitions-list.md#azure-cli).
 
### <a name="step-3-identify-the-needed-scope"></a>Passo 3: Identificar o âmbito necessário

O Azure fornece quatro níveis de âmbito: recurso, grupo de [recursos,](../azure-resource-manager/management/overview.md#resource-groups)subscrição e [grupo de gestão.](../governance/management-groups/overview.md) É uma boa prática conceder acesso com o menor privilégio que for necessário, por isso evite atribuir um papel num âmbito mais amplo. Para obter mais informações sobre o âmbito, consulte [o âmbito de aplicação.](scope-overview.md)

**Âmbito do recurso**

Para o âmbito de recursos, precisa do ID de recurso para o recurso. Pode encontrar o ID do recurso olhando para as propriedades do recurso no portal Azure. Um ID de recurso tem o seguinte formato.

```
/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}
```

**Âmbito de grupo de recursos**

Para o âmbito do grupo de recursos, precisa do nome do grupo de recursos. Pode encontrar o nome na página **dos grupos de Recursos** no portal Azure ou pode utilizar a lista de [grupos az](/cli/azure/group#az_group_list).

```azurecli
az group list --query "[].{name:name}" --output tsv
```

**Âmbito de subscrição** 

Para o âmbito de subscrição, precisa do ID de subscrição. Pode encontrar o ID na página **de Subscrições** no portal Azure ou pode utilizar [a lista de conta az](/cli/azure/account#az_account_list).

```azurecli
az account list --query "[].{name:name, id:id}" --output tsv
```

**Âmbito do grupo de gestão** 

Para o âmbito do grupo de gestão, você precisa do nome do grupo de gestão. Pode encontrar o nome na página de **grupos de Gestão** no portal Azure ou pode utilizar [a lista de grupos de gestão de conta az](/cli/azure/account/management-group#az_account_management_group_list).

```azurecli
az account management-group list --query "[].{name:name, id:id}" --output tsv
```
    
### <a name="step-4-add-role-assignment"></a>Passo 4: Adicionar atribuição de função

Para adicionar uma atribuição de funções, use a [atribuição de função az criar](/cli/azure/role/assignment#az_role_assignment_create) comando. Dependendo do âmbito, o comando tem normalmente um dos seguintes formatos.

**Âmbito do recurso**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}"
```

**Âmbito de grupo de recursos**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}"
```

**Âmbito de subscrição** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--subscription "{subscriptionNameOrId}"
```

**Âmbito do grupo de gestão** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/providers/Microsoft.Management/managementGroups/{managementGroupName}"
``` 

O seguinte mostra um exemplo da saída quando atribui a função [de Contribuinte de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) a um utilizador num âmbito de grupo de recursos.

```azurecli
{
  "canDelegate": null,
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
  "name": "{roleAssignmentId}",
  "principalId": "{principalId}",
  "principalType": "User",
  "resourceGroup": "{resourceGroupName}",
  "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "type": "Microsoft.Authorization/roleAssignments"
}
```
    
## <a name="add-role-assignment-examples"></a>Adicionar exemplos de atribuição de funções

### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>Adicionar atribuição de funções para um âmbito específico de recurso de recipiente de bolhas

Atribui a função [de contribuinte de dados blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) a um titular de serviço com o ID do objeto *5555555-5555-5555-555555555555555555555555555,* numa mira de recurso para um recipiente de bolhas denominado *blob-container-01*.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"
```

### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Adicionar atribuição de funções para todos os recipientes blob num âmbito de recursos de conta de armazenamento

Atribui a função [de Contribuinte de Dados Blob de Armazenamento](built-in-roles.md#storage-blob-data-contributor) a um titular de serviço com iD *55555555-5555-55555-555555555555555555555555555* numa área de recursos para uma conta de armazenamento denominada *armazenamento12345*.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"
```

### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Adicionar atribuição de função para um grupo num âmbito específico de recursos de rede virtual

Atribui o papel [de Contribuinte de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) ao grupo Ann Mack *Team* com o ID 2222222-2222-2222-2222-222222222222222 numa área de recursos para uma rede virtual chamada *pharma-sales-project-network*.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Virtual Machine Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network"
```

### <a name="add-role-assignment-for-a-user-at-a-resource-group-scope"></a>Adicionar atribuição de funções para um utilizador num âmbito de grupo de recursos

Atribui o papel [de Contribuinte de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) ao utilizador *\@ patlong contoso.com* no âmbito do grupo de recursos de vendas de *pharma.*

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Adicionar atribuição de funções para um utilizador que usa o ID de função único num âmbito de grupo de recursos

Há um par de vezes em que um nome pode mudar, por exemplo:

- Está a usar o seu próprio papel personalizado e decide mudar o nome.
- Está a utilizar um papel de pré-visualização que tem **(Pré-visualização)** no nome. Quando o papel é lançado, o papel é renomeado.

Mesmo que um papel seja renomeado, o iD do papel não muda. Se estiver a usar scripts ou automação para criar as suas atribuições de papéis, é uma boa prática usar o ID de função único em vez do nome de função. Portanto, se um papel for renomeado, os seus scripts são mais propensos a funcionar.

O exemplo a seguir atribui o papel [de Contribuinte de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) ao utilizador *\@ patlong contoso.com* no âmbito do grupo de recursos de vendas de *pharma.*

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "9980e02c-c2be-4d73-94e8-173b1dc7cf3c" \
--resource-group "pharma-sales"
```

### <a name="add-role-assignment-for-all-blob-containers-at-a-resource-group-scope"></a>Adicionar atribuição de funções para todos os recipientes blob num âmbito de grupo de recursos

Atribui a função [de Contribuinte de Dados Blob de Armazenamento](built-in-roles.md#storage-blob-data-contributor) a um titular de serviço com o objeto ID *5555555-5555-55555-5555555555555555555555555555555* no âmbito do grupo de recursos *Exemplo-Armazenamento-rg.*

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--resource-group "Example-Storage-rg"
```

Alternadamente, pode especificar o grupo de recursos totalmente qualificado com o `--scope` parâmetro:

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg"
```

### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>Adicionar atribuição de função para uma aplicação no âmbito de um grupo de recursos

Atribui a função [de Contribuinte de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) a uma aplicação com o iD do objeto principal de serviço 44444444-4444-4444-4444444444444444444444444444444 no âmbito do grupo de recursos de vendas de *pharma.*

```azurecli
az role assignment create --assignee "44444444-4444-4444-4444-444444444444" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

### <a name="add-role-assignment-for-a-new-service-principal-at-a-resource-group-scope"></a>Adicionar atribuição de funções para um novo diretor de serviço num âmbito de grupo de recursos

Se criar um novo diretor de serviço e tentar imediatamente atribuir um papel a esse diretor de serviço, essa atribuição de funções pode falhar em alguns casos. Por exemplo, se usar um script para criar uma nova identidade gerida e tentar atribuir um papel a esse principal de serviço, a atribuição de funções pode falhar. A razão para esta falha é provavelmente um atraso de replicação. O principal serviço é criado numa região; no entanto, a atribuição de funções pode ocorrer em uma região diferente que ainda não replicou o principal serviço. Para abordar este cenário, deve especificar o tipo principal ao criar a atribuição de funções.

Para adicionar uma atribuição de funções, utilize [a az role assignment assignment create](/cli/azure/role/assignment#az_role_assignment_create), especifique um valor para , `--assignee-object-id` e, em seguida, definido `--assignee-principal-type` para `ServicePrincipal` .

```azurecli
az role assignment create --assignee-object-id "{assigneeObjectId}" \
--assignee-principal-type "{assigneePrincipalType}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}" \
--scope "/subscriptions/{subscriptionId}"
```

O exemplo a seguir atribui o papel [de Contribuinte de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) à identidade gerida pelo *msi-teste* no âmbito do grupo de recursos *de vendas de pharma:*

```azurecli
az role assignment create --assignee-object-id "33333333-3333-3333-3333-333333333333" \
--assignee-principal-type "ServicePrincipal" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>Adicionar atribuição de funções para um utilizador num âmbito de subscrição

Atribui o papel [de Leitor](built-in-roles.md#reader) ao *utilizador annm \@ example.com* num âmbito de subscrição.

```azurecli
az role assignment create --assignee "annm@example.com" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

### <a name="add-role-assignment-for-a-group-at-a-subscription-scope"></a>Adicionar atribuição de função para um grupo num âmbito de subscrição

Atribui o papel de [Reader](built-in-roles.md#reader) ao grupo *Ann Mack Team* com iD 22222222-2222-2222-2222-2222-2222222222222 num âmbito de subscrição.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

### <a name="add-role-assignment-for-all-blob-containers-at-a-subscription-scope"></a>Adicionar atribuição de funções para todos os recipientes blob num âmbito de subscrição

Atribui a função [de Leitor de Dados blob de armazenamento](built-in-roles.md#storage-blob-data-reader) ao utilizador *\@ alain example.com* num âmbito de subscrição.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Storage Blob Data Reader" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>Adicionar atribuição de funções para um utilizador num âmbito de grupo de gestão

Atribui o papel [de Leitor de Faturação](built-in-roles.md#billing-reader) ao *utilizador alain \@ example.com* num âmbito de grupo de gestão.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="remove-role-assignment"></a>Remover atribuição de funções

No Azure RBAC, para remover o acesso, remove-se uma atribuição de funções utilizando a designação de [função az](/cli/azure/role/assignment#az_role_assignment_delete).

O exemplo a seguir remove a atribuição de função [de contribuinte de máquina virtual](built-in-roles.md#virtual-machine-contributor) do utilizador *\@ patlong contoso.com* no grupo de recursos de vendas de *pharma:*

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

Remove o papel de [Leitor](built-in-roles.md#reader) do grupo *Ann Mack Team* com iD 22222222-2222-2222-2222-2222-222222222222 num âmbito de subscrição.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

Remove o papel de [Leitor de Faturação](built-in-roles.md#billing-reader) do *alain \@ example.com* utilizador no âmbito do grupo de gestão.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="next-steps"></a>Próximos passos

- [ListE Azure atribui funções usando Azure CLI](role-assignments-list-cli.md)
- [Utilize o CLI Azure para gerir recursos e grupos de recursos da Azure](../azure-resource-manager/management/manage-resources-cli.md)