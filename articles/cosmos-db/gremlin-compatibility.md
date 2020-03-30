---
title: Compatibilidade Azure Cosmos DB Gremlin com características do TinkerPop
description: Documentação de referência Problemas de compatibilidade do motor Gráfico
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 581bc813ca27067b1f27ab9866a45df3084dbbcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644737"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Compatibilidade Azure Cosmos DB Gremlin
O motor Azure Cosmos DB Graph segue de perto a especificação de passos de passagem [do Apache TinkerPop,](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) mas existem diferenças.

## <a name="behavior-differences"></a>Diferenças de comportamento

* O motor Azure Cosmos DB Graph funciona pela ***primeira*** vez, enquanto o TinkerPop Gremlin é o primeiro a ser de profundidade. Este comportamento consegue um melhor desempenho em sistema horizontalmente escalável como cosmos DB. 

## <a name="unsupported-features"></a>Características não suportadas

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** é uma especificação agnóstica de linguagem de programação para percursos em gráfico. Cosmos DB Graph ainda não o suporta. Use `GremlinClient.SubmitAsync()` e passe traversal como uma corda de texto.

* ***`property(set, 'xyz', 1)`*** definir cardinalidade não é apoiado hoje. Em vez disso, utilize `property(list, 'xyz', 1)`. Para saber mais, consulte [propriedades da Vertex com o TinkerPop.](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)

* ***`atch()`*** permite consultar gráficos usando a correspondência de padrão declarativo. Esta capacidade não está disponível.

* ***Objetos como propriedades*** em vértices ou bordas não são suportados. As propriedades apenas podem ser matrizes ou tipos primitivos.

* ***A triagem por propriedades*** `order().by(<array property>)` de matriz não é suportada. A ordenação é suportada apenas por tipos primitivos.

* ***Os tipos jSON não primitivos*** não são suportados. `string`Utilizar, `number`ou `true` / `false` tipos. `null`valores não são suportados. 

* O serializador ***GraphSONv3*** não é atualmente suportado. Utilize `GraphSONv2` aulas de Serializer, Reader e Writer na configuração de ligação.

* **Expressões e funções lambda** não são atualmente suportadas. Isto inclui `.map{<expression>}`as `.by{<expression>}` `.filter{<expression>}` funções. Para saber mais, e para aprender a reescrevê-los usando passos Gremlin, consulte [A Note on Lambdas](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***As transações*** não são suportadas devido à natureza distribuída do sistema.  Configure o modelo de consistência adequado na conta Gremlin para "ler as suas próprias escritas" e usar uma conmoeda otimista para resolver as escritas contraditórias.

## <a name="next-steps"></a>Passos seguintes
* Visite a página de voz do [utilizador cosmos DB](https://feedback.azure.com/forums/263030-azure-cosmos-db) para partilhar feedback e ajudar a equipa a focar-se em funcionalidades que são importantes para si.
