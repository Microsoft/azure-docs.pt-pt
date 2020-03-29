---
title: Limites de Azure Cosmos DB Gremlin
description: Documentação de referência para limitações de tempo de execução do motor Graph
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72029842"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Limites do Gremlin do Azure Cosmos DB
Este artigo fala sobre os limites do motor Azure Cosmos DB Gremlin e explica como podem afetar os traversos dos clientes.

Cosmos DB Gremlin é construído no topo da infraestrutura Cosmos DB. Devido a isso, todos os limites explicados nos limites de [serviço do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) ainda se aplicam. 

## <a name="limits"></a>Limites

Quando o limite gremlin é atingido, o traversal é cancelado com um código de **estado x-ms** de 429 indicando um erro de estrangulamento. Consulte os cabeçalhos de resposta do [servidor Gremlin](gremlin-limits.md) para obter mais informações.

**Recurso**    | **Limite predefinido** | **Explicação**
--- | --- | ---
*Comprimento do script* | **64 KB** | Comprimento máximo de um script do percurso do Gremlin por pedido.
*Profundidade do operador* | **400** |  Número total de passos exclusivos num transversal. ```g.V().out()``` Por exemplo, tem uma contagem de 2: V() e ```g.V('label').repeat(out()).times(100)``` fora(), tem profundidade de acionador de 3: V(), repetição() e fora() porque ```.times(100)``` é um parâmetro para ```.repeat()``` o operador.
*Grau de paralelismo* | **32** | Número máximo de partições de armazenamento consultadas num único pedido à camada de armazenamento. Gráficos com centenas de divisórias serão impactados por este limite.
*Limite de repetição* | **32** | Número máximo de iterações que um operador ```.repeat()``` pode executar. Cada iteração de passo na maioria dos ```.repeat()``` casos corre pela primeira vez transversal, o que significa que qualquer traverso está limitado a, no máximo, 32 lúpulos entre vértices.
*Tempo de passagem* | **30 segundos** | Traversal será cancelado quando exceder desta vez. O Grafo do Cosmos DB é uma base de dados OLTP com a grande maioria dos percursos a ser concluída em milissegundos. Para executar consultas OLAP no Cosmos DB Graph, use [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) com Quadros de Dados [de Gráficoe](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) [Conector De Faísca Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark).
*Tempo limite da ligação inativa* | **Uma hora** | O serviço Gremlin manterá abertas as ligações de websocket sem marcha. Os pacotes de manutenção vivos da TCP ou os pedidos http-keep-alive não prolongam o tempo de vida de ligação para além deste limite. O motor Cosmos DB Graph considera que as ligações de websocket estão inativas se não houver pedidos ativos de Gremlin em funcionamento nele.
*Token de recursos por hora* | **100** | Número de tokens de recursos exclusivos utilizados pelos clientes do Gremlin para se ligarem à conta Gremlin numa região. Quando a aplicação exceder o limite `"Exceeded allowed resource token limit of 100 that can be used concurrently"` de fichas única de hora em hora, será devolvido no próximo pedido de autenticação.

## <a name="next-steps"></a>Passos seguintes
* [Cabeçalhos de resposta Azure Cosmos DB Gremlin](gremlin-headers.md) 
* [Bluee Cosmos DB Resource Tokens com Gremlin](how-to-use-resource-tokens-gremlin.md)
