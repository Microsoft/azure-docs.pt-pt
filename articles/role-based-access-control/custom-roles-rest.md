---
title: Criar ou atualizar funções personalizadas Azure usando a API REST - Azure RBAC
description: Saiba como listar, criar, atualizar ou eliminar funções personalizadas Azure utilizando o controlo de acesso baseado em funções REST API e Azure (Azure RBAC).
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ab64765fc4e329c8edbf7a4db813113d20d476b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100557519"
---
# <a name="create-or-update-azure-custom-roles-using-the-rest-api"></a>Criar ou atualizar funções personalizadas Azure usando a API REST

> [!IMPORTANT]
> A adição de um grupo de gestão `AssignableScopes` está atualmente em pré-visualização.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se os [papéis incorporados do Azure](built-in-roles.md) não corresponderem às necessidades específicas da sua organização, pode criar os seus próprios papéis personalizados. Este artigo descreve como listar, criar, atualizar ou eliminar funções personalizadas usando a API REST.

## <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar todas as funções personalizadas num diretório, utilize as [Definições de Função - Lista](/rest/api/authorization/roledefinitions/list) REST API.

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Substitua *{filter}* pelo tipo de função.

    > [!div class="mx-tableFixed"]
    > | Filtro | Description |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtro baseado no tipo CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Listar funções personalizadas num âmbito

Para listar funções personalizadas num âmbito, utilize as [Definições de Função - Lista](/rest/api/authorization/roledefinitions/list) REST API.

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro do URI, *substitua {scope}* pelo âmbito para o qual pretende listar as funções.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupo de recursos |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Recurso |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |

1. Substitua *{filter}* pelo tipo de função.

    > [!div class="mx-tableFixed"]
    > | Filtro | Description |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtro baseado no tipo CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Listar uma definição de função personalizada pelo nome

Para obter informações sobre um papel personalizado pelo seu nome de exibição, utilize as [Definições de Função - Get](/rest/api/authorization/roledefinitions/get) REST API.

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro do URI, *substitua {scope}* pelo âmbito para o qual pretende listar as funções.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupo de recursos |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Recurso |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |

1. Substitua *{filter}* pelo nome de visualização para a função.

    > [!div class="mx-tableFixed"]
    > | Filtro | Description |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Utilize a forma codificada url do nome exato do visor da função. Por exemplo, `$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Listar uma definição de função personalizada por ID

Para obter informações sobre um papel personalizado pelo seu identificador único, use as [Definições de Função - Get](/rest/api/authorization/roledefinitions/get) REST API.

1. Utilize as [Definições de Função - Lista](/rest/api/authorization/roledefinitions/list) REST API para obter o identificador GUID para o papel.

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro do URI, *substitua {scope}* pelo âmbito para o qual pretende listar as funções.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupo de recursos |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Recurso |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da definição de função.

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar um papel personalizado, utilize as [Definições de Função - Criar ou Atualizar](/rest/api/authorization/roledefinitions/createorupdate) a API REST. Para chamar a isto API, você deve ser assinado com um utilizador que é atribuído uma função que tem a `Microsoft.Authorization/roleDefinitions/write` permissão em todos os `assignableScopes` . Das funções incorporadas, apenas [o Proprietário](built-in-roles.md#owner) e o Administrador de Acesso [ao Utilizador](built-in-roles.md#user-access-administrator) incluem esta permissão.

1. Reveja a lista de [operações de fornecedores](resource-provider-operations.md) de recursos disponíveis para criar as permissões para o seu papel personalizado.

1. Utilize uma ferramenta GUID para gerar um identificador único que será utilizado para o identificador de funções personalizado. O identificador tem o formato: `00000000-0000-0000-0000-000000000000`

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
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Dentro do URI, *substitua {scope}* com o primeiro `assignableScopes` da função personalizada.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupo de recursos |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da função personalizada.

1. Dentro do órgão de pedido, substitua *{roleDefinitionId}* pelo identificador GUID.

1. Se `assignableScopes` for uma subscrição ou grupo de recursos, substitua as instâncias *{subscriptionId}* ou *{resourceGroup}* pelos seus identificadores.

1. Se `assignableScopes` for um grupo de gestão, substitua a instância *{groupId}* pelo seu identificador de grupo de gestão. A adição de um grupo de gestão `assignableScopes` está atualmente em pré-visualização.

1. Na `actions` propriedade, adicione as operações que o papel permite ser realizada.

1. Na `notActions` propriedade, adicione as operações que estão excluídas do `actions` permitido.

1. Nas `roleName` propriedades e `description` propriedades, especifique um nome de função único e uma descrição. Para obter mais informações sobre as propriedades, consulte [as funções personalizadas Azure](custom-roles.md).

    O seguinte mostra um exemplo de um organismo de pedido:

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
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Para atualizar uma função personalizada, utilize as [Definições de Função - Criar ou Atualizar](/rest/api/authorization/roledefinitions/createorupdate) a API REST. Para chamar a isto API, você deve ser assinado com um utilizador que é atribuído uma função que tem a `Microsoft.Authorization/roleDefinitions/write` permissão em todos os `assignableScopes` . Das funções incorporadas, apenas [o Proprietário](built-in-roles.md#owner) e o Administrador de Acesso [ao Utilizador](built-in-roles.md#user-access-administrator) incluem esta permissão.

1. Use as [Definições de Função - Lista](/rest/api/authorization/roledefinitions/list) ou [Definições de Função - Obtenha](/rest/api/authorization/roledefinitions/get) REST API para obter informações sobre o papel personalizado. Para mais informações, consulte a secção [de funções personalizadas da Lista](#list-custom-roles) anterior.

1. Comece com o seguinte pedido:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro do URI, *substitua {scope}* com o primeiro `assignableScopes` da função personalizada.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupo de recursos |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da função personalizada.

1. Com base nas informações sobre o papel personalizado, crie um corpo de pedido com o seguinte formato:

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
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Atualize o órgão de pedido com as alterações que pretende fazer para a função personalizada.

    O seguinte mostra um exemplo de um organismo de pedido com uma nova ação de diagnóstico adicionada:

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
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada

Para eliminar uma função personalizada, utilize as [Definições de Função - Eliminar](/rest/api/authorization/roledefinitions/delete) a API REST. Para chamar a isto API, você deve ser assinado com um utilizador que é atribuído uma função que tem a `Microsoft.Authorization/roleDefinitions/delete` permissão em todos os `assignableScopes` . Das funções incorporadas, apenas [o Proprietário](built-in-roles.md#owner) e o Administrador de Acesso [ao Utilizador](built-in-roles.md#user-access-administrator) incluem esta permissão.

1. Utilize as [Definições de Função - Lista](/rest/api/authorization/roledefinitions/list) ou [Definições de Função - Obtenha](/rest/api/authorization/roledefinitions/get) a API REST para obter o identificador GUID da função personalizada. Para mais informações, consulte a secção [de funções personalizadas da Lista](#list-custom-roles) anterior.

1. Comece com o seguinte pedido:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{scope}* pelo âmbito que pretende eliminar a função personalizada.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupo de recursos |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da função personalizada.

## <a name="next-steps"></a>Passos seguintes

- [Funções personalizadas do Azure](custom-roles.md)
- [Atribuir funções Azure utilizando a API REST](role-assignments-rest.md)
- [Referência à API REST do Azure](/rest/api/azure/)
