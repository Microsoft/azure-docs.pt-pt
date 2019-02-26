---
title: Lista negar atribuições para recursos do Azure com a API REST - Azure | Documentos da Microsoft
description: Saiba como listar atribuições de utilizadores, grupos e aplicações, com controlo de acesso baseado em funções (RBAC) para recursos do Azure e a API REST de negação.
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
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ba1c60d45fb53be158d9e302748366ddf417f23e
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805482"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Lista de atribuições para recursos do Azure com a API REST de negação

Atualmente, negar atribuições são **só de leitura** e só pode ser definida pela Microsoft. Apesar de não ser possível criar as suas próprias atribuições de negação, pode listar atribuições de negação, porque elas poderiam afetar as suas permissões em vigor. Este artigo descreve como negar que para listar atribuições com o RBAC e a API REST.

## <a name="list-a-single-deny-assignment"></a>Lista de um único negar atribuição

1. Começar com o pedido seguinte:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. No URI, substitua *{âmbito}* com o âmbito para o qual pretende listar as atribuições de negação.

    | Âmbito | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

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

    | Âmbito | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

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
