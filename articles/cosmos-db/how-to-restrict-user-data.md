---
title: Restringir o acesso dos utilizadores a operações de dados apenas com o Azure Cosmos DB
description: Saiba restringir o acesso a operações de dados apenas com o Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980377"
---
# <a name="restrict-user-access-to-data-operations-only"></a>Restringir o acesso do utilizador apenas às operações de dados

No Azure Cosmos DB, existem duas formas de autenticar as suas interações com o serviço de base de dados:
- utilizando a sua identidade azure Ative Directory ao interagir com o portal Azure,
- utilizando [chaves](secure-access-to-data.md#master-keys) db azure cosmos ou [fichas](secure-access-to-data.md#resource-tokens) de recursos ao emitir chamadas de APIs e SDKs.

Cada método de autenticação dá acesso a diferentes ![conjuntos de operações, com alguma sobreposição: Divisão de operações por tipo de autenticação](./media/how-to-restrict-user-data/operations.png)

Em alguns cenários, poderá querer restringir alguns utilizadores da sua organização a realizar operações de dados (ou seja, pedidos e consultas CRUD) apenas. Este é normalmente o caso dos desenvolvedores que não precisam de criar ou apagar recursos, ou alterar a entrada aprovisionada dos contentores em que estão a trabalhar.

Pode restringir o acesso aplicando os seguintes passos:
1. Criar uma função personalizada de Diretório Ativo Azure para os utilizadores a quem pretende restringir o acesso. O papel personalizado do Diretório Ativo deve ter um nível de acesso fino às operações utilizando [as ações granulares](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)da Azure Cosmos DB.
1. Autorizar a execução de operações não datadas com chaves. Pode conseguir isso limitando estas operações apenas às chamadas do Gestor de Recursos Azure.

As próximas secções deste artigo mostram como executar estes passos.

> [!NOTE]
> Para executar os comandos nas secções seguintes, é necessário instalar o Módulo PowerShell Azure 3.0.0 ou mais tarde, bem como o Papel proprietário do [Azure](../role-based-access-control/built-in-roles.md#owner) na subscrição que está a tentar modificar.

Nos scripts PowerShell nas secções seguintes, substitua os seguintes espaços reservados por valores específicos do seu ambiente:
- `$MySubscriptionId`- O ID de subscrição que contém a conta Azure Cosmos onde pretende limitar as permissões. Por exemplo: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName`- O grupo de recursos que contém a conta Azure Cosmos. Por exemplo: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName`- O nome da sua conta Azure Cosmos. Por exemplo: `mycosmosdbsaccount`.
- `$MyUserName`- O login dousername@domainutilizador para quem pretende limitar o acesso. Por exemplo: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>selecione a subscrição do Azure

Os comandos Azure PowerShell exigem que faça login e selecione a subscrição para executar os comandos:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Criar o papel personalizado do Diretório Ativo Azure

O seguinte guião cria uma atribuição de papel de Diretório Ativo Azure com acesso "Key Only" para contas Azure Cosmos. O papel baseia-se em [papéis personalizados para recursos Azure](../role-based-access-control/custom-roles.md) e [ações granular para O Azure Cosmos DB.](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb) Estas funções e ações `Microsoft.DocumentDB` fazem parte do espaço de nome do Diretório Ativo Azure.

1. Em primeiro lugar, crie `AzureCosmosKeyOnlyAccess.json` um documento JSON com o seguinte conteúdo:

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

1. Executar os seguintes comandos para criar a atribuição de funções e atribuí-la ao utilizador:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Proibir a execução de operações não datadas

Os seguintes comandos removem a capacidade de utilizar as teclas para:
- criar, modificar ou eliminar recursos
- atualizar as definições do contentor (incluindo políticas de indexação, entrada, etc.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [controlo de acesso baseado em papéis da Cosmos DB](role-based-access-control.md)
- Obtenha uma visão geral do [acesso seguro aos dados em Cosmos DB](secure-access-to-data.md)
