---
title: Adicionar ou remover atribuições de função usando o RBAC do Azure e a API REST
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
ms.openlocfilehash: 3c7b7dac649548b8b21cc13761009c11609c8904
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981040"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Adicionar ou remover atribuições de função usando o RBAC do Azure e a API REST

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] este artigo descreve como atribuir funções usando a API REST.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de função, você deve ter:

- permissões de `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, como [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) ou [proprietário](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No RBAC, para conceder acesso, você adiciona uma atribuição de função. Para adicionar uma atribuição de função, use as [atribuições de função-criar](/rest/api/authorization/roleassignments/create) API REST e especifique a entidade de segurança, a definição de função e o escopo. Para chamar essa API, você deve ter acesso à operação de `Microsoft.Authorization/roleAssignments/write`. Das funções internas, somente o [proprietário](built-in-roles.md#owner) e o [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) recebem acesso a essa operação.

1. Use as [definições de função-lista](/rest/api/authorization/roledefinitions/list) de API REST ou consulte [funções internas](built-in-roles.md) para obter o identificador para a definição de função que você deseja atribuir.

1. Use uma ferramenta GUID para gerar um identificador exclusivo que será usado para o identificador de atribuição de função. O identificador tem o formato: `00000000-0000-0000-0000-000000000000`

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

    | Âmbito | Tipo |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    | `subscriptions/{subscriptionId1}` | Subscrição |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Recurso |

1. Substitua *{roleAssignmentName}* pelo identificador GUID da atribuição de função.

1. No corpo da solicitação, substitua *{Scope}* pelo escopo da atribuição de função.

    | Âmbito | Tipo |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    | `subscriptions/{subscriptionId1}` | Subscrição |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* pelo identificador de definição de função.

1. Substitua *{PrincipalId}* pelo identificador de objeto do usuário, grupo ou entidade de serviço à qual a função será atribuída.

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função

No RBAC, para remover o acesso, remova uma atribuição de função. Para remover uma atribuição de função, use as [atribuições de função-excluir](/rest/api/authorization/roleassignments/delete) API REST. Para chamar essa API, você deve ter acesso à operação de `Microsoft.Authorization/roleAssignments/delete`. Das funções internas, somente o [proprietário](built-in-roles.md#owner) e o [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) recebem acesso a essa operação.

1. Obtenha o identificador de atribuição de função (GUID). Esse identificador é retornado quando você cria a atribuição de função pela primeira vez ou pode obtê-la listando as atribuições de função.

1. Comece com a seguinte solicitação:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{Scope}* pelo escopo para remover a atribuição de função.

    | Âmbito | Tipo |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    | `subscriptions/{subscriptionId1}` | Subscrição |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Recurso |

1. Substitua *{roleAssignmentName}* pelo identificador GUID da atribuição de função.

## <a name="next-steps"></a>Passos seguintes

- [Listar atribuições de função usando o RBAC do Azure e a API REST](role-assignments-list-rest.md)
- [Implementar recursos com modelos do Resource Manager e a API REST do Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
- [Referência à API REST do Azure](/rest/api/azure/)
- [Criar funções personalizadas para recursos do Azure usando a API REST](custom-roles-rest.md)
