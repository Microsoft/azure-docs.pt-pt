---
title: Restringir o acesso dos utilizadores a operações de dados apenas com a Azure Cosmos DB
description: Saiba como restringir o acesso a operações de dados apenas com a Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 44a62643c459fb61e7a2a95c2a9dd55ea4f19111
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570645"
---
# <a name="restrict-user-access-to-data-operations-in-azure-cosmos-db"></a>Restringir o acesso dos utilizadores a operações de dados na Azure Cosmos DB

Em Azure Cosmos DB, existem duas formas de autenticar as suas interações com o serviço de base de dados:

- utilizando a sua identidade de Diretório Ativo Azure ao interagir com o portal Azure,
- usando [chaves](secure-access-to-data.md#primary-keys) DB da Azure Cosmos ou [fichas de recursos ao](secure-access-to-data.md#resource-tokens) emitir chamadas de APIs e SDKs.

Cada método de autenticação dá acesso a diferentes conjuntos de operações, com alguma sobreposição:

:::image type="content" source="./media/how-to-restrict-user-data/operations.png" alt-text="Divisão de operações por tipo de autenticação" border="false":::

Em alguns cenários, poderá querer restringir alguns utilizadores da sua organização a realizar operações de dados (ou seja, pedidos e consultas da CRUD) apenas. Este é normalmente o caso dos desenvolvedores que não precisam de criar ou apagar recursos, ou alterar a produção a forrável dos contentores em que estão a trabalhar.

Pode restringir o acesso aplicando os seguintes passos:
1. Criar um papel personalizado do Azure Ative Directory para os utilizadores a quem pretende restringir o acesso. O papel personalizado do Ative Directory deve ter um nível de acesso fino às operações utilizando [as ações granulares](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)da Azure Cosmos DB .
1. Não permitir a execução de operações não-data com chaves. Pode conseguir isso limitando estas operações apenas às chamadas do Azure Resource Manager.

As próximas secções deste artigo mostram como executar estes passos.

> [!NOTE]
> Para executar os comandos nas próximas secções, é necessário instalar o Módulo Azure PowerShell 3.0.0 ou posterior, bem como a [Função do Proprietário Azure](../role-based-access-control/built-in-roles.md#owner) na subscrição que está a tentar modificar.

Nos scripts PowerShell nas secções seguintes, substitua os seguintes espaços reservados por valores específicos do seu ambiente:
- `$MySubscriptionId` - O ID de subscrição que contém a conta Azure Cosmos onde pretende limitar as permissões. Por exemplo: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName` - O grupo de recursos que contém a conta Azure Cosmos. Por exemplo: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName` - O nome da sua conta de Azure Cosmos. Por exemplo: `mycosmosdbsaccount`.
- `$MyUserName` - O login ( username@domain ) do utilizador para quem pretende limitar o acesso. Por exemplo: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>selecione a subscrição do Azure

Os comandos Azure PowerShell exigem que faça login e selecione a subscrição para executar os comandos:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Crie o papel personalizado do Azure Ative Directory

O seguinte script cria uma atribuição de papel de Azure Ative Directory com acesso "Key Only" para contas Azure Cosmos. O papel baseia-se em [papéis personalizados da Azure](../role-based-access-control/custom-roles.md) e [ações granulares para a Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb). Estes papéis e ações fazem parte do espaço de nome do `Microsoft.DocumentDB` Azure Ative Directory.

1. Em primeiro lugar, crie um documento JSON nomeado `AzureCosmosKeyOnlyAccess.json` com o seguinte conteúdo:

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. Executar os seguintes comandos para criar a atribuição de Função e atribuí-la ao utilizador:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Não permitir a execução de operações não-data

Os seguintes comandos removem a capacidade de utilizar as teclas para:
- criar, modificar ou eliminar recursos
- atualizar as definições dos contentores (incluindo políticas de indexação, produção, etc.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o controlo de acesso baseado em papéis da Cosmos DB](role-based-access-control.md)
- Obtenha uma visão geral do [acesso seguro aos dados em Cosmos DB](secure-access-to-data.md)
