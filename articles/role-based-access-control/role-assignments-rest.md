---
title: Adicionar ou remover atribuições de funções com rBAC e a API REST
description: Saiba como conceder acesso aos recursos do Azure para usuários, grupos, entidades de serviço ou identidades gerenciadas usando o RBAC (controle de acesso baseado em função) do Azure e a API REST.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ae6fce5ab962637fe477ade75cf81b6ac237bdd2
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138319"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Adicionar ou remover atribuições de função usando o RBAC do Azure e a API REST

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Este artigo descreve como atribuir funções utilizando a API REST.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de função, você deve ter:

- `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete` permissões, tais como [Administrador de Acesso ao Utilizador](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de funções

No RBAC, para conceder acesso, adiciona-se uma atribuição de funções. Para adicionar uma atribuição de funções, utilize as Atribuições de [Funções - Crie](/rest/api/authorization/roleassignments/create) a API REST e especifique o diretor de segurança, definição de funções e âmbito. Para chamar a isto API, você deve ter acesso à operação `Microsoft.Authorization/roleAssignments/write`. Das funções incorporadas, apenas o [Proprietário](built-in-roles.md#owner) e [o Administrador](built-in-roles.md#user-access-administrator) de Acesso ao Utilizador têm acesso a esta operação.

1. Utilize as Definições de [Função - Lista](/rest/api/authorization/roledefinitions/list) REST API ou consulte [funções incorporadas](built-in-roles.md) para obter o identificador para a definição de função que pretende atribuir.

1. Use uma ferramenta GUID para gerar um identificador exclusivo que será usado para o identificador de atribuição de função. O identificador tem o formato: `00000000-0000-0000-0000-000000000000`

1. Comece com o seguinte pedido e corpo:

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

1. Dentro do URI, substitua *{scope}* com o âmbito para a atribuição de funções.

    | Âmbito | Tipo |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    | `subscriptions/{subscriptionId1}` | Subscrição |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Recurso |

1. Substitua *{roleAssignmentName}* com o identificador GUID da atribuição de funções.

1. Dentro do organismo de pedido, substitua *{scope}* com o âmbito para a atribuição de funções.

    | Âmbito | Tipo |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    | `subscriptions/{subscriptionId1}` | Subscrição |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* com o identificador de definição de funções.

1. Substitua *o {principalId}* pelo identificador de objetos do utilizador, grupo ou diretor de serviço que lhe será atribuído o papel.

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função

No RBAC, para remover o acesso, remova uma atribuição de função. Para remover uma atribuição de funções, utilize as Atribuições de [Funções - Eliminar](/rest/api/authorization/roleassignments/delete) a API REST. Para chamar a isto API, você deve ter acesso à operação `Microsoft.Authorization/roleAssignments/delete`. Das funções incorporadas, apenas o [Proprietário](built-in-roles.md#owner) e [o Administrador](built-in-roles.md#user-access-administrator) de Acesso ao Utilizador têm acesso a esta operação.

1. Obtenha o identificador de atribuição de função (GUID). Esse identificador é retornado quando você cria a atribuição de função pela primeira vez ou pode obtê-la listando as atribuições de função.

1. Comece com o seguinte pedido:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{scope}* com a margem para remover a atribuição de funções.

    | Âmbito | Tipo |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    | `subscriptions/{subscriptionId1}` | Subscrição |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Recurso |

1. Substitua *{roleAssignmentName}* com o identificador GUID da atribuição de funções.

## <a name="next-steps"></a>Passos seguintes

- [Lista de atribuições de funções utilizando o Azure RBAC e a Rest API](role-assignments-list-rest.md)
- [Implementar recursos com modelos do Resource Manager e a API REST do Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
- [Referência à API REST do Azure](/rest/api/azure/)
- [Criar papéis personalizados para os recursos Azure usando a API REST](custom-roles-rest.md)
