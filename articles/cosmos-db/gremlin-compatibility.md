---
title: Compatibilidade da Azure Cosmos DB Gremlin com características do TinkerPop
description: Documentação de referência Problemas de compatibilidade do motor do motor
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 7257246f618e3028534f3ebd60eaf6f94a3a4720
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092512"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Compatibilidade Azure Cosmos DB Gremlin
O motor Azure Cosmos DB Graph segue de perto a especificação de passos traversais [Apache TinkerPop,](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) mas existem diferenças na implementação específicas para Azure Cosmos DB. Para conhecer a lista de passos gremlin suportados, consulte o artigo de Suporte ao [Protocolo de Arame Gremlin API.](gremlin-support.md)

## <a name="behavior-differences"></a>Diferenças de comportamento

* O motor Azure Cosmos DB Graph ***funciona*** primeiro transversal enquanto o TinkerPop Gremlin é o primeiro de profundidade. Este comportamento consegue um melhor desempenho em sistema horizontalmente escalável como o Cosmos DB. 

## <a name="unsupported-features"></a>Funcionalidades não suportadas

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** é uma especificação agnóstica de linguagem de programação para percursos em gráfico. Cosmos DB Graph ainda não o suporta. Use `GremlinClient.SubmitAsync()` e passe transversalmente como uma cadeia de texto.

* ***`property(set, 'xyz', 1)`*** definir cardinalidade não é apoiado hoje. Em vez disso, utilize `property(list, 'xyz', 1)`. Para saber mais, consulte [as propriedades da Vertex com o TinkerPop.](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)

* O *** `match()` passo*** não está disponível. Este passo fornece capacidades de consulta declarativas.

* ***Os objetos como propriedades*** em vértices ou bordas não são suportados. As propriedades apenas podem ser matrizes ou tipos primitivos.

* ***Triagem por propriedades*** `order().by(<array property>)` de matriz não é apoiado. A ordenação é suportada apenas por tipos primitivos.

* ***Os tipos json não primitivos*** não são suportados. Utilizar, `string` `number` ou `true` / `false` tipos. `null` os valores não são suportados. 

* O serializer ***GraphSONv3*** não está suportado atualmente. Utilize `GraphSONv2` aulas de Serializer, Reader e Writer na configuração de ligação. Os resultados devolvidos pela API AZURE Cosmos DB Gremlin não têm o mesmo formato que o formato GraphSON. 

* **As expressões e funções lambda** não são suportadas atualmente. Isto inclui o `.map{<expression>}` , o , e as `.by{<expression>}` `.filter{<expression>}` funções. Para saber mais, e para aprender a reescrevê-los usando os passos de Gremlin, consulte [Uma Nota sobre Lambdas.](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)

* ***As transações*** não são suportadas por causa da natureza distribuída do sistema.  Configure o modelo de consistência adequado na conta gremlin para "ler os seus próprios escritos" e usar a concordância otimista para resolver escritos contraditórios.

## <a name="known-limitations"></a>Limitações conhecidas

* **Utilização de índices para consultas gremlin com `.V()` passos intermédios**: Atualmente, apenas a primeira `.V()` chamada de um traversal fará uso do índice para resolver quaisquer filtros ou predicados ligados ao mesmo. As chamadas subsequentes não consultarão o índice, o que poderá aumentar a latência e o custo da consulta.
    
    Assumindo a indexação padrão, uma consulta típica de Gremlin que começa com o `.V()` passo usaria parâmetros nos seus passos de filtragem anexados, tais como `.has()` ou para `.where()` otimizar o custo e desempenho da consulta. Por exemplo:

    ```java
    g.V().has('category', 'A')
    ```

    No entanto, quando mais de um `.V()` passo é incluído na consulta gremlin, a resolução dos dados para a consulta pode não ser a melhor. Tome como exemplo a seguinte consulta:

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    Esta consulta devolverá dois grupos de vértices com base na sua propriedade chamada `category` . Neste caso, apenas a primeira chamada, `g.V().has('category', 'A')` fará uso do índice para resolver os vértices com base nos valores das suas propriedades.

    Uma solução alternativa para esta consulta é utilizar passos subtraversais como `.map()` e `union()` . Isto é exemplificado abaixo:

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    Pode rever o desempenho das consultas utilizando o [ `executionProfile()` passo Gremlin](graph-execution-profile.md).

## <a name="next-steps"></a>Passos seguintes
* Visite a página [de voz do utilizador da Cosmos DB](https://feedback.azure.com/forums/263030-azure-cosmos-db) para partilhar feedback e ajudar a equipa a focar-se em funcionalidades importantes para si.
