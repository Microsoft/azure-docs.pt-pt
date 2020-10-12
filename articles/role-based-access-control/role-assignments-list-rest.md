---
title: List Azure fun assignments using the REST API - Azure RBAC
description: Saiba como determinar quais os recursos que os utilizadores, grupos, principais de serviços ou identidades geridas têm acesso à utilização do controlo de acesso baseado em funções REST API e Azure (Azure RBAC).
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
ms.topic: how-to
ms.date: 05/06/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 634e1111c9374a1749e7dbb0666740ce2833a688
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84790981"
---
# <a name="list-azure-role-assignments-using-the-rest-api"></a>ListE Azure atribui funções utilizando a API REST

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Este artigo descreve como listar atribuições de funções usando a API REST.

> [!NOTE]
> Se a sua organização tiver funções de gestão subcontratadas a um prestador de serviços que utilize [a Azure delegada em gestão de recursos,](../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de funções autorizadas por esse prestador de serviços não serão mostradas aqui.

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

    No exemplo anterior, o microsoft.web é um fornecedor de recursos que se refere a uma instância do Serviço de Aplicações. Da mesma forma, pode utilizar quaisquer outros fornecedores de recursos e especificar o âmbito. Para obter mais informações, consulte [os fornecedores e tipos de recursos da Azure Resource](../azure-resource-manager/management/resource-providers-and-types.md) e as operações de [fornecedores de recursos do Azure Resource Manager.](resource-provider-operations.md)  
     
1. Substitua *{filter}* pela condição que pretende aplicar para filtrar a lista de atribuição de funções.

    > [!div class="mx-tableFixed"]
    > | Filtrar | Descrição |
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

- [Utilizar a API REST para adicionar ou remover atribuições de funções do Azure](role-assignments-rest.md)
- [Referência à API REST do Azure](/rest/api/azure/)
