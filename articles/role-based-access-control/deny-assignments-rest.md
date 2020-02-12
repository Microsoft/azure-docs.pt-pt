---
title: Lista nega atribuições para recursos Azure com a API REST
description: Saiba como listar as atribuições de utilizadores, grupos e aplicações utilizando o controlo de acesso baseado em papéis (RBAC) para recursos Azure e a API REST.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9e6214b3cb2cdca2d80ebae43771b206e3396d8b
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137329"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Lista nega atribuições para recursos Azure utilizando a API REST

[As atribuições de negação](deny-assignments.md) bloqueiam os utilizadores de realizarem ações específicas de recursos Do Azure, mesmo que uma atribuição de funções lhes conceda acesso. Este artigo descreve como listar missões de negação usando a API REST.

> [!NOTE]
> Não podes criar diretamente as tuas próprias missões de negação. Para obter informações sobre como as missões de negação são criadas, consulte [missões Denegação](deny-assignments.md).

## <a name="prerequisites"></a>Pré-requisitos

Para obter informações sobre uma atribuição de negação, deve ter:

- `Microsoft.Authorization/denyAssignments/read` permissão, que está incluída na maioria das [funções incorporadas para os recursos Azure.](built-in-roles.md)

## <a name="list-a-single-deny-assignment"></a>Enumerar uma única atribuição de negação

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Dentro do URI, substitua *{scope}* com o âmbito para o qual pretende enumerar as atribuições de negação.

    | Âmbito | Tipo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{deny-assignment-id}* com o identificador de atribuição de negação que pretende recuperar.

## <a name="list-multiple-deny-assignments"></a>Lista múltiplo de missões de negação

1. Comece com um dos seguintes pedidos:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Com parâmetros opcionais:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Dentro do URI, substitua *{scope}* com o âmbito para o qual pretende enumerar as atribuições de negação.

    | Âmbito | Tipo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{filter}* com a condição que pretende aplicar para filtrar a lista de atribuição de negação.

    | Filtro | Descrição |
    | --- | --- |
    | (sem filtro) | Enumerar todas as atribuições de negação em, acima e abaixo do âmbito especificado. |
    | `$filter=atScope()` | A lista nega atribuições apenas para o âmbito especificado e acima. Não inclui as missões de negação nos subscópios. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Lista nega atribuições com o nome especificado. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Lista nega atribuições no âmbito raiz (/)

1. Eleve o seu acesso como descrito no [acesso elevado a um Administrador Global em Diretório Ativo Azure](elevate-access-global-admin.md).

1. Utilize o seguinte pedido:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Substitua *{filter}* com a condição que pretende aplicar para filtrar a lista de atribuição de negação. É necessário um filtro.

    | Filtro | Descrição |
    | --- | --- |
    | `$filter=atScope()` | Lista nega atribuições apenas para o âmbito de raiz. Não inclui as missões de negação nos subscópios. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Lista nega atribuições com o nome especificado. |

1. Remova o acesso elevado.

## <a name="next-steps"></a>Passos seguintes

- [Entenda negar atribuições para recursos Azure](deny-assignments.md)
- [Elevate access for a Global Administrator in Azure Active Directory](elevate-access-global-admin.md) (Elevar o acesso para um Administrador Global no Azure Active Directory)
- [Referência à API REST do Azure](/rest/api/azure/)
