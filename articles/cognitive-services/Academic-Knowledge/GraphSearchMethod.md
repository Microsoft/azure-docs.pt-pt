---
title: Criar um gráfico de método Search - API de conhecimento académico
titlesuffix: Azure Cognitive Services
description: Utilize o método de pesquisa de gráficos a API de conhecimento académico para retornar um conjunto de entidades académicas com base nos padrões de gráfico específicos.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 5d47b938560fb1bd15adfe1a1c2d35b7359d47a3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57977416"
---
# <a name="graph-search-method"></a>Método de pesquisa do gráfico

O **pesquisa de grafo** REST API é utilizada para devolver um conjunto de entidades académicas com base nos padrões de gráfico fornecido.  A resposta é um conjunto de caminhos de gráfico que satisfaça as restrições de utilizador especificado. Um caminho gráfico é uma sequência intercalada de nós de gráfico e limites na forma de _v0 ', ' e0, v1, e1,..., vn_, onde _v0_ é o nó inicial do caminho.
<br>

**Ponto final REST:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?
```   
<br>

## <a name="request-parameters"></a>Parâmetros do Pedido  

Name     | Value | Necessário?  | Descrição
-----------|-----------|---------|--------
**mode**       | Cadeia de texto | Sim | Nome do modo que deseja usar. O valor é *json* ou *lambda*.

O método de pesquisa de gráfico tem de ser chamado por meio de um pedido HTTP POST. O pedido post deve incluir o cabeçalho de tipo de conteúdo: **application/json**.

##### <a name="json-search"></a>Pesquisa JSON 

Para o *json* pesquisa, o corpo da mensagem é um objeto JSON. O objeto JSON descreve um padrão de caminho com restrições de utilizador especificado (consulte a [especificação do objeto JSON](JSONSearchSyntax.md) para *json* pesquisa).


##### <a name="lambda-search"></a>Pesquisa lambda

Para o *lambda* pesquisa, o corpo da mensagem é uma cadeia de texto sem formatação. O corpo de mensagem é uma cadeia de consulta do LIKQ lambda, que é uma única instrução c# (consulte a [especificação de cadeia de consulta](LambdaSearchSyntax.md) para *lambda* pesquisa). 

<br>

## <a name="response-json"></a>Resposta (JSON)

Name | Descrição
-------|-----   
**Resultados** | Uma matriz de 0 ou mais entidades que correspondem à expressão de consulta. Cada entidade contém os valores de atributos solicitados. Este campo é apresentado se o pedido foi processado com êxito.
**error** | Códigos de estado HTTP. Este campo está presente se o pedido falhar.
**message** | Mensagem de erro. Este campo está presente se o pedido falhar.

Se uma consulta não é possível processar dentro _800 ms_, uma _tempo limite_ vai ser devolvido o erro. 

<br>

#### <a name="example"></a>Exemplo:

##### <a name="json-search"></a>Pesquisa JSON
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=json
```
<br>
Para o *json* pesquisar, se desejado papers cujos títulos contenham "Criar um gráfico de um mecanismo de" e escrito por "bin shao", podemos especificar a consulta da seguinte forma.

```JSON
{
  "path": "/paper/AuthorIDs/author",
  "paper": {
    "type": "Paper",
    "NormalizedTitle": "graph engine",
    "select": [
      "OriginalTitle"
    ]
  },
  "author": {
    "return": {
      "type": "Author",
      "Name": "bin shao"
    }
  }
}
```

A saída de uma consulta é uma matriz de caminhos de gráfico. Um caminho gráfico é uma matriz de objetos de nó correspondente para os nós especificado no caminho de consulta. Estes objetos de nó têm pelo menos uma propriedade *CellID*, que representa o ID de entidade. Outras propriedades podem ser obtidas ao especificar os nomes de propriedade através do operador select de um [ *objeto de ação de transversal*](JSONSearchSyntax.md).

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668,
        "OriginalTitle": "Trinity: a distributed graph engine on a memory cloud"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2171539317,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2411554868,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 73304046,
        "OriginalTitle": "The Trinity graph engine"
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
 ```

##### <a name="lambda-search"></a>Pesquisa lambda 

```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=lambda
```
<br>
Para o *lambda* pesquisa, se quisermos obter o autor IDs de um determinado documento, podemos escrever uma consulta semelhante ao seguinte.

```
MAG.StartFrom(@"{
    type  : ""Paper"",
    match : {
        NormalizedTitle : ""trinity: a distributed graph engine on a memory cloud""
    }
}").FollowEdge("AuthorIDs").VisitNode(Action.Return)
```

A saída de um *lambda* consulta de pesquisa também é uma matriz de caminhos de gráfico:

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2142490828
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2116756368
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
```
 
## <a name="graph-schema"></a>Esquema do grafo

Esquema do grafo é útil para escrever consultas de pesquisa de gráficos. É mostrado na figura a seguir.

![Esquema do grafo académicos da Microsoft](./Images/AcademicGraphSchema.png)
