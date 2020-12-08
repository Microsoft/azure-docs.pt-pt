---
title: Suporte e compatibilidade da Azure Cosmos DB Gremlin com características do TinkerPop
description: Saiba mais sobre a linguagem Gremlin no Apache TinkerPop. Saiba quais as funcionalidades e passos disponíveis no Azure Cosmos DB e nas diferenças de compatibilidade do motor TinkerPop Graph.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 11/11/2020
ms.author: sngun
ms.openlocfilehash: a149f0b331a77462aa53b948fedf25dd1331969e
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "94683629"
---
# <a name="azure-cosmos-db-gremlin-graph-support-and-compatibility-with-tinkerpop-features"></a>Suporte e compatibilidade de gráficos Azure Cosmos DB Gremlin com características do TinkerPop
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB suporta a linguagem transversal gráfica [de Apache Tinkerpop,](https://tinkerpop.apache.org) conhecida como [Gremlin.](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps) Pode utilizar a linguagem Gremlin para criar entidades de gráfico (vértices e limites), modificar propriedades nessas entidades, efetuar consultas e transversais e eliminar entidades.

O motor Azure Cosmos DB Graph segue de perto a especificação de passos traversais [Apache TinkerPop,](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) mas existem diferenças na implementação específicas para Azure Cosmos DB. Neste artigo, fornecemos uma rápida passagem de Gremlin e enumeramos as características de Gremlin que são apoiadas pela API de Gremlin.

## <a name="compatible-client-libraries"></a>Bibliotecas de clientes compatíveis

A seguinte tabela mostra controladores Gremlin populares que pode utilizar com o Azure Cosmos DB:

| Download | Origem | Introdução | Versão do conector suportado |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.4.6/reference/#gremlin-DotNet) | [Gremlin.NET no GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Criar Gráficos com .NET](create-graph-dotnet.md) | 3.4.6 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Documentação JavaDoc do Gremlin](https://tinkerpop.apache.org/javadocs/current/full/) | [Criar Gráficos com Java](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript no GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [Criar Gráficos com Node.js](create-graph-nodejs.md) | 3.3.4+ |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python no GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Criar Gráficos com Python](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP no GitHub](https://github.com/PommeVerte/gremlin-php) | [Criar Gráficos com PHP](create-graph-php.md) | 3.1.0 |
| [Go Lang](https://github.com/supplyon/gremcos/) | [Go Lang](https://github.com/supplyon/gremcos/) | | Esta biblioteca é construída por contribuintes externos. A equipa DB da Azure Cosmos não oferece qualquer apoio ou manter a biblioteca. |
| [Consola do Gremlin](https://tinkerpop.apache.org/downloads.html) | [Documentação do TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Criar Gráficos na Consola do Gremlin](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>Objetos de gráfico suportados

O TinkerPop é um padrão que abrange uma grande variedade de tecnologias de gráficos. Portanto, tem terminologia padrão para descrever as funcionalidades disponibilizadas pelo fornecedor de gráficos. O Azure Cosmos DB fornece uma base de dados de gráficos gravável, de alta simultaneidade e persistente que pode ser dividida em múltiplos servidores ou clusters. 

A tabela seguinte indica as funcionalidades do TinkerPop implementadas pelo Azure Cosmos DB: 

| Categoria | Implementação do Azure Cosmos DB |  Notas | 
| --- | --- | --- |
| Funcionalidades de gráficos | Fornece Persistência e Acesso em Simultâneo. Concebido para suportar Transações | Os métodos de computador podem ser implementados através do conector do Spark. |
| Funcionalidades de variável | Suporta Boolean, Integer, Byte, Double, Float, Integer, Long, String | Suporta tipos primitivos, é compatível com tipos complexos através do modelo de dados |
| Funcionalidades de vértice | Suporta RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty  | Suporta a criação, modificação e eliminação de vértices |
| Funcionalidades de propriedade de vértice | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Suporta a criação, modificação e eliminação de propriedades de vértice |
| Funcionalidades de limite | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Suporta a criação, modificação e eliminação de limites |
| Funcionalidades de propriedades de limites | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Suporta a criação, modificação e eliminação de propriedades de limites |

## <a name="gremlin-wire-format"></a>Formato de fio Gremlin

A Azure Cosmos DB utiliza o formato JSON ao devolver os resultados das operações da Gremlin. A Azure Cosmos DB suporta atualmente o formato JSON. Por exemplo, o seguinte corte mostra uma representação JSON de um vértice *devolvido ao cliente* da Azure Cosmos DB:

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

As propriedades utilizadas pelo formato JSON para vértices são descritas abaixo:

| Propriedade | Descrição | 
| --- | --- | --- |
| `id` | O ID do vértice. Deve ser único (em combinação com o valor de `_partition` se aplicável). Se não for fornecido qualquer valor, será automaticamente fornecido com um GUID | 
| `label` | A etiqueta do vértice. Esta propriedade é usada para descrever o tipo de entidade. |
| `type` | É utilizado para distinguir vértices de documentos que não são gráficos |
| `properties` | Conjunto de propriedades definidas pelo utilizador associadas ao vértice. Cada propriedade tem múltiplos valores. |
| `_partition` | A chave de partição do vértice. Utilizado para [a partição de gráficos.](graph-partitioning.md) |
| `outE` | Esta propriedade contém uma lista de bordas de um vértice. Armazenar a informação de contiguidade com o vértice permite uma execução rápida das transversais. Os limites são agrupados com base nas etiquetas. |

O limite contém a seguinte informação para ajudar com a navegação para outras partes do gráfico.

| Propriedade | Descrição |
| --- | --- |
| `id` | O ID do limite. Deve ser único (em combinação com o valor de `_partition` se aplicável) |
| `label` | A etiqueta do limite. Esta propriedade é opcional e é utilizada para descrever o tipo de relação. |
| `inV` | Esta propriedade contém uma lista de vértices para uma borda. Armazenar as informações de contiguidade com o limite permite uma execução rápida das transversais. Os vértices são agrupados com base nas etiquetas. |
| `properties` | Conjunto de propriedades definidas pelo utilizador associadas ao limite. Cada propriedade tem múltiplos valores. |

Cada propriedade pode armazenar múltiplos valores numa matriz. 

| Propriedade | Descrição |
| --- | --- |
| `value` | O valor da propriedade

## <a name="gremlin-steps"></a>Passos do Gremlin

Vamos observar os passos do Gremlin suportados pelo Azure Cosmos DB. Para obter referências completas do Gremlin, veja [Referências do TinkerPop](https://tinkerpop.apache.org/docs/3.3.2/reference).

| passo | Descrição | Documentação do TinkerPop 3.2 |
| --- | --- | --- |
| `addE` | Adiciona um limite entre dois vértices | [passo addE](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | Adiciona um vértice ao gráfico | [passo addV](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | Garante que todas as transversais devolvem um valor | [passo and](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | Um modulador de passos para atribuir uma variável ao resultado de um passo | [passo as](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | Um modulador de passos utilizado com `group` e `order` | [passo by](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | Devolve a primeira transversal que devolve um resultado | [passo coalesce](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | Devolve um valor constante. Utilizado com `coalesce`| [passo constant](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | Devolve a contagem da transversal | [passo count](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | Devolve os valores com os duplicados removidos | [passo dedup](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | Ignora os valores (vértice/limite) | [passo drop](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | Cria uma descrição de todas as operações geradas pelo passo gremlin executado | [execuçãoProfile passo](graph-execution-profile.md) |
| `fold` | Age como uma barreira que calcula a agregação de resultados| [passo fold](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Agrupa os valores com base nas etiquetas especificadas| [passo group](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Utilizado para filtrar propriedades, vértices e limites. Suporta variantes `hasLabel`, `hasId`, `hasNot` e `has`. | [passo has](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Insere valores numa transmissão| [passo inject](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Utilizado para efetuar um filtro com uma expressão booleana | [passo is](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | Utilizado para limitar o número de itens na transversal| [passo limit](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | Encapsula uma secção de uma transversal, da mesma forma que uma subconsulta | [passo local](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Utilizado para produzir a negação de um filtro | [passo not](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | Devolve o resultado da transversal especificada se gerar um resultado, caso contrário, devolve o elemento de chamada | [passo opcional](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | Garante que pelo menos uma das transversais devolve um valor | [passo or](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | Devolve resultados na sequência de ordenação especificada | [passo order](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | Devolve o caminho completo da transversal | [passo path](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | Projeta as propriedades como um Mapa | [passo project](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | Devolve as propriedades das etiquetas especificadas | [passo properties](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | Filtra o intervalo especificado de valores| [passo range](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | Repete o passo o número de vezes especificado. Utilizado para criar ciclos | [passo repeat](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | Utilizado para exemplificar resultados da transversal | [passo sample](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | Utilizado para projetar resultados da transversal |  [passo select](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | Utilizado para agregações que não sejam de bloqueio da transversal | [passo store](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | Função de filtragem de cordas. Esta função é usada como um predicado para o `has()` passo para combinar uma propriedade com o início de uma determinada corda | [Predicados TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Função de filtragem de cordas. Esta função é usada como um predicado para o `has()` passo para combinar uma propriedade com o final de uma determinada corda | [Predicados TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Função de filtragem de cordas. Esta função é usada como um predicado para o `has()` passo para combinar uma propriedade com o conteúdo de uma determinada cadeia | [Predicados TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Função de filtragem de cordas. Esta função é usada como um predicado para o `has()` passo para combinar com uma propriedade que não começa com uma determinada corda | [Predicados TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Função de filtragem de cordas. Esta função é usada como um predicado para o `has()` passo para combinar com uma propriedade que não termina com uma determinada corda | [Predicados TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Função de filtragem de cordas. Esta função é usada como um predicado para o `has()` passo para combinar com uma propriedade que não contém uma determinada corda | [Predicados TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Agrega caminhos de um vértice numa árvore | [passo tree](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Mostra um iterador como um passo| [passo unfold](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Intercala resultados de múltiplas transversais| [passo union](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | Inclui os passos necessários para transversais entre vértices e limites `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` e `otherV` | [passos vertex](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | Utilizado para filtrar resultados da transversal. Suporta os operadores `eq`, `neq`, `lt`, `lte`, `gt`, `gte` e `between`  | [passo where](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

O motor otimizado para escrita fornecido pelo Azure Cosmos DB suporta a indexação automática de todas as propriedades nos vértices e limites por predefinição. Portanto, as consultas com filtros, as consultas de intervalo, a ordenação ou as agregações em qualquer propriedade são processadas no índice e fornecidas de forma eficiente. Para obter mais informações sobre como a indexação funciona no Azure Cosmos DB, veja a nossa documentação sobre [indexação sem esquema](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="behavior-differences"></a>Diferenças de comportamento

* O motor Azure Cosmos DB Graph funciona ***primeira largura -** transversal enquanto TinkerPop Gremlin é o primeiro de profundidade. Este comportamento consegue um melhor desempenho em sistema horizontalmente escalável como o Cosmos DB.

## <a name="unsupported-features"></a>Funcionalidades não suportadas

_ ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)** _ é uma especificação agnóstica da linguagem de programação para os traversais de gráficos. Cosmos DB Graph ainda não o suporta. Use `GremlinClient.SubmitAsync()` e passe transversalmente como uma cadeia de texto.

_ **`property(set, 'xyz', 1)`** _ set cardinality is supportd today. Em vez disso, utilize `property(list, 'xyz', 1)`. Para saber mais, consulte [as propriedades da Vertex com o TinkerPop.](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)

_ O **`match()` passo*** não está disponível atualmente. Este passo fornece capacidades de consulta declarativas.

_ ***Os objetos como propriedades**- em vértices ou bordas não são suportados. As propriedades apenas podem ser matrizes ou tipos primitivos.

_ ***Ordenar por propriedades de matriz** _ não é `order().by(<array property>)` suportado. A ordenação é suportada apenas por tipos primitivos.

_ ***Os tipos JSON não primitivos** não são suportados. Utilizar, `string` `number` ou `true` / `false` tipos. `null` os valores não são suportados. 

_ * O serializer **GraphSONv3** _ não está atualmente suportado. Utilize `GraphSONv2` aulas de Serializer, Reader e Writer na configuração de ligação. Os resultados devolvidos pela API AZURE Cosmos DB Gremlin não têm o mesmo formato que o formato GraphSON. 

_ **As expressões e funções lambda** não são suportadas atualmente. Isto inclui o `.map{<expression>}` , o , e as `.by{<expression>}` `.filter{<expression>}` funções. Para saber mais, e para aprender a reescrevê-los usando os passos de Gremlin, consulte [Uma Nota sobre Lambdas.](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)

* ***As transações** não são suportadas por causa da natureza distribuída do sistema.  Configure o modelo de consistência adequado na conta gremlin para "ler os seus próprios escritos" e usar a concordância otimista para resolver escritos contraditórios.

## <a name="known-limitations"></a>Limitações conhecidas

_ **Utilização de índice para consultas gremlin com `.V()` passos intermédios**: Atualmente, apenas a primeira `.V()` chamada de um traversal utilizará o índice para resolver quaisquer filtros ou predicados ligados ao mesmo. As chamadas subsequentes não consultarão o índice, o que poderá aumentar a latência e o custo da consulta.
    
    Assuming default indexing, a typical read Gremlin query that starts with the `.V()` step would use parameters in its attached filtering steps, such as `.has()` or `.where()` to optimize the cost and performance of the query. For example:

    ```java
    g.V().has('category', 'A')
    ```

    However, when more than one `.V()` step is included in the Gremlin query, the resolution of the data for the query might not be optimal. Take the following query as an example:

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    This query will return two groups of vertices based on their property called `category`. In this case, only the first call, `g.V().has('category', 'A')` will make use of the index to resolve the vertices based on the values of their properties.

    A workaround for this query is to use subtraversal steps such as `.map()` and `union()`. This is exemplified below:

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    You can review the performance of the queries by using the [Gremlin `executionProfile()` step](graph-execution-profile.md).

## <a name="next-steps"></a>Passos seguintes

* Comece a criar uma aplicação de gráficos [com os nossos SDKs](create-graph-dotnet.md) 
* Saiba mais sobre o [suporte de gráficos](graph-introduction.md) no Azure Cosmos DB
