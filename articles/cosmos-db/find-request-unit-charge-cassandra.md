---
title: Encontre a taxa da unidade de pedido (RU) para uma consulta da API de Cassandra em Azure Cosmos DB
description: Saiba como encontrar a taxa da unidade de pedido (RU) para consultas de Cassandra executadas contra um contentor Azure Cosmos. Pode utilizar o portal Azure, os controladores .NET e Java para encontrar a carga RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 38027f651623b16fed259cbf099bc274457723eb
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92482558"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-cassandra-api"></a>Encontre a taxa unitária de pedido para operações executadas na Azure Cosmos DB Cassandra API

Azure Cosmos DB suporta muitas APIs, tais como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem o seu próprio conjunto de operações de base de dados. Estas operações vão desde leituras simples de pontos e escritas a consultas complexas. Cada operação de base de dados consome recursos do sistema com base na complexidade da operação.

O custo de todas as operações de bases de dados é regulado pelo Azure Cosmos DB e é expresso por Unidades de Pedido (ou RUs, abreviatura em inglês). Pode pensar em RUs como uma moeda de desempenho que abstrai os recursos do sistema, tais como CPU, IOPS e memória que são necessárias para executar as operações de base de dados suportadas pela Azure Cosmos DB. Não interessa que API utiliza para interagir com o contentor do Azure Cosmos: os custos são sempre medidos por RUs. Quer a operação da base de dados seja uma escrita, leitura de ponto ou consulta, os custos são sempre medidos em RUs. Para saber mais, consulte as unidades de pedido e é artigo [de considerações.](request-units.md)

Este artigo apresenta as diferentes formas de encontrar o consumo da [unidade de pedido](request-units.md) (RU) para qualquer operação executada contra um contentor em Azure Cosmos DB Cassandra API. Se estiver a utilizar uma API diferente, consulte [a API para MongoDB,](find-request-unit-charge-mongodb.md) [SQL API,](find-request-unit-charge.md) [Gremlin API](find-request-unit-charge-gremlin.md)e [artigos da Table API](find-request-unit-charge-table.md) para encontrar a taxa RU/s.

Quando executa operações contra a Azure Cosmos DB Cassandra API, a carga RU é devolvida na carga útil recebida como um campo chamado `RequestCharge` . Tem várias opções para obter os custos das RUs.

## <a name="use-the-net-sdk"></a>Utilizar o .NET SDK

Quando utilizar o [.NET SDK,](https://www.nuget.org/packages/CassandraCSharpDriver/)pode recuperar a carga útil recebida sob a `Info` propriedade de um `RowSet` objeto:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Para obter mais informações, consulte [Quickstart: Construa uma aplicação Cassandra utilizando o .NET SDK e a Azure Cosmos DB](create-cassandra-dotnet.md).

## <a name="use-the-java-sdk"></a>Use o Java SDK

Quando utilizar o [SDK Java,](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)pode recuperar a carga útil de entrada, chamando o `getExecutionInfo()` método num `ResultSet` objeto:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Para obter mais informações, consulte [Quickstart: Construa uma aplicação Cassandra utilizando o Java SDK e o Azure Cosmos DB](create-cassandra-java.md).

## <a name="next-steps"></a>Passos seguintes

Para aprender a otimizar o seu consumo de RU, consulte estes artigos:

* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
* [Otimizar o débito aprovisionado no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo das consultas no Azure Cosmos DB](./optimize-cost-reads-writes.md)