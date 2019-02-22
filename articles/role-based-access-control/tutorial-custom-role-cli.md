---
title: Tutorial - criar uma função personalizada para recursos do Azure com a CLI do Azure | Documentos da Microsoft
description: Introdução à criação de uma função personalizada para recursos do Azure com a CLI do Azure.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.openlocfilehash: de1805d91f48b5718ecf293c2b8672ba40fb81a9
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588165"
---
# <a name="tutorial-create-a-custom-role-for-azure-resources-using-azure-cli"></a>Tutorial: Criar uma função personalizada para recursos do Azure com a CLI do Azure

Se o [funções incorporadas para recursos do Azure](built-in-roles.md) não atenderem às necessidades específicas da sua organização, pode criar suas próprias funções personalizadas. Neste tutorial vai criar uma função personalizada com o nome Pedidos de Suporte do Leitor com a CLI do Azure. A função personalizada permite ao utilizador ver tudo no plano de gestão de uma subscrição e também abrir pedidos de suporte.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma função personalizada
> * Listar funções personalizadas
> * Atualizar uma função personalizada
> * Eliminar uma função personalizada

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, irá precisar de:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso do Utilizador](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) ou [da CLI do Azure](/cli/azure/install-azure-cli)

## <a name="sign-in-to-azure-cli"></a>Iniciar sessão na CLI do Azure

Iniciar sessão na [CLI do Azure](/cli/azure/authenticate-azure-cli).

## <a name="create-a-custom-role"></a>Criar uma função personalizada

A forma mais fácil de criar uma função personalizada é começar com um modelo JSON, adicionar as suas alterações e, em seguida, criar uma nova função.

1. Reveja a lista de operações para o [fornecedor de recursos Microsoft.Support](resource-provider-operations.md#microsoftsupport). É útil conhecer as operações disponíveis para criar as suas permissões.

    | Operação | Descrição |
    | --- | --- |
    | Microsoft.Support/register/action | Regista para o Fornecedor de Recursos de Suporte |
    | Microsoft.Support/supportTickets/read | Obtém os detalhes do Pedido de Suporte (incluindo o estado, gravidade, detalhes de contacto e comunicações) ou obtém a lista dos Pedidos de Suporte nas subscrições. |
    | Microsoft.Support/supportTickets/write | Cria ou Atualiza um Pedido de Suporte. Pode criar um Pedido de Suporte para problemas relacionados com a Gestão Técnica, de Faturação, de Quotas ou de Subscrição. Pode atualizar a gravidade, os detalhes de contacto e as comunicações para pedidos de suporte existentes. |
    
1. Crie um novo ficheiro designado **ReaderSupportRole.json**.

1. Abra ReaderSupportRole.json num editor e adicione o seguinte JSON.

    Para obter informações sobre as diferentes propriedades, consulte [funções personalizadas para recursos do Azure](custom-roles.md).

    ```json
    {
      "Name": "",
      "IsCustom": true,
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId1}"
      ]
    }
    ```
    
1. Adicione as seguintes operações à propriedade `Actions`. Estas ações permitem ao utilizador ver tudo na subscrição e criar pedidos de suporte.

    ```
    "*/read",
    "Microsoft.Support/*"
    ```

1. Obtenha o ID da sua subscrição com o comando [az account list](/cli/azure/account#az-account-list).

    ```azurecli
    az account list --output table
    ```

1. Em `AssignableScopes`, substitua `{subscriptionId1}` pelo seu ID da subscrição.

    Tem de adicionar os IDs de subscrição explícitos, caso contrário, não poderá importar a função para a sua subscrição.

1. Altere as propriedades `Name` e `Description` para "Pedidos de Suporte do Leitor" e "Ver tudo na subscrição e abrir pedidos de suporte."

    O seu ficheiro JSON deverá ser semelhante ao seguinte:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. Para criar a nova função personalizada, utilize o comando[az role definition create](/cli/azure/role/definition#az-role-definition-create) e especifique o ficheiro de definição de função JSON.

    ```azurecli
    az role definition create --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

    A nova função personalizada está agora disponível e pode ser atribuída a utilizadores, grupos ou principais de serviço, tal como as funções incorporadas.

## <a name="list-custom-roles"></a>Listar funções personalizadas

- Para listar todas as funções personalizadas, utilize o comando [az role definition list](/cli/azure/role/definition#az-role-definition-list) com o parâmetro `--custom-role-only`.

    ```azurecli
    az role definition list --custom-role-only true
    ```
    
    ```Output
    [
      {
        "additionalProperties": {},
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ],
        "description": "View everything in the subscription and also open support tickets.",
        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
        "name": "22222222-2222-2222-2222-222222222222",
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Support/*",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Insights/diagnosticSettings/*/read"
            ],
            "additionalProperties": {},
            "dataActions": [],
            "notActions": [],
            "notDataActions": []
          }
        ],
        "roleName": "Reader Support Tickets",
        "roleType": "CustomRole",
        "type": "Microsoft.Authorization/roleDefinitions"
      }
    ]
    ```
    
    Também pode ver a função personalizada no portal do Azure.

    ![captura de ecrã da função personalizada importada no portal do Azure](./media/tutorial-custom-role-cli/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Para atualizar a função personalizada, atualize o ficheiro JSON e, em seguida, atualize a função personalizada.

1. Abra o ficheiro ReaderSupportRole.json.

1. Em `Actions`, adicione a operação para criar e gerir implementações do grupo de recursos `"Microsoft.Resources/deployments/*"`. Não se esqueça de incluir uma vírgula após a operação anterior.

    O seu ficheiro JSON atualizado deverá ser semelhante ao seguinte:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. Para atualizar a função personalizada, utilize o comando [az role definition update](/cli/azure/role/definition#az-role-definition-update) e especifique o ficheiro JSON atualizado.

    ```azurecli
    az role definition update --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*",
            "Microsoft.Resources/deployments/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```
    
## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada

- Utilize o comando [az role definition delete](/cli/azure/role/definition#az-role-definition-delete) e especifique o nome da função ou o ID da função para eliminar a função personalizada.

    ```azurecli
    az role definition delete --name "Reader Support Tickets"
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar funções personalizadas para recursos do Azure com a CLI do Azure](custom-roles-cli.md)