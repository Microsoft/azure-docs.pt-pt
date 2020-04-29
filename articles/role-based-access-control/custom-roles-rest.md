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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: fda0400310f46da64322654c42af75521746d679
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062193"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Criar ou atualizar funções personalizadas para os recursos Do Azure utilizando a API REST

> [!IMPORTANT]
> A adição de `AssignableScopes` um grupo de gestão está atualmente em pré-visualização.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Se as [funções incorporadas para os recursos Azure](built-in-roles.md) não atenderem às necessidades específicas da sua organização, pode criar os seus próprios papéis personalizados. Este artigo descreve como listar, criar, atualizar ou eliminar funções personalizadas usando a API REST.

## <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar todas as funções personalizadas num diretório, utilize as [Definições](/rest/api/authorization/roledefinitions/list) de Papel - Lista REST API.

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Substitua *{filter}* com o tipo de função.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrição |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtro com base no tipo CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Listar papéis personalizados num âmbito

Para listar as funções personalizadas num âmbito, utilize as [Definições](/rest/api/authorization/roledefinitions/list) de Papel - Lista REST API.

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro do URI, substitua *{scope}* com o âmbito para o qual pretende enumerar as funções.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupo de recursos |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Recurso |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |

1. Substitua *{filter}* com o tipo de função.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrição |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtro com base no tipo CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Listar uma definição de papel personalizada pelo nome

Para obter informações sobre um papel personalizado pelo seu nome de exibição, use as [Definições](/rest/api/authorization/roledefinitions/get) de Papel - Obter REST API.

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro do URI, substitua *{scope}* com o âmbito para o qual pretende enumerar as funções.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupo de recursos |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Recurso |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |

1. Substitua *{filter}* com o nome do visor para a função.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrição |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Utilize a forma codificada de URL do nome exato do ecrã da função. Por exemplo,`$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Listar uma definição de função personalizada por ID

Para obter informações sobre um papel personalizado pelo seu identificador único, use as [Definições](/rest/api/authorization/roledefinitions/get) de Papel - Obter REST API.

1. Utilize as Definições de [Função - Lista](/rest/api/authorization/roledefinitions/list) REST API para obter o identificador GUID para o papel.

1. Comece com o seguinte pedido:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{scope}* com o âmbito para o qual pretende enumerar as funções.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupo de recursos |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Recurso |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da definição de função.

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar uma função personalizada, use as Definições de [Função - Criar ou Atualizar](/rest/api/authorization/roledefinitions/createorupdate) a API REST. Para chamar a isto API, deve ser contratado com um utilizador `Microsoft.Authorization/roleDefinitions/write` que tenha `assignableScopes`uma função que tenha a permissão em todos os . Das funções incorporadas, apenas o [Proprietário](built-in-roles.md#owner) e o Administrador de [Acesso ao Utilizador](built-in-roles.md#user-access-administrator) incluem esta permissão.

1. Reveja a lista de [operações](resource-provider-operations.md) do fornecedor de recursos que estão disponíveis para criar as permissões para o seu papel personalizado.

1. Utilize uma ferramenta GUID para gerar um identificador único que será utilizado para o identificador de funções personalizadas. O identificador tem o formato:`00000000-0000-0000-0000-000000000000`

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

1. Dentro do URI, substitua *{scope}* pelo primeiro `assignableScopes` da função personalizada.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupo de recursos |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da função personalizada.

1. Dentro do organismo de pedido, substitua *{roleDefinitionId}* pelo identificador GUID.

1. Se `assignableScopes` for um grupo de subscrição ou de recursos, substitua as instâncias *{subscriçãoId}* ou *{resourceGroup}* com os seus identificadores.

1. Se `assignableScopes` for um grupo de gestão, substitua a instância *{groupId}* pelo seu identificador de grupo de gestão. A adição de `assignableScopes` um grupo de gestão está atualmente em pré-visualização.

1. No `actions` imóvel, adicione as operações que a função permite ser realizadas.

1. No `notActions` imóvel, adicione as operações que `actions`estão excluídas do permitido .

1. Nas `roleName` propriedades `description` e propriedades, especifique um nome de papel único e uma descrição. Para mais informações sobre as propriedades, consulte [as funções personalizadas.](custom-roles.md)

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
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Para atualizar uma função personalizada, utilize as Definições de [Função - Criar ou Atualizar](/rest/api/authorization/roledefinitions/createorupdate) a API REST. Para chamar a isto API, deve ser contratado com um utilizador `Microsoft.Authorization/roleDefinitions/write` que tenha `assignableScopes`uma função que tenha a permissão em todos os . Das funções incorporadas, apenas o [Proprietário](built-in-roles.md#owner) e o Administrador de [Acesso ao Utilizador](built-in-roles.md#user-access-administrator) incluem esta permissão.

1. Utilize as [Definições](/rest/api/authorization/roledefinitions/list) de Papel - Lista ou [Definições](/rest/api/authorization/roledefinitions/get) de Papel - Obtenha a API REST para obter informações sobre o papel personalizado. Para mais informações, consulte a secção de [papéis personalizados](#list-custom-roles) da Lista Anterior.

1. Comece com o seguinte pedido:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{scope}* pelo primeiro `assignableScopes` da função personalizada.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupo de recursos |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |

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
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
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
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada

Para eliminar uma função personalizada, utilize as Definições de [Função - Eliminar](/rest/api/authorization/roledefinitions/delete) a API REST. Para chamar a isto API, deve ser contratado com um utilizador `Microsoft.Authorization/roleDefinitions/delete` que tenha `assignableScopes`uma função que tenha a permissão em todos os . Das funções incorporadas, apenas o [Proprietário](built-in-roles.md#owner) e o Administrador de [Acesso ao Utilizador](built-in-roles.md#user-access-administrator) incluem esta permissão.

1. Utilize as [Definições](/rest/api/authorization/roledefinitions/list) de Papel - Lista ou [Definições](/rest/api/authorization/roledefinitions/get) de Papel - Obtenha a API REST para obter o identificador GUID da função personalizada. Para mais informações, consulte a secção de [papéis personalizados](#list-custom-roles) da Lista Anterior.

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

- [Custom roles for Azure resources](custom-roles.md) (Funções personalizadas para recursos do Azure)
- [Gerir o acesso aos recursos Azure utilizando o RBAC e a API REST](role-assignments-rest.md)
- [Referência da API do Rest Azure](/rest/api/azure/)
