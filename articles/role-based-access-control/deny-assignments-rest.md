---
title: Lista negar atribuições para recursos do Azure com a API REST - Azure | Documentos da Microsoft
description: Saiba como listar atribuições de utilizadores, grupos e aplicações com o controlo de acesso baseado em funções (RBAC) para recursos do Azure e a API REST de negação.
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
ms.openlocfilehash: 0bc49456f5965846a2de542b4a063bab2d1838bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118294"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Lista de atribuições para recursos do Azure com a API REST de negação

[Negar atribuições](deny-assignments.md) impedir que os utilizadores a efetuar ações de recursos do Azure específica, mesmo se uma atribuição de função lhes concede acesso. Este artigo descreve como listar atribuições com a API REST de negação.

> [!NOTE]
> Não é possível diretamente criar seu próprio negar atribuições. Para obter informações sobre como negar atribuições são criadas, consulte [negar atribuições](deny-assignments.md).

## <a name="prerequisites"></a>Pré-requisitos

Para obter informações sobre uma atribuição de negar, tem de ter:

- `Microsoft.Authorization/denyAssignments/read` permissão, o que está incluído na maioria [funções incorporadas para recursos do Azure](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Lista de um único negar atribuição

1. Começar com o pedido seguinte:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. No URI, substitua *{âmbito}* com o âmbito para o qual pretende listar as atribuições de negação.

    | Scope | Tipo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Substitua *{id de atribuição de negar}* com o identificador de atribuição de negar que pretende recuperar.

## <a name="list-multiple-deny-assignments"></a>Listar várias atribuições de negação

1. Começar com um dos pedidos seguintes:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Com os parâmetros opcionais:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. No URI, substitua *{âmbito}* com o âmbito para o qual pretende listar as atribuições de negação.

    | Scope | Tipo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Substitua *{filtro}* com a condição que pretende aplicar para filtrar a lista de atribuição de negação.

    | Filtro | Descrição |
    | --- | --- |
    | (sem filtro) | Lista todos os negar atribuições em acima e abaixo âmbito especificado. |
    | `$filter=atScope()` | Lista de negação atribuições para apenas o âmbito especificado e superior. Não inclui as atribuições de negação em subscopes. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Lista de negação atribuições com o nome especificado. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Lista negar atribuições no âmbito de raiz (/)

1. Elevar o acesso, conforme descrito em [elevar o acesso de Administrador Global no Azure Active Directory](elevate-access-global-admin.md).

1. Utilize o seguinte pedido:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Substitua *{filtro}* com a condição que pretende aplicar para filtrar a lista de atribuição de negação. Um filtro é necessário.

    | Filtro | Descrição |
    | --- | --- |
    | `$filter=atScope()` | Lista de negação atribuições para apenas o âmbito de raiz. Não inclui as atribuições de negação em subscopes. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Lista de negação atribuições com o nome especificado. |

1. Remova acesso elevado.

## <a name="next-steps"></a>Passos Seguintes

- [Compreender negar atribuições para recursos do Azure](deny-assignments.md)
- [Elevate access for a Global Administrator in Azure Active Directory](elevate-access-global-admin.md) (Elevar o acesso para um Administrador Global no Azure Active Directory)
- [Referência à API REST do Azure](/rest/api/azure/)
