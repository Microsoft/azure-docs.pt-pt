---
title: Listar atribuições de função usando o RBAC do Azure e a API REST
description: Saiba como determinar quais recursos os usuários, grupos, entidades de serviço ou identidades gerenciadas têm acesso ao uso do RBAC (controle de acesso baseado em função) do Azure e a API REST.
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
ms.openlocfilehash: e20edcb5e2406c216711a2e0f696ef06e19fe21e
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710402"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Listar atribuições de função usando o RBAC do Azure e a API REST

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] este artigo descreve como listar atribuições de função usando a API REST.

## <a name="list-role-assignments"></a>Listar atribuições de função

No RBAC, para listar o acesso, você lista as atribuições de função. Para listar as atribuições de função, use uma das [atribuições de função-listar](/rest/api/authorization/roleassignments/list) APIs REST. Para refinar os resultados, especifique um escopo e um filtro opcional.

1. Comece com a seguinte solicitação:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro do URI, substitua *{Scope}* pelo escopo para o qual você deseja listar as atribuições de função.

    | Âmbito | Tipo |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    | `subscriptions/{subscriptionId1}` | Subscrição |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

    No exemplo anterior, Microsoft. Web é um provedor de recursos que se refere a uma instância do serviço de aplicativo. Da mesma forma, você pode usar qualquer outro provedor de recursos e especificar o escopo. Para obter mais informações, consulte [provedores de recursos do Azure e tipos](../azure-resource-manager/resource-manager-supported-services.md) e [operações de provedor de recursos Azure Resource Manager](resource-provider-operations.md)com suporte.  
     
1. Substitua *{Filter}* pela condição que você deseja aplicar para filtrar a lista de atribuição de função.

    | Filtrar | Descrição |
    | --- | --- |
    | `$filter=atScope()` | Lista as atribuições de função somente para o escopo especificado, não incluindo as atribuições de função em Subescopos. |
    | `$filter=principalId%20eq%20'{objectId}'` | Lista as atribuições de função para um usuário, grupo ou entidade de serviço especificado. |
    | `$filter=assignedTo('{objectId}')` | Lista as atribuições de função para um usuário ou entidade de serviço especificada. Se o usuário for membro de um grupo que tem uma atribuição de função, essa atribuição de função também será listada. Esse filtro é transitivo para grupos, o que significa que, se o usuário for membro de um grupo e esse grupo for membro de outro grupo que tenha uma atribuição de função, essa atribuição de função também será listada. Esse filtro só aceita uma ID de objeto para um usuário ou uma entidade de serviço. Não é possível passar uma ID de objeto para um grupo. |

## <a name="next-steps"></a>Passos seguintes

- [Adicionar ou remover atribuições de função usando o RBAC do Azure e a API REST](role-assignments-rest.md)
- [Referência à API REST do Azure](/rest/api/azure/)
