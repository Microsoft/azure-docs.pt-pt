---
title: Limites de Azure Cosmos DB Gremlin
description: Documentação de referência para limitações de tempo de execução do motor Graph
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: sngun
ms.openlocfilehash: 4e638fdff67ad2d0bc6f191cdfd46867ab847923
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93080126"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Limites do Gremlin do Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Este artigo fala sobre os limites do motor Azure Cosmos DB Gremlin e explica como podem ter impacto nas travessias dos clientes.

Cosmos DB Gremlin é construído em cima da infraestrutura cosmos DB. Devido a isso, todos os limites explicados nos [limites de serviço DB da Azure Cosmos](./concepts-limits.md) ainda se aplicam.

## <a name="limits"></a>Limites

Quando o limite de Gremlin é atingido, o traversal é cancelado com um **código x-ms-status de** 429 indicando um erro de estrangulamento. Consulte os [cabeçalhos de resposta do servidor Gremlin](gremlin-limits.md) para obter mais informações.

**Recurso**    | **Limite predefinido** | **Explicação**
--- | --- | ---
*Comprimento do script* | **64 KB** | Comprimento máximo de um script do percurso do Gremlin por pedido.
*Profundidade do operador* | **400** |  Número total de passos exclusivos num transversal. Por exemplo, ```g.V().out()``` tem uma contagem de operador de 2: V() e fora(), tem profundidade do operador de ```g.V('label').repeat(out()).times(100)``` 3: V(), repetição() e out() porque ```.times(100)``` é um parâmetro para o ```.repeat()``` operador.
*Grau de paralelismo* | **32** | Número máximo de partições de armazenamento consultadas num único pedido à camada de armazenamento. Os gráficos com centenas de divisórias serão impactados por este limite.
*Limite de repetição* | **32** | Número máximo de iterações que um operador ```.repeat()``` pode executar. Cada iteração de passo na maioria dos ```.repeat()``` casos corre a primeira travessia, o que significa que qualquer travessia é limitada a no máximo 32 lúpulo entre vértices.
*Tempo limite de tempo transversal* | **30 segundos** | Traversal será cancelado quando exceder desta vez. O Grafo do Cosmos DB é uma base de dados OLTP com a grande maioria dos percursos a ser concluída em milissegundos. Para executar consultas OLAP no Cosmos DB Graph, utilize [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) com [Quadros de Dados de Gráficos](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) e [Conector de Faíscas Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark).
*Tempo limite da ligação inativa* | **Uma hora** | O tempo que o serviço Gremlin manterá as ligações websocket abertas. Os pacotes de manter vivos da TCP ou os pedidos HTTP Keep-alive não prolongam o tempo de vida da ligação para além deste limite. O motor Cosmos DB Graph considera que as ligações websocket estão inativas se não houver pedidos ativos da Gremlin.
*Token de recursos por hora* | **100** | Número de tokens de recursos exclusivos utilizados pelos clientes do Gremlin para se ligarem à conta Gremlin numa região. Quando a aplicação exceder o limite de ficha único de hora em hora, `"Exceeded allowed resource token limit of 100 that can be used concurrently"` será devolvida no próximo pedido de autenticação.

## <a name="next-steps"></a>Passos seguintes
* [Cabeçalhos de resposta Azure Cosmos DB Gremlin](gremlin-headers.md)
* [Tokens de recursos DB da Azure Cosmos com Gremlin](how-to-use-resource-tokens-gremlin.md)