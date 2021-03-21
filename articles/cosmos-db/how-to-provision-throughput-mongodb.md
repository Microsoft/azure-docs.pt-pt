---
title: Produção de provisão para Azure Cosmos DB API para recursos mongoDB
description: Saiba como providenciar contentores, bases de dados e produção de escala automática na Azure Cosmos DB API para recursos mongoDB. Utilizará o portal Azure, CLI, PowerShell e vários outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 25eaa4a66fb4a73f976edbd30e6f82015ce84f6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93086130"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-api-for-mongodb-resources"></a>Base de dados, contentores ou produção de escala automática na Azure Cosmos DB API para recursos mongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este artigo explica como provisão para a produção em Azure Cosmos DB API para MongoDB. Pode providenciar padrão (manual) ou produção de escala automática num contentor ou numa base de dados e partilhá-la entre os recipientes dentro da base de dados. Você pode provistuar produção usando o portal Azure CLI, ou Azure Cosmos DB SDKs.

Se estiver a utilizar uma API diferente, consulte [a SQL API,](how-to-provision-container-throughput.md) [Cassandra API,](how-to-provision-throughput-cassandra.md) [Artigos da Gremlin API](how-to-provision-throughput-gremlin.md) para prever o rendimento.

## <a name="azure-portal"></a><a id="portal-mongodb"></a> Portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-mongodb-dotnet.md#create-a-database-account)ou selecione uma conta Azure Cosmos existente.

1. Abra o painel **do Data Explorer** e selecione New **Collection**. Em seguida, forneça os seguintes detalhes:

   * Indique se está a criar uma nova base de dados ou a utilizar uma existente. Selecione a opção **de produção da base de dados De provisionar** se pretender obter a produção no nível da base de dados.
   * Introduza uma identificação de coleção.
   * Introduza um valor chave de partição (por exemplo, `/ItemID` ).
   * Introduza uma produção que pretende prever (por exemplo, 1000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-mongodb/provision-database-throughput-portal-mongodb-api.png" alt-text="Screenshot do Data Explorer, ao criar uma nova coleção com produção de nível de base de dados":::

> [!Note]
> Se estiver a abastecer a produção num recipiente numa conta Azure Cosmos configurada com o API API AZure Cosmos para o MongoDB, utilize `/myShardKey` para o caminho chave da partição.

## <a name="net-sdk"></a><a id="dotnet-mongodb"></a> .NET SDK

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Os modelos do Gestor de Recursos Azure podem ser usados para obter rendimento de escala automática na base de dados ou recursos de nível de contentor para todas as APIs DB de Azure Cosmos. Consulte [os modelos do Azure Resource Manager para amostras do Azure Cosmos.](templates-samples-mongodb.md)

## <a name="azure-cli"></a>CLI do Azure

O Azure CLI pode ser usado para provisões de viação de escala automática numa base de dados ou recursos de nível de contentor para todas as APIs Azure Cosmos DB. Para amostras consulte [amostras do Azure CLI para Azure Cosmos DB](cli-samples-mongodb.md).

## <a name="azure-powershell"></a>Azure PowerShell

O Azure PowerShell pode ser utilizado para a disponibilização de produção de escala automática numa base de dados ou recursos de nível de contentor para todas as APIs DB do Azure Cosmos. Para amostras consulte [as amostras Azure PowerShell para Azure Cosmos DB](powershell-samples-mongodb.md).

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para saber sobre o fornecimento de produção em Azure Cosmos DB:

* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)