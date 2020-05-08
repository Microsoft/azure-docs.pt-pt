---
title: List Azure nega atribuições usando a REST API - Azure RBAC
description: Saiba como listar o Azure a negar atribuições para utilizadores, grupos e aplicações utilizando o controlo de acesso baseado em funções REST API e Azure (Azure RBAC).
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: dae0352566e6cb4f8ed1384ca12213e2aaa07f9d
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733875"
---
# <a name="list-azure-deny-assignments-using-the-rest-api"></a>List Azure nega atribuições usando a API REST

[O Azure nega que as atribuições](deny-assignments.md) impeçam os utilizadores de realizarem ações específicas de recursos Do Azure, mesmo que uma atribuição de funções lhes conceda acesso. Este artigo descreve como listar missões de negação usando a API REST.

> [!NOTE]
> Não podes criar diretamente as tuas próprias missões de negação. Para obter informações sobre como as missões de negação são criadas, consulte [Azure negar atribuições.](deny-assignments.md)

## <a name="prerequisites"></a>Pré-requisitos

Para obter informações sobre uma atribuição de negação, deve ter:

- `Microsoft.Authorization/denyAssignments/read`permissão, que está incluída na maioria das [funções azure incorporadas.](built-in-roles.md)

## <a name="list-a-single-deny-assignment"></a>Enumerar uma única atribuição de negação

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Dentro do URI, substitua *{scope}* com o âmbito para o qual pretende enumerar as atribuições de negação.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Subscrição |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Recurso |

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

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Subscrição |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{filter}* com a condição que pretende aplicar para filtrar a lista de atribuição de negação.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrição |
    > | --- | --- |
    > | (sem filtro) | Lista todas as atribuições de negação em, acima e abaixo do âmbito especificado. |
    > | `$filter=atScope()` | As listas negam atribuições apenas para o âmbito especificado e acima. Não inclui as missões de negação nos subscópios. |
    > | `$filter=assignedTo('{objectId}')` | As listas negam atribuições para o utilizador ou diretor de serviço especificado.<br/>Se o utilizador for membro de um grupo que tenha uma atribuição de negação, essa atribuição de negação também está listada. Este filtro é transitório para grupos, o que significa que se o utilizador for membro de um grupo e esse grupo for membro de outro grupo que tem uma atribuição de negação, essa missão de negação também está listada.<br/>Este filtro só aceita um ID de objeto para um utilizador ou um diretor de serviço. Não pode passar uma identificação de objeto para um grupo. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | As listas negam as atribuições para o utilizador ou diretor de serviço especificado e no âmbito especificado. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | As listas negam atribuições com o nome especificado. |
    > | `$filter=principalId+eq+'{objectId}'` | As listas negam atribuições para o utilizador, grupo ou diretor de serviço especificado. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Lista nega atribuições no âmbito raiz (/)

1. Eleve o seu acesso como descrito no [acesso elevate para gerir todas as subscrições e grupos de gestão do Azure.](elevate-access-global-admin.md)

1. utilize o seguinte pedido:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Substitua *{filter}* com a condição que pretende aplicar para filtrar a lista de atribuição de negação. É necessário um filtro.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrição |
    > | --- | --- |
    > | `$filter=atScope()` | Lista nega atribuições apenas para o âmbito de raiz. Não inclui as missões de negação nos subscópios. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Lista nega atribuições com o nome especificado. |

1. Remova o acesso elevado.

## <a name="next-steps"></a>Passos seguintes

- [Entenda Azure negar atribuições](deny-assignments.md)
- [Elevate access to manage all Azure subscriptions and management groups](elevate-access-global-admin.md) (Elevar o acesso para gerir todas as subscrições e grupos de gestão do Azure)
- [Referência da API do Rest Azure](/rest/api/azure/)
