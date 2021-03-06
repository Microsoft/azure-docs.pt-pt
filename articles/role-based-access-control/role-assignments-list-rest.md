---
title: List Azure fun assignments using the REST API - Azure RBAC
description: Saiba como determinar quais os recursos que os utilizadores, grupos, principais de serviços ou identidades geridas têm acesso à utilização do controlo de acesso baseado em funções REST API e Azure (Azure RBAC).
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.topic: how-to
ms.date: 02/27/2021
ms.author: rolyon
ms.openlocfilehash: 9780902a1c5f4a711e1abffa6b508c28efe269ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735885"
---
# <a name="list-azure-role-assignments-using-the-rest-api"></a>ListE Azure atribui funções utilizando a API REST

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Este artigo descreve como listar atribuições de funções usando a API REST.

> [!NOTE]
> Se a sua organização tiver funções de gestão subcontratadas a um prestador de serviços que utilize [a Azure delegada em gestão de recursos,](../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de funções autorizadas por esse prestador de serviços não serão mostradas aqui.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="list-role-assignments"></a>Listar atribuições de função

No Azure RBAC, para listar o acesso, lista as atribuições de funções. Para listar atribuições de funções, utilize uma das [atribuições de funções - List](/rest/api/authorization/roleassignments/list) REST APIs. Para refinar os seus resultados, especifique um âmbito e um filtro opcional.

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro do URI, substitua *{scope}* pelo âmbito para o qual pretende listar as atribuições de funções.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Recurso |

    No exemplo anterior, o microsoft.web é um fornecedor de recursos que se refere a uma instância do Serviço de Aplicações. Da mesma forma, pode utilizar quaisquer outros fornecedores de recursos e especificar o âmbito. Para obter mais informações, consulte [os fornecedores e tipos de recursos Azure](../azure-resource-manager/management/resource-providers-and-types.md) e as operações de [fornecedores de recursos Azure suportados.](resource-provider-operations.md)  
     
1. Substitua *{filter}* pela condição que pretende aplicar para filtrar a lista de atribuição de funções.

    > [!div class="mx-tableFixed"]
    > | Filtro | Description |
    > | --- | --- |
    > | `$filter=atScope()` | Lista atribuições de funções apenas para o âmbito especificado, não incluindo as atribuições de funções em subscópios. |
    > | `$filter=assignedTo('{objectId}')` | Lista atribuições de funções para um utilizador especificado ou principal de serviço.<br/>Se o utilizador for membro de um grupo que tem uma atribuição de funções, essa atribuição de funções também está listada. Este filtro é transitório para grupos, o que significa que se o utilizador é membro de um grupo e esse grupo é membro de outro grupo que tem uma atribuição de papel, essa atribuição de funções também está listada.<br/>Este filtro só aceita um ID de objeto para um utilizador ou um principal de serviço. Não se pode passar uma identificação de objeto para um grupo. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Lista atribuições de funções para o utilizador ou principal de serviço especificado e no âmbito especificado. |
    > | `$filter=principalId+eq+'{objectId}'` | Lista atribuições de funções para um utilizador especificado, grupo ou principal de serviço. |

O seguinte pedido lista todas as atribuições de funções para o utilizador especificado no âmbito de subscrição:

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

- [Atribuir funções Azure utilizando a API REST](role-assignments-rest.md)
- [Referência à API REST do Azure](/rest/api/azure/)
