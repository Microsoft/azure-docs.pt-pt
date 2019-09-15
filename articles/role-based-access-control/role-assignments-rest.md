---
title: Gerenciar o acesso aos recursos do Azure usando o RBAC e a API REST – Azure | Microsoft Docs
description: Saiba como gerenciar o acesso aos recursos do Azure para usuários, grupos e aplicativos usando o RBAC (controle de acesso baseado em função) e a API REST. Isto inclui como listar, conceder e remover acesso.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 86ee030e8c97cf3033b9d2d76b8125c64ecf8065
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996477"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>Gerenciar o acesso aos recursos do Azure usando o RBAC e a API REST

O [RBAC (controle de acesso baseado em função)](overview.md) é a maneira como você gerencia o acesso aos recursos do Azure. Este artigo descreve como gerenciar o acesso para usuários, grupos e aplicativos usando o RBAC e a API REST.

## <a name="list-access"></a>Listar o acesso

No RBAC, para listar o acesso, você lista as atribuições de função. Para listar as atribuições de função, use uma das [atribuições de função-listar](/rest/api/authorization/roleassignments/list) APIs REST. Para refinar os resultados, especifique um escopo e um filtro opcional.

1. Comece com a seguinte solicitação:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro do URI, substitua *{Scope}* pelo escopo para o qual você deseja listar as atribuições de função.

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    | `subscriptions/{subscriptionId1}` | Subscrição |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

    No exemplo anterior, Microsoft. Web é um provedor de recursos que se refere a uma instância do serviço de aplicativo. Da mesma forma, você pode usar qualquer outro provedor de recursos e especificar o escopo. Para obter mais informações, consulte [provedores de recursos do Azure e tipos](../azure-resource-manager/resource-manager-supported-services.md) e [operações de provedor de recursos Azure Resource Manager](resource-provider-operations.md)com suporte.  
     
1. Substitua *{Filter}* pela condição que você deseja aplicar para filtrar a lista de atribuição de função.

    | Filtro | Descrição |
    | --- | --- |
    | `$filter=atScope()` | Lista as atribuições de função somente para o escopo especificado, não incluindo as atribuições de função em Subescopos. |
    | `$filter=principalId%20eq%20'{objectId}'` | Lista as atribuições de função para um usuário, grupo ou entidade de serviço especificado. |
    | `$filter=assignedTo('{objectId}')` | Lista as atribuições de função para um usuário ou entidade de serviço especificada. Se o usuário for membro de um grupo que tem uma atribuição de função, essa atribuição de função também será listada. Esse filtro é transitivo para grupos, o que significa que, se o usuário for membro de um grupo e esse grupo for membro de outro grupo que tenha uma atribuição de função, essa atribuição de função também será listada. Esse filtro só aceita uma ID de objeto para um usuário ou uma entidade de serviço. Não é possível passar uma ID de objeto para um grupo. |

## <a name="grant-access"></a>Conceder acesso

No RBAC, para conceder acesso, crie uma atribuição de função. Para criar uma atribuição de função, use as [atribuições de função-criar](/rest/api/authorization/roleassignments/create) API REST e especifique a entidade de segurança, a definição de função e o escopo. Para chamar essa API, você deve ter acesso à `Microsoft.Authorization/roleAssignments/write` operação. Das funções internas, somente o [proprietário](built-in-roles.md#owner) e o [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) recebem acesso a essa operação.

1. Use as [definições de função-lista](/rest/api/authorization/roledefinitions/list) de API REST ou consulte [funções internas](built-in-roles.md) para obter o identificador para a definição de função que você deseja atribuir.

1. Use uma ferramenta GUID para gerar um identificador exclusivo que será usado para o identificador de atribuição de função. O identificador tem o formato:`00000000-0000-0000-0000-000000000000`

1. Comece com a seguinte solicitação e corpo:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. Dentro do URI, substitua *{Scope}* pelo escopo da atribuição de função.

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    | `subscriptions/{subscriptionId1}` | Subscrição |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Substitua *{roleAssignmentName}* pelo identificador GUID da atribuição de função.

1. No corpo da solicitação, substitua *{Scope}* pelo escopo da atribuição de função.

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    | `subscriptions/{subscriptionId1}` | Subscrição |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Substitua *{roleDefinitionId}* pelo identificador de definição de função.

1. Substitua *{PrincipalId}* pelo identificador de objeto do usuário, grupo ou entidade de serviço à qual a função será atribuída.

## <a name="remove-access"></a>Remover acesso

No RBAC, para remover o acesso, remova uma atribuição de função. Para remover uma atribuição de função, use as [atribuições de função-excluir](/rest/api/authorization/roleassignments/delete) API REST. Para chamar essa API, você deve ter acesso à `Microsoft.Authorization/roleAssignments/delete` operação. Das funções internas, somente o [proprietário](built-in-roles.md#owner) e o [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) recebem acesso a essa operação.

1. Obtenha o identificador de atribuição de função (GUID). Esse identificador é retornado quando você cria a atribuição de função pela primeira vez ou pode obtê-la listando as atribuições de função.

1. Comece com a seguinte solicitação:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{Scope}* pelo escopo para remover a atribuição de função.

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    | `subscriptions/{subscriptionId1}` | Subscrição |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Substitua *{roleAssignmentName}* pelo identificador GUID da atribuição de função.

## <a name="next-steps"></a>Passos Seguintes

- [Implementar recursos com modelos do Resource Manager e a API REST do Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Referência à API REST do Azure](/rest/api/azure/)
- [Criar funções personalizadas para recursos do Azure usando a API REST](custom-roles-rest.md)
