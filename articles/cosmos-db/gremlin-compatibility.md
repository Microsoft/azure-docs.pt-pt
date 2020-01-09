---
title: Azure Cosmos DB a compatibilidade do Gremlin com os recursos do TinkerPop
description: Documentação de referência problemas de compatibilidade do mecanismo grafo
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 581bc813ca27067b1f27ab9866a45df3084dbbcc
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644737"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB compatibilidade com o Gremlin
Azure Cosmos DB mecanismo de grafo segue a especificação de etapas de passagem do [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , mas há diferenças.

## <a name="behavior-differences"></a>Diferenças de comportamento

* Azure Cosmos DB mecanismo de grafo executa uma passagem ***de primeira amplitude*** enquanto TinkerPop Gremlin é de profundidade. Esse comportamento atinge um desempenho melhor em sistema horizontalmente escalonável como Cosmos DB. 

## <a name="unsupported-features"></a>Recursos sem suporte

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** é uma especificação agnóstica de linguagem de programação para percursos em gráfico. Cosmos DB grafo ainda não dá suporte a ele. Use `GremlinClient.SubmitAsync()` e passe passagem como uma cadeia de texto.

* a cardinalidade do conjunto de ***`property(set, 'xyz', 1)`*** não tem suporte hoje. Em vez disso, utilize `property(list, 'xyz', 1)`. Para saber mais, confira [Propriedades de vértice com TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** permite consultar grafos usando correspondência de padrão declarativa. Essa funcionalidade não está disponível.

* Não há suporte para ***objetos como propriedades*** em vértices ou bordas. As propriedades apenas podem ser matrizes ou tipos primitivos.

* Não há suporte para ***a classificação por propriedades de matriz*** `order().by(<array property>)`. A ordenação é suportada apenas por tipos primitivos.

* Não há suporte para ***tipos JSON não primitivos*** . Use `string`tipos de `false` /, `number`ou `true`. Não há suporte para valores de `null`. 

* Atualmente, o serializador ***GraphSONv3*** não tem suporte. Use `GraphSONv2` classes de serializador, leitor e gravador na configuração de conexão.

* Atualmente, não há suporte para **expressões lambda e funções** . Isso inclui as funções `.map{<expression>}`, `.by{<expression>}`e `.filter{<expression>}`. Para saber mais e saber como reescrevê-los usando as etapas do Gremlin, consulte [uma observação sobre lambdas](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***As transações*** não têm suporte devido à natureza distribuída do sistema.  Configure o modelo de consistência apropriado na conta do Gremlin para "ler suas próprias gravações" e use a simultaneidade otimista para resolver as gravações conflitantes.

## <a name="next-steps"></a>Passos seguintes
* Visite [Cosmos DB](https://feedback.azure.com/forums/263030-azure-cosmos-db) página de voz de usuário para compartilhar comentários e ajudar a equipe a se concentrar em recursos que são importantes para você.
