---
title: Lista de atribuições de funções utilizando o Azure RBAC e a Rest API
description: Saiba como determinar que recursos utilizadores, grupos, diretores de serviços ou identidades geridas têm acesso a usar o controlo de acesso baseado em funções azure (RBAC) e a API REST.
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a494e7fd4c9fb79faa6a1d8cb2c3c871796ccdc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062162"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Lista de atribuições de funções utilizando o Azure RBAC e a Rest API

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Este artigo descreve como listar atribuições de papéis usando a API REST.

> [!NOTE]
> Se a sua organização tiver funções de gestão subcontratadas a um prestador de serviços que utiliza a [gestão de recursos delegados do Azure,](../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de funções autorizadas por esse prestador de serviços não serão mostradas aqui.

## <a name="list-role-assignments"></a>Listar atribuições de função

No RBAC, para listar o acesso, enumera as atribuições de funções. Para listar atribuições de papéis, utilize uma das Atribuições de [Funções - Lista](/rest/api/authorization/roleassignments/list) DE APIs REST. Para refinar os seus resultados, especifice um âmbito e um filtro opcional.

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro do URI, substitua *{scope}* com o âmbito para o qual pretende enumerar as atribuições de funções.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Recurso |

    No exemplo anterior, o microsoft.web é um fornecedor de recursos que se refere a uma instância do Serviço de Aplicações. Da mesma forma, pode utilizar quaisquer outros fornecedores de recursos e especificar o âmbito. Para mais informações, consulte [os fornecedores e tipos de recursos da Azure](../azure-resource-manager/management/resource-providers-and-types.md) e apoiou as operações de fornecedor de recursos do Gestor de [Recursos Azure.](resource-provider-operations.md)  
     
1. Substitua *{filter}* com a condição que pretende aplicar para filtrar a lista de atribuição de funções.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrição |
    > | --- | --- |
    > | `$filter=atScope()` | Lista as atribuições de funções apenas para o âmbito especificado, sem incluir as atribuições de funções nos subscópios. |
    > | `$filter=assignedTo('{objectId}')` | Lista as atribuições de funções para um utilizador ou diretor de serviço especificado.<br/>Se o utilizador for membro de um grupo que tenha uma atribuição de funções, essa atribuição de funções também está listada. Este filtro é transitório para grupos, o que significa que se o utilizador for membro de um grupo e esse grupo for membro de outro grupo que tem uma atribuição de funções, essa atribuição de funções também está listada.<br/>Este filtro só aceita um ID de objeto para um utilizador ou um diretor de serviço. Não pode passar uma identificação de objeto para um grupo. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Lista as atribuições de funções para o utilizador ou diretor de serviço especificado e no âmbito especificado. |
    > | `$filter=principalId+eq+'{objectId}'` | Lista as atribuições de funções para um utilizador, grupo ou diretor de serviço especificado. |

## <a name="next-steps"></a>Passos seguintes

- [Adicione ou remova tarefas de funções utilizando o Azure RBAC e a Rest API](role-assignments-rest.md)
- [Referência da API do Rest Azure](/rest/api/azure/)
