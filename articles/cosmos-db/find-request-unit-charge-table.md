---
title: Encontre a taxa da unidade de pedido (RU) para uma consulta de API de tabela em Azure Cosmos DB
description: Saiba como encontrar a taxa da unidade de pedido (RU) para consultas de API de tabela executadas contra um recipiente Azure Cosmos. Você pode usar o portal Azure, .NET, Java, Python e Node.js idiomas para encontrar a carga RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 0c3f2495630358f48900a9cf27cbc0feb4192e1d
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201317"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-table-api"></a>Encontre a taxa unitária de pedido para operações executadas na Azure Cosmos DB Table API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB suporta muitas APIs, tais como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem o seu próprio conjunto de operações de base de dados. Estas operações vão desde leituras simples de pontos e escritas a consultas complexas. Cada operação de base de dados consome recursos do sistema com base na complexidade da operação.

O custo de todas as operações de bases de dados é regulado pelo Azure Cosmos DB e é expresso por Unidades de Pedido (ou RUs, abreviatura em inglês). A taxa de pedido é a unidade de pedido consumida por todas as suas operações de base de dados. Pode pensar em RUs como uma moeda de desempenho que abstrai os recursos do sistema, tais como CPU, IOPS e memória que são necessárias para executar as operações de base de dados suportadas pela Azure Cosmos DB. Não interessa que API utiliza para interagir com o contentor do Azure Cosmos: os custos são sempre medidos por RUs. Quer a operação da base de dados seja uma escrita, leitura de ponto ou consulta, os custos são sempre medidos em RUs. Para saber mais, consulte as unidades de pedido e é artigo [de considerações.](request-units.md)

Este artigo apresenta as diferentes formas de encontrar o consumo da [unidade de pedido](request-units.md) (RU) para qualquer operação executada contra um contentor na Azure Cosmos DB Table API. Se estiver a utilizar uma API diferente, consulte [a API para MongoDB,](find-request-unit-charge-mongodb.md) [Cassandra API,](find-request-unit-charge-cassandra.md) [Gremlin API](find-request-unit-charge-gremlin.md)e [artigos da SQL API](find-request-unit-charge.md) para encontrar a taxa RU/s.

## <a name="use-the-net-sdk"></a>Utilizar o .NET SDK

Atualmente, o único SDK que devolve a taxa RU para operações de mesa é o [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). O `TableResult` objeto expõe uma propriedade que é `RequestCharge` povoada pelo SDK quando a utiliza contra a API da Tabela DB AZure Cosmos:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Para obter mais informações, consulte [Quickstart: Construa uma aplicação API de tabela utilizando o DB .NET SDK e Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Passos seguintes

Para aprender a otimizar o seu consumo de RU, consulte estes artigos:

* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
* [Otimizar o débito aprovisionado no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo das consultas no Azure Cosmos DB](./optimize-cost-reads-writes.md)