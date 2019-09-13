---
title: Azure Cosmos DB a compatibilidade do Gremlin com os recursos do TinkerPop
description: Documentação de referência problemas de compatibilidade do mecanismo grafo
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: d58d1ea50251c3568161ce008db2672ff0fd6372
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910951"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB compatibilidade com o Gremlin
Azure Cosmos DB mecanismo de grafo segue a especificação de etapas de passagem do [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , mas há diferenças.

## <a name="behavior-differences"></a>Diferenças de comportamento

* Azure Cosmos DB mecanismo de grafo executa uma passagem ***de primeira amplitude*** enquanto TinkerPop Gremlin é de profundidade. Esse comportamento atinge um desempenho melhor em sistema horizontalmente escalonável como Cosmos DB. 

## <a name="unsupported-features"></a>Funcionalidades não suportadas

* ***[Gremlin Bytecode](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** é uma especificação agnóstica de linguagem de programação para percursos em gráfico. Cosmos DB grafo ainda não dá suporte a ele. Use ```GremlinClient.SubmitAsync()``` e passe passagem como uma cadeia de texto.

* ***```property(set, 'xyz', 1)```*** Não há suporte para definir a cardinalidade hoje. Use ```property(list, 'xyz', 1)``` em vez disso.

* ***```match()```*** permite consultar grafos usando correspondência de padrão declarativa. Essa funcionalidade não está disponível.

* Não há suporte para ***objetos como propriedades*** em vértices ou bordas. As propriedades apenas podem ser matrizes ou tipos primitivos.

* ***Classificando por propriedades de matriz*** ```.order().by(<array property>)``` não tem suporte. A ordenação é suportada apenas por tipos primitivos.

* Não há suporte para ***tipos JSON não primitivos*** . Use ```string```os ```number```tipos, ```true``` ou ./ ```false``` ```null```Não há suporte para valores. 

* O serializador ***GraphSONv3*** não está disponível hoje.

* ***As transações*** não têm suporte devido à natureza distribuída do sistema.  Configure o modelo de consistência apropriado na conta do Gremlin para "ler suas próprias gravações" e use a simultaneidade otimista para resolver as gravações conflitantes.

## <a name="next-steps"></a>Passos seguintes
* Visite [Cosmos DB](https://feedback.azure.com/forums/263030-azure-cosmos-db) página de voz de usuário para compartilhar comentários e ajudar a equipe a se concentrar em recursos que são importantes para você.
