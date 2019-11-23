---
title: Azure Cosmos DB a compatibilidade do Gremlin com os recursos do TinkerPop
description: Documentação de referência problemas de compatibilidade do mecanismo grafo
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 0ed5824859b8463919a809861993f9f98a4f9251
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327035"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB compatibilidade com o Gremlin
Azure Cosmos DB mecanismo de grafo segue a especificação de etapas de passagem do [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , mas há diferenças.

## <a name="behavior-differences"></a>Diferenças de comportamento

* Azure Cosmos DB mecanismo de grafo executa uma passagem ***de primeira amplitude*** enquanto TinkerPop Gremlin é de profundidade. Esse comportamento atinge um desempenho melhor em sistema horizontalmente escalonável como Cosmos DB. 

## <a name="unsupported-features"></a>Recursos sem suporte

* ***[Gremlin Bytecode](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** é uma especificação agnóstica de linguagem de programação para percursos em gráfico. Cosmos DB grafo ainda não dá suporte a ele. Use ```GremlinClient.SubmitAsync()``` e passe passagem como uma cadeia de texto.

* a cardinalidade do conjunto de ***```property(set, 'xyz', 1)```*** não tem suporte hoje. Em vez disso, use ```property(list, 'xyz', 1)```.

* ***```match()```*** permite consultar grafos usando correspondência de padrão declarativa. Essa funcionalidade não está disponível.

* Não há suporte para ***objetos como propriedades*** em vértices ou bordas. As propriedades apenas podem ser matrizes ou tipos primitivos.

* Não há suporte para ***a classificação por propriedades de matriz*** ```.order().by(<array property>)```. A ordenação é suportada apenas por tipos primitivos.

* Não há suporte para ***tipos JSON não primitivos*** . Use ```string```tipos de ```false``` /, ```number```ou ```true```. Não há suporte para valores de ```null```. 

* O serializador ***GraphSONv3*** não está disponível hoje.

* ***As transações*** não têm suporte devido à natureza distribuída do sistema.  Configure o modelo de consistência apropriado na conta do Gremlin para "ler suas próprias gravações" e use a simultaneidade otimista para resolver as gravações conflitantes.

## <a name="next-steps"></a>Passos seguintes
* Visite [Cosmos DB](https://feedback.azure.com/forums/263030-azure-cosmos-db) página de voz de usuário para compartilhar comentários e ajudar a equipe a se concentrar em recursos que são importantes para você.
