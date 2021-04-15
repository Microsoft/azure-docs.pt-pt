---
title: Atribuir funções Azure utilizando a API REST - Azure RBAC
description: Saiba como conceder acesso aos recursos Azure para utilizadores, grupos, principais serviços ou identidades geridas utilizando o controlo de acesso baseado em funções REST API e Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 3baf44a4240b23b41ce2e80dc22dbda4c7d0672a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363721"
---
# <a name="assign-azure-roles-using-the-rest-api"></a>Atribuir funções Azure utilizando a API REST

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Este artigo descreve como atribuir funções usando a API REST.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="assign-an-azure-role"></a>Atribuir um papel Azure

Para atribuir uma função, utilize as [Atribuições de Função - Crie](/rest/api/authorization/roleassignments/create) a API REST e especifique o principal de segurança, definição de função e âmbito. Para chamar a isto API, você deve ter acesso à `Microsoft.Authorization/roleAssignments/write` operação. Das funções incorporadas, apenas [o Proprietário](built-in-roles.md#owner) e o Administrador de Acesso ao [Utilizador](built-in-roles.md#user-access-administrator) têm acesso a esta operação.

1. Utilize as [Definições de Função - Liste](/rest/api/authorization/roledefinitions/list) API de REST ou consulte [funções incorporadas](built-in-roles.md) para obter o identificador para a definição de função que pretende atribuir.

1. Utilize uma ferramenta GUID para gerar um identificador único que será utilizado para o identificador de atribuição de funções. O identificador tem o formato: `00000000-0000-0000-0000-000000000000`

1. Comece com o seguinte pedido e corpo:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. Dentro do URI, *substitua {scope}* com o âmbito para a atribuição de funções.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Recurso |

    No exemplo anterior, o microsoft.web é um fornecedor de recursos que se refere a uma instância do Serviço de Aplicações. Da mesma forma, pode utilizar quaisquer outros fornecedores de recursos e especificar o âmbito. Para obter mais informações, consulte [os fornecedores e tipos de recursos Azure](../azure-resource-manager/management/resource-providers-and-types.md) e as operações de [fornecedores de recursos Azure suportados.](resource-provider-operations.md)  

1. Substitua *{roleAssignmentId}* pelo identificador GUID da atribuição de funções.

1. Dentro do órgão de pedido, *substitua {scope}* com o âmbito para a atribuição de funções.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* pelo identificador de definição de função.

1. Substitua *{principalId}* pelo identificador de objetos do utilizador, grupo ou principal de serviço que será atribuído a função.

O seguinte pedido e corpo atribui a função [Backup Reader](built-in-roles.md#backup-reader) a um utilizador no âmbito de subscrição:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

```json
{
  "properties": {
    "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
    "principalId": "{objectId1}"
  }
}
```

O seguinte mostra um exemplo da saída:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:23.7679147Z",
        "updatedOn": "2020-05-06T23:55:23.7679147Z",
        "createdBy": null,
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

### <a name="new-service-principal"></a>Novo diretor de serviços

Se criar um novo diretor de serviço e tentar imediatamente atribuir um papel a esse diretor de serviço, essa atribuição de funções pode falhar em alguns casos. Por exemplo, se criar uma nova identidade gerida e tentar atribuir um papel a esse diretor de serviço, a atribuição de funções poderá falhar. A razão para esta falha é provavelmente um atraso de replicação. O principal serviço é criado numa região; no entanto, a atribuição de funções pode ocorrer em uma região diferente que ainda não replicou o principal serviço.

Para abordar este cenário, utilize as [Atribuições de Função - Crie](/rest/api/authorization/roleassignments/create) a API REST e desembrulhe a `principalType` propriedade para `ServicePrincipal` . Também deve definir o `apiVersion` de `2018-09-01-preview` ou mais tarde.

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2018-09-01-preview
```

```json
{
  "properties": {
    "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
    "principalId": "{principalId}",
    "principalType": "ServicePrincipal"
  }
}
```

## <a name="next-steps"></a>Passos seguintes

- [ListE Azure atribui funções utilizando a API REST](role-assignments-list-rest.md)
- [Implementar recursos com modelos do Resource Manager e API REST do Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
- [Referência à API REST do Azure](/rest/api/azure/)
- [Criar ou atualizar funções personalizadas Azure usando a API REST](custom-roles-rest.md)
