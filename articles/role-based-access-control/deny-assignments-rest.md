---
title: Lista Azure nega atribuições usando a API REST - Azure RBAC
description: Saiba como listar o Azure negar atribuições para utilizadores, grupos e aplicações utilizando o controlo de acesso baseado em funções REST API e Azure (Azure RBAC).
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
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2f835c270930734bf9963a7c7c3168b873eddaf6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84791916"
---
# <a name="list-azure-deny-assignments-using-the-rest-api"></a>Lista Azure nega atribuições usando a API REST

[O Azure nega que as atribuições](deny-assignments.md) impeçam os utilizadores de realizarem ações específicas de recursos Azure, mesmo que uma atribuição de papel lhes conceda acesso. Este artigo descreve como listar atribuições de negação usando a API REST.

> [!NOTE]
> Não podes criar diretamente as tuas próprias missões de negação. Para obter informações sobre como as missões de negação são criadas, consulte [Azure negar atribuições](deny-assignments.md).

## <a name="prerequisites"></a>Pré-requisitos

Para obter informações sobre uma missão de negação, você deve ter:

- `Microsoft.Authorization/denyAssignments/read`permissão, que está incluída na maioria [dos papéis incorporados do Azure.](built-in-roles.md)

## <a name="list-a-single-deny-assignment"></a>Listar uma única missão de negação

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Dentro do URI, substitua *{scope}* pelo âmbito para o qual pretende listar as atribuições de negação.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Subscrição |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{deny-assignment-id}* pelo identificador de atribuição de negação que pretende recuperar.

## <a name="list-multiple-deny-assignments"></a>Listar várias atribuições de negação

1. Comece com um dos seguintes pedidos:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Com parâmetros opcionais:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Dentro do URI, substitua *{scope}* pelo âmbito para o qual pretende listar as atribuições de negação.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Subscrição |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{filter}* pela condição que pretende aplicar para filtrar a lista de atribuição de negar.

    > [!div class="mx-tableFixed"]
    > | Filtro | Description |
    > | --- | --- |
    > | (sem filtro) | Lista todas as atribuições negam em, acima, e abaixo do âmbito especificado. |
    > | `$filter=atScope()` | As listas negam atribuições apenas para o âmbito especificado e acima. Não inclui as atribuições de negação em subscópios. |
    > | `$filter=assignedTo('{objectId}')` | As listas negam atribuições para o utilizador especificado ou o principal de serviço.<br/>Se o utilizador for membro de um grupo que tem uma atribuição de negação, essa atribuição de negação também está listada. Este filtro é transitório para grupos, o que significa que se o utilizador é membro de um grupo e esse grupo é membro de outro grupo que tem uma atribuição de negação, essa atribuição de negação também está listada.<br/>Este filtro só aceita um ID de objeto para um utilizador ou um principal de serviço. Não se pode passar uma identificação de objeto para um grupo. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | As listas negam atribuições para o utilizador ou principal de serviço especificado e no âmbito especificado. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | As listas negam atribuições com o nome especificado. |
    > | `$filter=principalId+eq+'{objectId}'` | As listas negam atribuições para o utilizador, grupo ou principal de serviço especificado. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Lista negar atribuições no âmbito raiz (/)

1. Eleve o seu acesso como descrito no [acesso Elevate para gerir todas as subscrições e grupos de gestão da Azure.](elevate-access-global-admin.md)

1. utilize o seguinte pedido:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Substitua *{filter}* pela condição que pretende aplicar para filtrar a lista de atribuição de negar. É necessário um filtro.

    > [!div class="mx-tableFixed"]
    > | Filtro | Description |
    > | --- | --- |
    > | `$filter=atScope()` | A lista nega atribuições apenas para o âmbito de raiz. Não inclui as atribuições de negação em subscópios. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Lista negar atribuições com o nome especificado. |

1. Remova o acesso elevado.

## <a name="next-steps"></a>Passos seguintes

- [Compreenda que Azure nega atribuições](deny-assignments.md)
- [Elevate access to manage all Azure subscriptions and management groups](elevate-access-global-admin.md) (Elevar o acesso para gerir todas as subscrições e grupos de gestão do Azure)
- [Referência à API REST do Azure](/rest/api/azure/)
