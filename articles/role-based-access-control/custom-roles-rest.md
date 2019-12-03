---
title: Criar funções personalizadas ou atualizadas para recursos do Azure usando a API REST – Azure | Microsoft Docs
description: Saiba como listar, criar, atualizar ou excluir funções personalizadas com RBAC (controle de acesso baseado em função) para recursos do Azure usando a API REST.
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
ms.openlocfilehash: 2e5dcd7593ba0992337396bc7c05cc30351644dc
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702973"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Criar ou atualizar funções personalizadas para recursos do Azure usando a API REST

Se as [funções internas para os recursos do Azure](built-in-roles.md) não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias funções personalizadas. Este artigo descreve como listar, criar, atualizar ou excluir funções personalizadas usando a API REST.

## <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar todas as funções personalizadas em um diretório, use as [definições de função-lista](/rest/api/authorization/roledefinitions/list) de API REST.

1. Comece com a seguinte solicitação:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Substitua *{Filter}* pelo tipo de função.

    | Filtrar | Descrição |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtrar com base no tipo CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Listar funções personalizadas em um escopo

Para listar funções personalizadas em um escopo, use as [definições de função-lista](/rest/api/authorization/roledefinitions/list) de API REST.

1. Comece com a seguinte solicitação:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro do URI, substitua *{Scope}* pelo escopo para o qual você deseja listar as funções.

    | Âmbito | Tipo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{Filter}* pelo tipo de função.

    | Filtrar | Descrição |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtrar com base no tipo CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Listar uma definição de função personalizada por nome

Para obter informações sobre uma função personalizada por seu nome de exibição, use as [definições de função-obter](/rest/api/authorization/roledefinitions/get) API REST.

1. Comece com a seguinte solicitação:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro do URI, substitua *{Scope}* pelo escopo para o qual você deseja listar as funções.

    | Âmbito | Tipo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{Filter}* pelo nome de exibição da função.

    | Filtrar | Descrição |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Use a forma codificada da URL do nome de exibição exato da função. Por exemplo, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Listar uma definição de função personalizada por ID

Para obter informações sobre uma função personalizada por seu identificador exclusivo, use as [definições de função-obter](/rest/api/authorization/roledefinitions/get) API REST.

1. Use a API REST de [definições de função-List](/rest/api/authorization/roledefinitions/list) para obter o identificador de GUID para a função.

1. Comece com a seguinte solicitação:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{Scope}* pelo escopo para o qual você deseja listar as funções.

    | Âmbito | Tipo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da definição de função.

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar uma função personalizada, use as [definições de função – criar ou atualizar](/rest/api/authorization/roledefinitions/createorupdate) a API REST. Para chamar essa API, você deve estar conectado com um usuário que recebe uma função que tem a permissão `Microsoft.Authorization/roleDefinitions/write` em todos os `assignableScopes`. Das funções internas, somente o [proprietário](built-in-roles.md#owner) e o [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) incluem essa permissão.

1. Examine a lista de [operações do provedor de recursos](resource-provider-operations.md) que estão disponíveis para criar as permissões para sua função personalizada.

1. Use uma ferramenta GUID para gerar um identificador exclusivo que será usado para o identificador de função personalizado. O identificador tem o formato: `00000000-0000-0000-0000-000000000000`

1. Comece com a seguinte solicitação e corpo:

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

1. Dentro do URI, substitua *{Scope}* pelo primeiro `assignableScopes` da função personalizada.

    | Âmbito | Tipo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da função personalizada.

1. No corpo da solicitação, na propriedade `assignableScopes`, substitua *{roleDefinitionId}* pelo identificador GUID.

1. Substitua *{SubscriptionId}* pelo seu identificador de assinatura.

1. Na propriedade `actions`, adicione as operações que a função permite que sejam executadas.

1. Na propriedade `notActions`, adicione as operações que são excluídas do `actions`permitido.

1. Nas propriedades `roleName` e `description`, especifique um nome de função exclusivo e uma descrição. Para obter mais informações sobre as propriedades, consulte [funções personalizadas](custom-roles.md).

    Veja a seguir um exemplo de um corpo de solicitação:

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

Para atualizar uma função personalizada, use as [definições de função-criar ou atualizar](/rest/api/authorization/roledefinitions/createorupdate) a API REST. Para chamar essa API, você deve estar conectado com um usuário que recebe uma função que tem a permissão `Microsoft.Authorization/roleDefinitions/write` em todos os `assignableScopes`. Das funções internas, somente o [proprietário](built-in-roles.md#owner) e o [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) incluem essa permissão.

1. Use as definições de [função-lista](/rest/api/authorization/roledefinitions/list) ou [definições de função-obter](/rest/api/authorization/roledefinitions/get) API REST para obter informações sobre a função personalizada. Para obter mais informações, consulte a seção [listar funções personalizadas](#list-custom-roles) anteriores.

1. Comece com a seguinte solicitação:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{Scope}* pelo primeiro `assignableScopes` da função personalizada.

    | Âmbito | Tipo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da função personalizada.

1. Com base nas informações sobre a função personalizada, crie um corpo de solicitação com o seguinte formato:

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

1. Atualize o corpo da solicitação com as alterações que você deseja fazer na função personalizada.

    Veja a seguir um exemplo de um corpo de solicitação com uma nova ação de configurações de diagnóstico adicionada:

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

Para excluir uma função personalizada, use as [definições de função-excluir](/rest/api/authorization/roledefinitions/delete) API REST. Para chamar essa API, você deve estar conectado com um usuário que recebe uma função que tem a permissão `Microsoft.Authorization/roleDefinitions/delete` em todos os `assignableScopes`. Das funções internas, somente o [proprietário](built-in-roles.md#owner) e o [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) incluem essa permissão.

1. Use as definições de [função-lista](/rest/api/authorization/roledefinitions/list) ou [definições de função-obtenha](/rest/api/authorization/roledefinitions/get) a API REST para obter o identificador GUID da função personalizada. Para obter mais informações, consulte a seção [listar funções personalizadas](#list-custom-roles) anteriores.

1. Comece com a seguinte solicitação:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{Scope}* pelo escopo do qual você deseja excluir a função personalizada.

    | Âmbito | Tipo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscrição |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da função personalizada.

## <a name="next-steps"></a>Passos seguintes

- [Custom roles for Azure resources](custom-roles.md) (Funções personalizadas para recursos do Azure)
- [Gerenciar o acesso aos recursos do Azure usando o RBAC e a API REST](role-assignments-rest.md)
- [Referência à API REST do Azure](/rest/api/azure/)