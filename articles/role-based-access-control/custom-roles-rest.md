---
title: Criar funções personalizadas para recursos do Azure com a API REST - Azure | Documentos da Microsoft
description: Saiba como criar funções personalizadas com controlo de acesso baseado em funções (RBAC) para recursos do Azure com a API REST. Isto inclui como listar, criar, atualizar e eliminar funções personalizadas.
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
ms.openlocfilehash: 4024f6fdb40c752ef61f348d15f681e81d81c08c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596684"
---
# <a name="create-custom-roles-for-azure-resources-using-the-rest-api"></a>Criar funções personalizadas para recursos do Azure com a API REST

Se o [funções incorporadas para recursos do Azure](built-in-roles.md) não atenderem às necessidades específicas da sua organização, pode criar suas próprias funções personalizadas. Este artigo descreve como criar e gerir funções personalizadas com a API REST.

## <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar todas as funções personalizadas num diretório, utilize o [definições de funções – lista](/rest/api/authorization/roledefinitions/list) REST API.

1. Começar com o pedido seguinte:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Substitua *{filtro}* com o tipo de função.

    | Filtro | Descrição |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtrar com base no tipo CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Lista de funções personalizadas com um âmbito

Para listar as funções personalizadas a um âmbito, utilize o [definições de funções – lista](/rest/api/authorization/roledefinitions/list) REST API.

1. Começar com o pedido seguinte:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. No URI, substitua *{âmbito}* com o âmbito para o qual pretende listar as funções.

    | Âmbito | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{filtro}* com o tipo de função.

    | Filtro | Descrição |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtrar com base no tipo CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Listar uma definição de função personalizada por nome

Para obter informações sobre uma função personalizada pelo respetivo nome de exibição, utilize o [obter definições de funções –](/rest/api/authorization/roledefinitions/get) REST API.

1. Começar com o pedido seguinte:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. No URI, substitua *{âmbito}* com o âmbito para o qual pretende listar as funções.

    | Âmbito | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{filtro}* com o nome a apresentar para a função.

    | Filtro | Descrição |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Use o formulário com codificação URL do nome a apresentar exata da função. Por exemplo, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Listar uma definição de função personalizada por ID

Para obter informações sobre uma função personalizada pelo respetivo Identificador exclusivo, utilize o [obter definições de funções –](/rest/api/authorization/roledefinitions/get) REST API.

1. Utilize o [definições de funções – lista](/rest/api/authorization/roledefinitions/list) API REST para obter o identificador GUID para a função.

1. Começar com o pedido seguinte:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. No URI, substitua *{âmbito}* com o âmbito para o qual pretende listar as funções.

    | Âmbito | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* com o identificador GUID de definição de função.

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar uma função personalizada, utilize o [definições de funções – criar ou atualizar](/rest/api/authorization/roledefinitions/createorupdate) REST API. Para chamar esta API, tem de ser assinado com um utilizador que está atribuído uma função que tenha as `Microsoft.Authorization/roleDefinitions/write` permissão em todos os `assignableScopes`. Das funções incorporadas, apenas [proprietário](built-in-roles.md#owner) e [administrador de acesso de utilizador](built-in-roles.md#user-access-administrator) inclui esta permissão.

1. Reveja a lista de [operações de fornecedor de recursos](resource-provider-operations.md) que estão disponíveis para criar as permissões para a sua função personalizada.

1. Utilize uma ferramenta GUID para gerar um identificador exclusivo que será utilizado para o identificador de função personalizada. O identificador tem o formato: `00000000-0000-0000-0000-000000000000`

1. Começar com o seguinte pedido e o corpo:

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

1. No URI, substitua *{âmbito}* com o primeiro `assignableScopes` da função personalizada.

    | Âmbito | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* com o identificador GUID de função personalizada.

1. Dentro do corpo do pedido, na `assignableScopes` propriedade, substitua *{roleDefinitionId}* com o identificador GUID.

1. Substitua *{subscriptionId}* com o identificador de subscrição.

1. Na `actions` propriedade, adicionar as operações que permite que a função a ser executada.

1. Na `notActions` propriedade, adicionar as operações que são excluídas da permitidos `actions`.

1. Na `roleName` e `description` propriedades, especifique um nome de função exclusivo e uma descrição. Para obter mais informações sobre as propriedades, consulte [funções personalizadas](custom-roles.md).

    O código a seguir mostra um exemplo de um corpo de pedido:

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

Para atualizar uma função personalizada, utilize o [definições de funções – criar ou atualizar](/rest/api/authorization/roledefinitions/createorupdate) REST API. Para chamar esta API, tem de ser assinado com um utilizador que está atribuído uma função que tenha as `Microsoft.Authorization/roleDefinitions/write` permissão em todos os `assignableScopes`. Das funções incorporadas, apenas [proprietário](built-in-roles.md#owner) e [administrador de acesso de utilizador](built-in-roles.md#user-access-administrator) inclui esta permissão.

1. Utilize o [definições de funções – lista](/rest/api/authorization/roledefinitions/list) ou [obter definições de funções –](/rest/api/authorization/roledefinitions/get) API de REST para obter informações sobre a função personalizada. Para obter mais informações, consulte quanto mais cedo [lista de funções personalizadas](#list-custom-roles) secção.

1. Começar com o pedido seguinte:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. No URI, substitua *{âmbito}* com o primeiro `assignableScopes` da função personalizada.

    | Âmbito | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* com o identificador GUID de função personalizada.

1. Com base nas informações sobre a função personalizada, crie um corpo de pedido com o seguinte formato:

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

1. Atualize o corpo do pedido com as alterações que deseja tornar-se para a função personalizada.

    O código a seguir mostra um exemplo de um corpo de pedido com uma nova ação de definições de diagnóstico, adicionada:

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

Para eliminar uma função personalizada, utilize o [definições de funções – eliminar](/rest/api/authorization/roledefinitions/delete) REST API. Para chamar esta API, tem de ser assinado com um utilizador que está atribuído uma função que tenha as `Microsoft.Authorization/roleDefinitions/delete` permissão em todos os `assignableScopes`. Das funções incorporadas, apenas [proprietário](built-in-roles.md#owner) e [administrador de acesso de utilizador](built-in-roles.md#user-access-administrator) inclui esta permissão.

1. Utilize o [definições de funções – lista](/rest/api/authorization/roledefinitions/list) ou [obter definições de funções –](/rest/api/authorization/roledefinitions/get) API REST para obter o identificador GUID de função personalizada. Para obter mais informações, consulte quanto mais cedo [lista de funções personalizadas](#list-custom-roles) secção.

1. Começar com o pedido seguinte:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. No URI, substitua *{âmbito}* com o âmbito de que pretende eliminar a função personalizada.

    | Âmbito | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* com o identificador GUID de função personalizada.

## <a name="next-steps"></a>Passos Seguintes

- [Custom roles for Azure resources](custom-roles.md) (Funções personalizadas para recursos do Azure)
- [Gerir o acesso aos recursos do Azure através do RBAC e a API REST](role-assignments-rest.md)
- [Referência à API REST do Azure](/rest/api/azure/)