---
title: Criar ou atualizar funções personalizadas para os recursos do Azure com a API REST
description: Aprenda a listar, criar, atualizar ou eliminar funções personalizadas com controlo de acesso baseado em papéis (RBAC) para recursos Azure utilizando a API REST.
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
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 145bc45e1b7faeddc23cf5f0662337e15ab51c29
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137366"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Criar ou atualizar funções personalizadas para os recursos Do Azure utilizando a API REST

Se as [funções incorporadas para os recursos Azure](built-in-roles.md) não atenderem às necessidades específicas da sua organização, pode criar os seus próprios papéis personalizados. Este artigo descreve como listar, criar, atualizar ou eliminar funções personalizadas usando a API REST.

## <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar todas as funções personalizadas num diretório, utilize as [Definições](/rest/api/authorization/roledefinitions/list) de Papel - Lista REST API.

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Substitua *{filter}* com o tipo de função.

    | Filtro | Descrição |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtro com base no tipo CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Listar papéis personalizados num âmbito

Para listar as funções personalizadas num âmbito, utilize as [Definições](/rest/api/authorization/roledefinitions/list) de Papel - Lista REST API.

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro do URI, substitua *{scope}* com o âmbito para o qual pretende enumerar as funções.

    | Âmbito | Tipo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{filter}* com o tipo de função.

    | Filtro | Descrição |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtro com base no tipo CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Listar uma definição de papel personalizada pelo nome

Para obter informações sobre um papel personalizado pelo seu nome de exibição, use as [Definições](/rest/api/authorization/roledefinitions/get) de Papel - Obter REST API.

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro do URI, substitua *{scope}* com o âmbito para o qual pretende enumerar as funções.

    | Âmbito | Tipo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{filter}* com o nome do visor para a função.

    | Filtro | Descrição |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Utilize a forma codificada de URL do nome exato do ecrã da função. Por exemplo, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Listar uma definição de função personalizada por ID

Para obter informações sobre um papel personalizado pelo seu identificador único, use as [Definições](/rest/api/authorization/roledefinitions/get) de Papel - Obter REST API.

1. Utilize as Definições de [Função - Lista](/rest/api/authorization/roledefinitions/list) REST API para obter o identificador GUID para o papel.

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{scope}* com o âmbito para o qual pretende enumerar as funções.

    | Âmbito | Tipo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da definição de função.

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar uma função personalizada, use as Definições de [Função - Criar ou Atualizar](/rest/api/authorization/roledefinitions/createorupdate) a API REST. Para chamar a isto API, deve ser contratado com um utilizador que tenha uma função que tenha a `Microsoft.Authorization/roleDefinitions/write` permissão em todos os `assignableScopes`. Das funções incorporadas, apenas o [Proprietário](built-in-roles.md#owner) e o Administrador de [Acesso ao Utilizador](built-in-roles.md#user-access-administrator) incluem esta permissão.

1. Reveja a lista de [operações](resource-provider-operations.md) do fornecedor de recursos que estão disponíveis para criar as permissões para o seu papel personalizado.

1. Utilize uma ferramenta GUID para gerar um identificador único que será utilizado para o identificador de funções personalizadas. O identificador tem o formato: `00000000-0000-0000-0000-000000000000`

1. Comece com o seguinte pedido e corpo:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Dentro do URI, substitua *{scope}* pela primeira `assignableScopes` da função personalizada.

    | Âmbito | Tipo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da função personalizada.

1. Dentro do organismo de pedido, na propriedade `assignableScopes`, *substitua {roleDefinitionId}* pelo identificador GUID.

1. Substitua *{subscriçãoId}* com o seu identificador de subscrição.

1. Na `actions` imóvel, adicione as operações que o papel permite ser realizado.

1. Na propriedade `notActions`, adicione as operações que estão excluídas da `actions`permitida .

1. Nas propriedades `roleName` e `description`, especifique um nome de papel único e uma descrição. Para mais informações sobre as propriedades, consulte [as funções personalizadas.](custom-roles.md)

    O seguinte mostra um exemplo de um órgão de pedido:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Para atualizar uma função personalizada, utilize as Definições de [Função - Criar ou Atualizar](/rest/api/authorization/roledefinitions/createorupdate) a API REST. Para chamar a isto API, deve ser contratado com um utilizador que tenha uma função que tenha a `Microsoft.Authorization/roleDefinitions/write` permissão em todos os `assignableScopes`. Das funções incorporadas, apenas o [Proprietário](built-in-roles.md#owner) e o Administrador de [Acesso ao Utilizador](built-in-roles.md#user-access-administrator) incluem esta permissão.

1. Utilize as [Definições](/rest/api/authorization/roledefinitions/list) de Papel - Lista ou [Definições](/rest/api/authorization/roledefinitions/get) de Papel - Obtenha a API REST para obter informações sobre o papel personalizado. Para mais informações, consulte a secção de [papéis personalizados](#list-custom-roles) da Lista Anterior.

1. Comece com o seguinte pedido:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{scope}* pela primeira `assignableScopes` da função personalizada.

    | Âmbito | Tipo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da função personalizada.

1. Com base na informação sobre a função personalizada, crie um corpo de pedido com o seguinte formato:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Atualize o organismo de pedido com as alterações que pretende fazer para a função personalizada.

    O seguinte mostra um exemplo de um corpo de pedido com uma nova ação de definições de diagnóstico adicionada:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada

Para eliminar uma função personalizada, utilize as Definições de [Função - Eliminar](/rest/api/authorization/roledefinitions/delete) a API REST. Para chamar a isto API, deve ser contratado com um utilizador que tenha uma função que tenha a `Microsoft.Authorization/roleDefinitions/delete` permissão em todos os `assignableScopes`. Das funções incorporadas, apenas o [Proprietário](built-in-roles.md#owner) e o Administrador de [Acesso ao Utilizador](built-in-roles.md#user-access-administrator) incluem esta permissão.

1. Utilize as [Definições](/rest/api/authorization/roledefinitions/list) de Papel - Lista ou [Definições](/rest/api/authorization/roledefinitions/get) de Papel - Obtenha a API REST para obter o identificador GUID da função personalizada. Para mais informações, consulte a secção de [papéis personalizados](#list-custom-roles) da Lista Anterior.

1. Comece com o seguinte pedido:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{scope}* pelo âmbito que pretende eliminar a função personalizada.

    | Âmbito | Tipo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da função personalizada.

## <a name="next-steps"></a>Passos seguintes

- [Custom roles for Azure resources](custom-roles.md) (Funções personalizadas para recursos do Azure)
- [Gerir o acesso aos recursos Azure utilizando o RBAC e a API REST](role-assignments-rest.md)
- [Referência à API REST do Azure](/rest/api/azure/)
