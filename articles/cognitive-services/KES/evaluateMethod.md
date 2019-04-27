---
title: Avalie o método - API de serviço de exploração de conhecimento
titlesuffix: Azure Cognitive Services
description: Saiba como utilizar o método Evaluate no conhecimento exploração de serviço (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: dcfa9bb7931cf3b682bacf722b67acd6d4a370c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814100"
---
# <a name="evaluate-method"></a>Avalie o método

O *avaliar* método avalia e devolve o resultado de uma expressão de consulta estruturadas baseada nos dados de índice.

Normalmente, uma expressão será obtida a partir de uma resposta para o método de interpretação.  Mas também é possível compor expressões de consulta por conta própria (consulte [expressão de consulta estruturadas](Expressions.md)).  

## <a name="request"></a>Pedir 

`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

Name|Value|Descrição
----|----|----
expr       | Cadeia de texto | Expressão de consulta estruturadas que seleciona um subconjunto de entidades de índice.
Atributos | Cadeia de texto | Lista delimitada por vírgulas de atributos a serem incluídos na resposta.
count      | Número (predefinição = 10) | Número máximo de resultados a devolver.
offset     | Número (predefinição = 0) | Índice do primeiro resultado a devolver.
OrderBy |   Cadeia de texto | Nome do atributo utilizado para ordenar os resultados, seguidos por ordem de classificação opcional (predefinição = asc): "*attrname*[: (asc&#124;desc)]".  Se não for especificado, os resultados são devolvidos ao reduzir a probabilidade de registo natural.
tempo limite  | Número (predefinição = 1000) | Tempo limite em milissegundos. Apenas os resultados calculados antes do tempo limite decorreu são devolvidos.

Utilizar o *contagem* e *deslocamento* parâmetros, um grande número de resultados pode ser obtido a forma incremental através de várias solicitações.
  
## <a name="response-json"></a>Resposta (JSON)
JSONPath|Descrição
----|----
$.expr | *expr* parâmetro no pedido.
$.entities | Matriz de 0 ou mais entidades de objeto que correspondem a expressão de consulta estruturadas. 
$.aborted | VERDADEIRO se o pedido excedeu o limite de tempo.

Cada entidade contém um *logprob* valor e os valores dos atributos solicitados.

## <a name="example"></a>Exemplo
No exemplo publicações acadêmicas, da seguinte solicitação passa uma expressão de consulta estruturadas (potencialmente a partir da saída de um *interpretar* pedido) e obtém alguns atributos para os 2 principais entidades de correspondência:

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

A resposta contém os 2 principais ("contagem = 2") é muito provável que a correspondência entidades.  Para cada entidade, são devolvidos o título, ano, nome do autor e atributos de ID do autor.  Observe como a estrutura de composição de valores de atributo corresponde a forma como os que estão especificadas no ficheiro de dados. 

```json
{
  "expr": "Composite(Author.Name=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -6.645,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1968481722
        },
        {
          "Name": "susan t dumais",
          "Id": 676500258
        },
        {
          "Name": "eric horvitz",
          "Id": 1470530979
        }
      ]
    },
    {
      "logprob": -6.764,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1982462162
        },
        {
          "Name": "christine alvarado",
          "Id": 2163512453
        },
        {
          "Name": "mark s ackerman",
          "Id": 2055132526
        },
        {
          "Name": "david r karger",
          "Id": 2012534293
        }
      ]
    }
  ]
}
```
