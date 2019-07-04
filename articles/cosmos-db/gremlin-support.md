---
title: Suporte do Azure Cosmos DB Gremlin
description: Saiba mais sobre a linguagem Gremlin no Apache TinkerPop. Saiba que funcionalidades e passos estão disponíveis no Azure Cosmos DB
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: db263c1c7f0a8b87b315c5aa6da31336229c9643
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502724"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Suporte de gráficos do Gremlin do Azure Cosmos DB
Azure Cosmos DB suporta [Apache Tinkerpop](https://tinkerpop.apache.org) criar um gráfico de linguagem de passagem, conhecida como [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps). Pode utilizar a linguagem Gremlin para criar entidades de gráfico (vértices e limites), modificar propriedades nessas entidades, efetuar consultas e transversais e eliminar entidades. 

Neste artigo, iremos fornecer uma passo a passo rápido do Gremlin e enumerar os recursos de Gremlin que são suportados pela API do Gremlin.

## <a name="compatible-client-libraries"></a>Bibliotecas de cliente compatível

A seguinte tabela mostra controladores Gremlin populares que pode utilizar com o Azure Cosmos DB:

| Transferência | source | Introdução | Versão do conector suportado |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [Gremlin.NET no GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Criar Gráficos com .NET](create-graph-dotnet.md) | 3.4.0-RC2 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Documentação JavaDoc do Gremlin](https://tinkerpop.apache.org/javadocs/current/full/) | [Criar Gráficos com Java](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript no GitHub](https://github.com/jbmusso/gremlin-javascript) | [Criar Gráficos com Node.js](create-graph-nodejs.md) | 3.3.4+ |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python no GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Criar Gráficos com Python](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP no GitHub](https://github.com/PommeVerte/gremlin-php) | [Criar Gráficos com PHP](create-graph-php.md) | 3.1.0 |
| [Consola do Gremlin](https://tinkerpop.apache.org/downloads.html) | [Documentação do TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Criar Gráficos na Consola do Gremlin](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>Suporte a objetos de gráfico
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

## <a name="gremlin-wire-format-graphson"></a>Formato de conexão do gremlin: GraphSON

O Azure Cosmos DB utiliza o [formato GraphSON](https://tinkerpop.apache.org/docs/3.3.2/reference/#graphson-reader-writer) ao devolver resultados das operações do Gremlin. GraphSON é o formato padrão do Gremlin para representar vértices, limites e propriedades (propriedades com um valor e com múltiplos valores) com JSON. 

Por exemplo, o fragmento seguinte mostra uma representação GraphSON de um vértice *devolvido ao cliente* a partir do Azure Cosmos DB. 

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

As propriedades utilizadas por GraphSON para vértices são descritas abaixo:

| Propriedade | Descrição | 
| --- | --- | --- |
| `id` | O ID do vértice. Tem de ser exclusivo (em combinação com o valor de `_partition` se aplicável). Se não for fornecido nenhum valor, ele serão automaticamente fornecido com um GUID | 
| `label` | A etiqueta do vértice. Esta propriedade é utilizada para descrever o tipo de entidade. |
| `type` | É utilizado para distinguir vértices de documentos que não são gráficos |
| `properties` | Conjunto de propriedades definidas pelo utilizador associadas ao vértice. Cada propriedade tem múltiplos valores. |
| `_partition` | A chave de partição do vértice. Utilizado para [criação de partições de gráfico](graph-partitioning.md). |
| `outE` | Esta propriedade contém uma lista das margens de um vértice. Armazenar a informação de contiguidade com o vértice permite uma execução rápida das transversais. Os limites são agrupados com base nas etiquetas. |

O limite contém a seguinte informação para ajudar com a navegação para outras partes do gráfico.

| Propriedade | Descrição |
| --- | --- |
| `id` | O ID do limite. Tem de ser exclusivo (em combinação com o valor de `_partition` se aplicável) |
| `label` | A etiqueta do limite. Esta propriedade é opcional e é utilizada para descrever o tipo de relação. |
| `inV` | Esta propriedade contém uma lista de em vértices de um edge. Armazenar as informações de contiguidade com o limite permite uma execução rápida das transversais. Os vértices são agrupados com base nas etiquetas. |
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
| `executionProfile` | Cria uma descrição de todas as operações geradas pela etapa executada do Gremlin | [passo executionProfile](graph-execution-profile.md) |
| `fold` | Age como uma barreira que calcula a agregação de resultados| [passo fold](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Agrupa os valores com base nas etiquetas especificadas| [passo group](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Utilizado para filtrar propriedades, vértices e limites. Suporta variantes `hasLabel`, `hasId`, `hasNot` e `has`. | [passo has](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Insere valores numa transmissão| [passo inject](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Utilizado para efetuar um filtro com uma expressão booleana | [passo is](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | Utilizado para limitar o número de itens na transversal| [passo limit](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | Encapsula uma secção de uma transversal, da mesma forma que uma subconsulta | [passo local](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Utilizado para produzir a negação de um filtro | [passo not](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | Devolve o resultado da transversal especificada se gerar um resultado, caso contrário, devolve o elemento de chamada | [passo optional](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
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
| `TextP.startingWith(string)` | Função de filtragem de cadeia. Essa função é usada como um predicado para o `has()` passo para corresponder a uma propriedade com o início de uma determinada seqüência de caracteres | [Predicados de TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Função de filtragem de cadeia. Essa função é usada como um predicado para o `has()` passo para corresponder a uma propriedade com o fim de uma determinada seqüência de caracteres | [Predicados de TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Função de filtragem de cadeia. Essa função é usada como um predicado para o `has()` passo para corresponder a uma propriedade com o conteúdo de uma determinada seqüência de caracteres | [Predicados de TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Função de filtragem de cadeia. Essa função é usada como um predicado para o `has()` passo para corresponder a uma propriedade que não começa com uma determinada seqüência de caracteres | [Predicados de TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Função de filtragem de cadeia. Essa função é usada como um predicado para o `has()` passo para corresponder a uma propriedade que não termina com uma determinada seqüência de caracteres | [Predicados de TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Função de filtragem de cadeia. Essa função é usada como um predicado para o `has()` passo para corresponder a uma propriedade que não contém uma determinada seqüência de caracteres | [Predicados de TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Agrega caminhos de um vértice numa árvore | [passo tree](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Mostra um iterador como um passo| [passo unfold](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Intercala resultados de múltiplas transversais| [passo union](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | Inclui os passos necessários para transversais entre vértices e limites `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` e `otherV` | [passos vertex](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | Utilizado para filtrar resultados da transversal. Suporta os operadores `eq`, `neq`, `lt`, `lte`, `gt`, `gte` e `between`  | [passo where](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

O motor otimizado para escrita fornecido pelo Azure Cosmos DB suporta a indexação automática de todas as propriedades nos vértices e limites por predefinição. Portanto, as consultas com filtros, as consultas de intervalo, a ordenação ou as agregações em qualquer propriedade são processadas no índice e fornecidas de forma eficiente. Para obter mais informações sobre como a indexação funciona no Azure Cosmos DB, veja a nossa documentação sobre [indexação sem esquema](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="next-steps"></a>Passos Seguintes
* Comece a criar uma aplicação de gráficos [com os nossos SDKs](create-graph-dotnet.md) 
* Saiba mais sobre o [suporte de gráficos](graph-introduction.md) no Azure Cosmos DB
