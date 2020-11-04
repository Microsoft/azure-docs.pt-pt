---
title: Produção de base de dados de provisão em Azure Cosmos DB SQL API
description: Saiba como obter a produção a nível de base de dados no Azure Cosmos DB SQL API utilizando o portal Azure, CLI, PowerShell e vários outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4ecbee2260da735cd6ba74d3b9ffb55b4a652e9e
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342004"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db---sql-api"></a>Produção padrão de provisão (manual) numa base de dados em Azure Cosmos DB - SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo explica como providenciar a produção padrão (manual) numa base de dados em Azure Cosmos DB SQL API. Pode aprovisionar débito para um único [contentor](how-to-provision-container-throughput.md) ou para uma base de dados e partilhá-la entre os contentores na mesma. Para saber quando utilizar o nível de contentor e o nível de base de dados, consulte os [casos de utilização para o fornecimento de produção em contentores e artigos de bases de dados.](set-throughput.md) Pode prever a produção de nível de base de dados utilizando o portal Azure ou O Azure Cosmos DB SDKs.

Se estiver a utilizar uma API diferente, consulte [a API para a MongoDB,](how-to-provision-throughput-mongodb.md) [Cassandra API,](how-to-provision-throughput-cassandra.md) [artigos da API gremlin](how-to-provision-throughput-gremlin.md) para prever o rendimento.

## <a name="provision-throughput-using-azure-portal"></a>Aprovisionar débito com o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-sql-api-dotnet.md#create-account)ou selecione uma conta Azure Cosmos existente.

1. Abra o painel **do Data Explorer** e selecione New **Database**. Fornecer os seguintes detalhes:

   * Introduza uma identificação de base de dados.
   * Selecione a opção **de saída da base de dados De provisionar.**
   * Introduza uma produção (por exemplo, 1000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-sql-api.png" alt-text="Screenshot da caixa de diálogo new database":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Produção de provisão utilizando O Azure CLI ou PowerShell

Para criar uma base de dados com produção partilhada ver,

* [Criar uma base de dados utilizando o Azure CLI](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Criar uma base de dados utilizando o PowerShell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Aprovisionar débito com o SDK do .NET

> [!Note]
> Você pode usar Azure Cosmos SDKs para API SQL para provisão para todos os APIs. Pode utilizar opcionalmente o seguinte exemplo para a API cassandra também.

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para saber sobre a produção prevista na Azure Cosmos DB:

* [Dimensionamento global de débito aprovisionado](./request-units.md)
* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md)
* [Como prever a produção padrão (manual) para um recipiente](how-to-provision-container-throughput.md)
* [Como providenciar a produção de autoescala para um contentor](how-to-provision-autoscale-throughput.md)
* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)