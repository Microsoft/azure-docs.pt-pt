---
title: Sintaxe de pesquisa JSON - API de conhecimento académico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre a sintaxe de pesquisa JSON, que pode utilizar a API de conhecimento académico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: fddd2291fe7fbb46c57d31e9aebc7fc6244df971
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61336971"
---
# <a name="json-search-syntax"></a>Sintaxe de Pesquisa JSON

```javascript
/* Query Object:
   Suppose we have a query path /v0/e0/v1/e1/...
   A query object contains constraints to be applied to graph nodes in a path.
   Constraints are given in <"node identifier", {constraint object}> key-value pairs: 
*/
{
    /* query path can also be set in the query object */
    path: "/v0/e0/v1/e1/.../vn/",
    v0: { /* A Constraint Object */ },
    v1: { /* A Constraint Object */ },
}
```

Os nomes de nó num caminho de consulta (_v0 ', ' v1,..._ ) servem como identificadores de nó que podem ser referenciados no objeto de consulta; os nomes de borda (_e0, e1,..._ ) no caminho representam os tipos das margens correspondentes. Podemos usar um asterisco _*_ como um nome de nó ou de extremidade (exceto para o nó inicial, que tem de obter) para declarar que existem não existem restrições sobre esse elemento. Por exemplo, um caminho de consulta `/v0/*/v1/e1/*/` obtém os caminhos do gráfico sem restringir o tipo de limite _(v0, v1)_ . Enquanto isso, a consulta não tem restrições no destino (o último nó) do caminho seja.

Quando um caminho contém apenas um nó, digamos _v0_, a consulta simplesmente retornará todas as entidades que satisfazem as restrições. Um objeto de restrição aplicadas para o nó inicial é chamado um *a partir de objeto de consulta*, cuja especificação é dado da seguinte forma.

```javascript
/* Starting Query Object:
   Specifies constraints on the starting node
*/
{
    /* "match" operator searches for entities with the specified properties. 
       All properties defined in the graph schema be queried such as "Name" and "NormalizedTitle".
     */
    "match": { 
        "Name" : "some name",
        ...
    },
    /* "id" operator directly specifies the IDs of the starting nodes. 
       When it is present, the "match" operator is ignored. 
     */
    "id": [ id1, id2, id3... ],
    /* "type" operator limits results to a certain type of entities,
       for example, "Author" or "Paper".
     */
    "type": "Author",
    /* "select" operator pulls properties from the database and 
       returns them to the client.
     */
    "select": ["PaperRank", ...]
}
```

Quando um caminho contém mais do que apenas um nó inicial, o processador de consultas executará uma passagem de gráfico seguindo o padrão do caminho especificado. Quando ele chega a um nó, as ações especificadas pelo utilizador transversal serão acionadas, ou seja, se pretende parar no nó atual e retornar ou continuar a explorar o gráfico. Quando não é especificada nenhuma ação de passagem, serão conduzidas ações padrão. Para um nó intermediário, a ação predefinida é continuar a explorar o gráfico. Para o último nó de um caminho, a ação predefinida é de parar e retornar. Um objeto de restrição que especifica as ações de passagem é chamado um *objeto de ação de transversal*. A especificação é dado da seguinte forma:

```javascript
/* Traversal Action Object:
   Specifies graph traversal actions on a node (except for the starting node).
 */
{
    /* Set the continue condition here. */
    continue: { 
        /* simple property exact match */
        "property_key" : "property_value", 
        /* Advanced query operators */
        /* -- Numerical comparisons */
        "property_key_2" : { "gt" /* or simply ">" */ : 123 },
        /* -- Substring query */
        "property_key_3" : { "substring" : "456" },
        /* -- CellID query */
        "id": [ 111, 222, 333... ],
        /* -- Entity type query */
        "type": "cell_type",
        /* -- Property existance query */
        "has" : "property_key_4",
        /* -- Logical operators */
        /* ---- Note that, by default the operators are combined with AND semantics */
        
        /* -- OR operator */
        "or": {
          /* Query operators... */
        },
        /* -- NOT operator */
        "not": {
            /* Query operators... */
        }
    },
    /* Set the return condition here. */
    return: {
        /* Same operators as the continue object */
    },
    /* The selected properties to return. */
    select: ["property_key_1", ...]
}
```

O corpo da POSTAGEM de um *json* consulta de pesquisa deve conter, pelo menos, uma *caminho* padrão. Objetos de ação de completas são opcionais. Aqui estão dois exemplos.

```JSON
{
  "path": "/series/ConferenceInstanceIDs/conference/FieldOfStudyIDs/field",
  "series": {
    "type": "ConferenceSeries",
    "FullName": "graph",
    "select": [ "FullName", "ShortName" ]
  },
  "conference": {
    "type": "ConferenceInstance",
    "select": [ "FullName" ]
  },
  "field": {
    "type": "FieldOfStudy",
    "select": [ "Name" ],
    "return": { "Name": { "substring" : "World Wide Web" } }
  }
}
```

```JSON
{
  "path": "/author/PaperIDs/paper",
  "author": {
    "type": "Author",
    "select": [ "DisplayAuthorName" ],
    "match": { "Name": "leslie lamport" }
  },
  "paper": {
    "type": "Paper",
    "select": [ "OriginalTitle", "CitationCount" ],
    "return": { "CitationCount": { "gt": 100 } }
  }
}
```

