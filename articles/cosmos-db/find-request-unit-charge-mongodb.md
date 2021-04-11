---
title: Encontre a taxa de unidade de pedido para a Azure Cosmos DB API para operações da MongoDB
description: Saiba como encontrar a taxa da unidade de pedido (RU) para consultas mongoDB executadas contra um contentor Azure Cosmos. Pode utilizar o portal Azure, MongoDB .NET, Java, Node.js condutores.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 6b2944c1d29849ea44b5afd878d5b0e030358cc5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801830"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>Encontre a taxa da unidade de pedido para operações executadas na Azure Cosmos DB API para a MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB suporta muitas APIs, tais como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem o seu próprio conjunto de operações de base de dados. Estas operações vão desde leituras simples de pontos e escritas a consultas complexas. Cada operação de base de dados consome recursos do sistema com base na complexidade da operação.

O custo de todas as operações de bases de dados é regulado pelo Azure Cosmos DB e é expresso por Unidades de Pedido (ou RUs, abreviatura em inglês). A taxa de pedido é a unidade de pedido consumida por todas as suas operações de base de dados. Pode pensar em RUs como uma moeda de desempenho que abstrai os recursos do sistema, tais como CPU, IOPS e memória que são necessárias para executar as operações de base de dados suportadas pela Azure Cosmos DB. Não interessa que API utiliza para interagir com o contentor do Azure Cosmos: os custos são sempre medidos por RUs. Quer a operação da base de dados seja uma escrita, leitura de ponto ou consulta, os custos são sempre medidos em RUs. Para saber mais, consulte as unidades de pedido e é artigo [de considerações.](request-units.md)

Este artigo apresenta as diferentes formas de encontrar o consumo da [unidade de pedido](request-units.md) (RU) para qualquer operação executada contra um contentor em Azure Cosmos DB API para a MongoDB. Se estiver a utilizar uma API diferente, consulte [a SQL API,](find-request-unit-charge.md) [a Cassandra API,](find-request-unit-charge-cassandra.md) [a Gremlin API](find-request-unit-charge-gremlin.md)e os artigos [da Table API](find-request-unit-charge-table.md) para encontrar a taxa RU/s.

A carga RU é exposta por um [comando de base de dados](https://docs.mongodb.com/manual/reference/command/) personalizado chamado `getLastRequestStatistics` . O comando devolve um documento que contém o nome da última operação executada, a sua carga de pedido e a sua duração. Se utilizar o AZure Cosmos DB API para o MongoDB, tem várias opções para recuperar a carga RU.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) e alimente-a com dados ou selecione uma conta existente que já contenha dados.

1. Vá ao painel **do Data Explorer** e, em seguida, selecione o recipiente em que pretende trabalhar.

1. Selecione o **...** ao lado do nome do recipiente e selecione **Nova Consulta**.

1. Introduza uma consulta válida e, em seguida, selecione **Executar Consulta**.

1. Selecione **Estatísticas de Consulta** para mostrar o custo de pedido real para o pedido que executou. Este editor de consulta permite-lhe executar e visualizar os custos da unidade de pedido apenas para predicados de consulta. Não é possível utilizar este editor para comandos de manipulação de dados, tais como inserções de declarações.

   :::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Screenshot de uma taxa de pedido de consulta mongoDB no portal Azure":::

1. Para obter taxas de pedido para comandos de manipulação de dados, executar o `getLastRequestStatistics` comando a partir de uma UI baseada em conchas como a concha de Mongo, Robo [3T,](mongodb-robomongo.md) [MongoDB Compass](mongodb-compass.md), ou uma extensão do Código VS com scripts de concha.

   `db.runCommand({getLastRequestStatistics: 1})`

## <a name="use-the-mongodb-net-driver"></a>Utilize o controlador MongoDB .NET

Quando utilizar o [controlador oficial MongoDB .NET,](https://docs.mongodb.com/ecosystem/drivers/csharp/)pode executar comandos ligando para o `RunCommand` método num `IMongoDatabase` objeto. Este método requer uma implementação da `Command<>` classe abstrata:

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Para obter mais informações, consulte [Quickstart: Construa uma aplicação web .NET utilizando um API AZure Cosmos DB para a MongoDB](create-mongodb-dotnet.md).

## <a name="use-the-mongodb-java-driver"></a>Use o motorista de Java MongoDB


Quando utilizar o [controlador oficial MongoDB Java,](https://mongodb.github.io/mongo-java-driver/)pode executar comandos chamando o `runCommand` método num `MongoDatabase` objeto:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Para obter mais informações, consulte [Quickstart: Construa uma aplicação web utilizando o Azure Cosmos DB API para a MongoDB e o Java SDK](create-mongodb-java.md).

## <a name="use-the-mongodb-nodejs-driver"></a>Use o motorista de Node.js MongoDB

Quando utilizar o [controlador oficial de Node.js MongoDB,](https://mongodb.github.io/node-mongodb-native/)pode executar comandos chamando o método num `command` `db` objeto:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Para mais informações, consulte [Quickstart: Migrar uma aplicação web Node.js MongoDB existente para Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="next-steps"></a>Passos seguintes

Para aprender a otimizar o seu consumo de RU, consulte estes artigos:

* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
* [Otimizar o débito aprovisionado no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo das consultas no Azure Cosmos DB](./optimize-cost-reads-writes.md)