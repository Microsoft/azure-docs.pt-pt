---
title: List Azure atribuições de funções usando a REST API - Azure RBAC
description: Saiba como determinar que recursos os utilizadores, grupos, diretores de serviços ou identidades geridas têm acesso à utilização do controlo de acesso baseado em funções rest API e Azure (Azure RBAC).
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
ms.date: 05/06/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 50ef431559a38d30f7e1e76646e8930c70fc4ef9
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891338"
---
# <a name="list-azure-role-assignments-using-the-rest-api"></a>Lista de atribuições de funções azure utilizando a API REST

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Este artigo descreve como listar atribuições de papéis usando a API REST.

> [!NOTE]
> Se a sua organização tiver funções de gestão subcontratadas a um prestador de serviços que utiliza a [gestão de recursos delegados do Azure,](../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de funções autorizadas por esse prestador de serviços não serão mostradas aqui.

## <a name="list-role-assignments"></a>Listar atribuições de função

No Azure RBAC, para listar o acesso, enumera as atribuições de funções. Para listar atribuições de papéis, utilize uma das Atribuições de [Funções - Lista](/rest/api/authorization/roleassignments/list) DE APIs REST. Para refinar os seus resultados, especifice um âmbito e um filtro opcional.

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

O seguinte pedido lista todas as atribuições de funções para o utilizador especificado no âmbito da subscrição:

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()+and+assignedTo('{objectId1}')
```

O seguinte mostra um exemplo da saída:

```json
{
    "value": [
        {
            "properties": {
                "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
                "principalId": "{objectId1}",
                "scope": "/subscriptions/{subscriptionId1}",
                "createdOn": "2019-01-15T21:08:45.4904312Z",
                "updatedOn": "2019-01-15T21:08:45.4904312Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "{roleAssignmentId1}"
        }
    ]
}
```

## <a name="next-steps"></a>Passos seguintes

- [Adicione ou remova atribuições de funções Azure utilizando a API REST](role-assignments-rest.md)
- [Referência da API do Rest Azure](/rest/api/azure/)
