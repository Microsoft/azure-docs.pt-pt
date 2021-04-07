---
title: Encontre taxa de unidade de pedido (RU) para consultas gremlin API em Azure Cosmos DB
description: Saiba como encontrar a taxa da unidade de pedido (RU) para consultas gremlin executadas contra um contentor Azure Cosmos. Pode utilizar o portal Azure, .NET, java drivers para encontrar a carga RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 295ed3eb2312a5f614a4214b5b627f5657fba1eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201348"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-gremlin-api"></a>Encontre a taxa unitária de pedido para operações executadas na Azure Cosmos DB Gremlin API
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB suporta muitas APIs, tais como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem o seu próprio conjunto de operações de base de dados. Estas operações vão desde leituras simples de pontos e escritas a consultas complexas. Cada operação de base de dados consome recursos do sistema com base na complexidade da operação.

O custo de todas as operações de bases de dados é regulado pelo Azure Cosmos DB e é expresso por Unidades de Pedido (ou RUs, abreviatura em inglês). A taxa de pedido é a unidade de pedido consumida por todas as suas operações de base de dados. Pode pensar em RUs como uma moeda de desempenho que abstrai os recursos do sistema, tais como CPU, IOPS e memória que são necessárias para executar as operações de base de dados suportadas pela Azure Cosmos DB. Não interessa que API utiliza para interagir com o contentor do Azure Cosmos: os custos são sempre medidos por RUs. Quer a operação da base de dados seja uma escrita, leitura de ponto ou consulta, os custos são sempre medidos em RUs. Para saber mais, consulte as unidades de pedido e é artigo [de considerações.](request-units.md)

Este artigo apresenta as diferentes formas de encontrar o consumo da [unidade de pedido](request-units.md) (RU) para qualquer operação executada contra um contentor em Azure Cosmos DB Gremlin API. Se estiver a utilizar uma API diferente, consulte [a API para MongoDB,](find-request-unit-charge-mongodb.md) [Cassandra API,](find-request-unit-charge-cassandra.md) [SQL API](find-request-unit-charge.md)e [artigos da Table API](find-request-unit-charge-table.md) para encontrar a taxa RU/s.

Os cabeçalhos devolvidos pela API gremlin são mapeados para atributos de estado personalizados, que atualmente são surgidos pela Gremlin .NET e Java SDK. A taxa de pedido está disponível na `x-ms-request-charge` chave. Quando utiliza a API Gremlin, tem várias opções para encontrar o consumo de RU para uma operação contra um contentor Azure Cosmos.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos](create-graph-gremlin-console.md#create-a-database-account) e alimente-a com dados ou selecione uma conta existente que já contenha dados.

1. Vá ao painel **do Data Explorer** e, em seguida, selecione o recipiente em que pretende trabalhar.

1. Introduza uma consulta válida e, em seguida, selecione **Executar a consulta de Gremlin**.

1. Selecione **Estatísticas de Consulta** para mostrar o custo de pedido real para o pedido que executou.

:::image type="content" source="./media/find-request-unit-charge/portal-gremlin-query.png" alt-text="Screenshot para obter uma taxa de pedido de consulta gremlin no portal Azure":::

## <a name="use-the-net-sdk-driver"></a>Utilize o controlador .NET SDK

Quando utilizar o [Gremlin.NET SDK,](https://www.nuget.org/packages/Gremlin.Net/)os atributos de estado estão disponíveis sob a `StatusAttributes` propriedade do `ResultSet<>` objeto:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Para obter mais informações, consulte [Quickstart: Construa uma aplicação .NET Framework ou Core utilizando uma conta API API AZure Cosmos DB Gremlin](create-graph-dotnet.md).

## <a name="use-the-java-sdk-driver"></a>Use o controlador Java SDK

Quando utilizar o [Gremlin Java SDK,](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)pode obter atributos de estado, chamando o `statusAttributes()` método do `ResultSet` objeto:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Para obter mais informações, consulte [Quickstart: Crie uma base de dados de gráficos em Azure Cosmos DB utilizando o Java SDK](create-graph-java.md).

## <a name="next-steps"></a>Passos seguintes

Para aprender a otimizar o seu consumo de RU, consulte estes artigos:

* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
* [Otimizar o débito aprovisionado no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo das consultas no Azure Cosmos DB](./optimize-cost-reads-writes.md)