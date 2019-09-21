---
title: Gerenciar o acesso aos recursos do Azure usando RBAC e CLI do Azure | Microsoft Docs
description: Saiba como gerenciar o acesso aos recursos do Azure para usuários, grupos e aplicativos usando o RBAC (controle de acesso baseado em função) e CLI do Azure. Isto inclui como listar, conceder e remover acesso.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3420374e90790bd1ffe4c845c19de1bfed317302
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173743"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>Gerenciar o acesso aos recursos do Azure usando RBAC e CLI do Azure

O [RBAC (controle de acesso baseado em função)](overview.md) é a maneira como você gerencia o acesso aos recursos do Azure. Este artigo descreve como gerenciar o acesso para usuários, grupos e aplicativos usando o RBAC e o CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para gerenciar o acesso, você precisa de um dos seguintes:

* [Bash em Azure Cloud Shell](/azure/cloud-shell/overview)
* [CLI do Azure](/cli/azure)

## <a name="list-roles"></a>Listar funções

Para listar todas as definições de função disponíveis, use a [lista de definições de função AZ](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

O exemplo a seguir lista o nome e a descrição de todas as definições de função disponíveis:

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

O exemplo a seguir lista todas as definições de função internas:

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

Para listar uma definição de função, use a [lista de definição de função AZ](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

O exemplo a seguir lista a definição da função *colaborador* :

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

### <a name="list-actions-of-a-role"></a>Listar ações de uma função

O exemplo a seguir lista apenas as ações *e as não ações da* função *colaborador* :

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

O exemplo a seguir lista apenas as ações da função *colaborador da máquina virtual* :

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

No RBAC, para listar o acesso, você lista as atribuições de função.

### <a name="list-role-assignments-for-a-user"></a>Listar atribuições de funções para um utilizador

Para listar as atribuições de função para um usuário específico, use a [lista de atribuição de função AZ](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Por padrão, somente atribuições de escopo direcionadas à assinatura serão exibidas. Para exibir atribuições com escopo definido por recurso ou grupo `--all` , use e para exibir atribuições `--include-inherited`herdadas, use.

O exemplo a seguir lista as atribuições de função atribuídas diretamente ao usuário *patlong\@contoso.com* :

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

### <a name="list-role-assignments-at-a-resource-group-scope"></a>Listar atribuições de função em um escopo de grupo de recursos

Para listar as atribuições de função que existem em um escopo de grupo de recursos, use a [lista de atribuição de função AZ](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

O exemplo a seguir lista as atribuições de função para o grupo de recursos *Pharma-Sales* :

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
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

...
```

### <a name="list-role-assignments-at-a-subscription-scope"></a>Listar atribuições de função em um escopo de assinatura

Para listar todas as atribuições de função em um escopo de assinatura, use a [lista de atribuição de função AZ](/cli/azure/role/assignment#az-role-assignment-list). Para obter a ID da assinatura, você pode encontrá-la na folha **assinaturas** no portal do Azure ou pode usar a lista de [contas AZ](/cli/azure/account#az-account-list).

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>Listar atribuições de função em um escopo do grupo de gerenciamento

Para listar todas as atribuições de função em um escopo do grupo de gerenciamento, use a [lista de atribuição de função AZ](/cli/azure/role/assignment#az-role-assignment-list). Para obter a ID do grupo de gerenciamento, você pode encontrá-la na folha **grupos de gerenciamento** no portal do Azure ou pode usar [AZ Account Management-grupo List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="grant-access"></a>Conceder acesso

No RBAC, para conceder acesso, crie uma atribuição de função.

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Criar uma atribuição de função para um usuário em um escopo de grupo de recursos

Para conceder acesso a um usuário em um escopo de grupo de recursos, use [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

O exemplo a seguir atribui a função *colaborador da máquina virtual* ao *usuário\@patlong contoso.com* no escopo do grupo de recursos *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Criar uma atribuição de função usando a ID de função exclusiva

Há algumas ocasiões em que um nome de função pode ser alterado, por exemplo:

- Você está usando sua própria função personalizada e decide alterar o nome.
- Você está usando uma função de visualização que tem **(visualização)** no nome. Quando a função é liberada, a função é renomeada.

> [!IMPORTANT]
> Uma versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mesmo que uma função seja renomeada, a ID da função não será alterada. Se você estiver usando scripts ou automação para criar atribuições de função, é uma prática recomendada usar a ID de função exclusiva em vez do nome da função. Portanto, se uma função for renomeada, os scripts provavelmente funcionarão.

Para criar uma atribuição de função usando a ID de função exclusiva em vez do nome da função, use [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

O exemplo a seguir atribui a função [colaborador da máquina virtual](built-in-roles.md#virtual-machine-contributor) ao usuário *patlong\@contoso.com* no escopo do grupo de recursos *Pharma-Sales* . Para obter a ID de função exclusiva, você pode usar a [lista de definição de função AZ](/cli/azure/role/definition#az-role-definition-list) ou ver [funções internas para recursos do Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>Criar uma atribuição de função para um grupo

Para conceder acesso a um grupo, use [criar atribuição de função AZ](/cli/azure/role/assignment#az-role-assignment-create). Para obter a ID do grupo, você pode usar [AZ ad Group List](/cli/azure/ad/group#az-ad-group-list) ou [AZ ad Group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

O exemplo a seguir atribui a função *leitor* ao grupo de *equipe Ana Mack* com ID 22222222-2222-2222-2222-222222222222 em um escopo de assinatura.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

O exemplo a seguir atribui a função *colaborador de máquina virtual* ao grupo de *equipe Ana Mack* com ID 22222222-2222-2222-2222-222222222222 em um escopo de recurso para uma rede virtual denominada *Pharma-Sales-Project-Network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application-at-a-resource-group-scope"></a>Criar uma atribuição de função para um aplicativo em um escopo de grupo de recursos

Para conceder acesso a um aplicativo, use [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Para obter a ID de objeto do aplicativo, você pode usar a [lista AZ ad app](/cli/azure/ad/app#az-ad-app-list) ou [AZ ad App Show](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

O exemplo a seguir atribui a função *colaborador da máquina virtual* a um aplicativo com a ID de objeto 44444444-4444-4444-4444-444444444444 no escopo do grupo de recursos *Pharma-Sales* .

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-user-at-a-subscription-scope"></a>Criar uma atribuição de função para um usuário em um escopo de assinatura

Para conceder acesso a um usuário em um escopo de assinatura, use [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Para obter a ID da assinatura, você pode encontrá-la na folha **assinaturas** no portal do Azure ou pode usar a lista de [contas AZ](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

O exemplo a seguir atribui a função *leitor* ao usuário *annm\@example.com* em um escopo de assinatura.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>Criar uma atribuição de função para um usuário em um escopo de grupo de gerenciamento

Para conceder acesso a um usuário em um escopo do grupo de gerenciamento, use [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Para obter a ID do grupo de gerenciamento, você pode encontrá-la na folha **grupos de gerenciamento** no portal do Azure ou pode usar [AZ Account Management-grupo List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

O exemplo a seguir atribui a função de *leitor de cobrança* ao usuário *Alain\@example.com* em um escopo de grupo de gerenciamento.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="create-a-role-assignment-for-a-new-service-principal"></a>Criar uma atribuição de função para uma nova entidade de serviço

Se você criar uma nova entidade de serviço e tentar atribuir imediatamente uma função a essa entidade de serviço, essa atribuição de função poderá falhar em alguns casos. Por exemplo, se você usar um script para criar uma nova identidade gerenciada e, em seguida, tentar atribuir uma função a essa entidade de serviço, a atribuição de função poderá falhar. O motivo dessa falha é provavelmente um atraso de replicação. A entidade de serviço é criada em uma região; no entanto, a atribuição de função pode ocorrer em uma região diferente que ainda não tenha replicado a entidade de serviço. Para resolver esse cenário, você deve especificar o tipo de entidade de segurança ao criar a atribuição de função.

Para criar uma atribuição de função, use [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create), especifique um valor `--assignee-object-id`para e, em `--assignee-principal-type` seguida `ServicePrincipal`, defina como.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

O exemplo a seguir atribui a função *colaborador de máquina virtual* à identidade gerenciada de *teste de MSI* no escopo do grupo de recursos *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-access"></a>Remover acesso

No RBAC, para remover o acesso, você remove uma atribuição de função usando [AZ role Assignment Delete](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

O exemplo a seguir remove a atribuição de função de *colaborador de máquina virtual* do usuário *\@patlong contoso.com* no grupo de recursos *Pharma-Sales* :

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

O exemplo a seguir remove a função *leitor* do grupo de *equipe Ana Mack* com ID 22222222-2222-2222-2222-222222222222 em um escopo de assinatura. Para obter a ID do grupo, você pode usar [AZ ad Group List](/cli/azure/ad/group#az-ad-group-list) ou [AZ ad Group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

O exemplo a seguir remove a função de *leitor de cobrança* do usuário *Alain\@example.com* no escopo do grupo de gerenciamento. Para obter a ID do grupo de gerenciamento, você pode usar [AZ Account Management – Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Criar uma função personalizada para recursos do Azure usando CLI do Azure](tutorial-custom-role-cli.md)
- [Usar o CLI do Azure para gerenciar recursos e grupos de recursos do Azure](../azure-resource-manager/cli-azure-resource-manager.md)
