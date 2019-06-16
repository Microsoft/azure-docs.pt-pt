---
title: Método CalcHistogram - API de serviço de exploração de conhecimento
titlesuffix: Azure Cognitive Services
description: Saiba como utilizar o método CalcHistogram no conhecimento exploração de serviço (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: aaa5b3a85c08f11d821557257de451b8ffc8a3fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814168"
---
# <a name="calchistogram-method"></a>calchistogram método
O *calchistogram* método computa os objetos que correspondem uma expressão de consulta estruturadas e calcula a distribuição de seus valores de atributo.

## <a name="request"></a>Pedir
`http://<host>/calchistogram?expr=<expr>[&options]` 

Name|Value|Descrição
----|-----|-----------
expr | Cadeia de texto | Expressão de consulta estruturadas que especifica as entidades de índice sobre o qual calcular histogramas.
Atributos | Cadeia de texto (predefinição = "") | Lista delimitada por vírgulas de atributo a ser incluído na resposta.
count   | Número (predefinição = 10) | Número de resultados a devolver.
offset  | Número (predefinição = 0) | Índice do primeiro resultado a devolver.

## <a name="response-json"></a>Resposta (JSON)
JSONPath | Descrição
----|----
$.expr | *expr* parâmetro no pedido.
$.num_entities | Número total de entidades correspondentes.
$.histograms |  Matriz de histogramas, um para cada atributo de pedido.
$.histograms[\*].attribute | Nome do atributo na qual o histograma foi computado.
$.histograms[\*].distinct_values | Número de valores distintos entre entidades para este atributo de correspondência.
$.histograms[\*].total_count | Número total de instâncias de valor entre entidades para este atributo de correspondência.
$.histograms[\*].histogram | Dados de histograma para este atributo.
$.histograms[\*].histogram[\*].value | Valor do atributo.
$.histograms[\*].histogram[\*].logprob  | Probabilidade de registo natural total de correspondência de entidades com este valor de atributo.
$.histograms[\*].histogram[\*].count    | Número de entidades correspondentes com este valor de atributo.
$.aborted | VERDADEIRO se o pedido excedeu o limite de tempo.

### <a name="example"></a>Exemplo
No exemplo publicações acadêmicas, o seguinte calcula um histograma da publicação contagens por ano e por palavra-chave para um determinado autor desde 2013:

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

A resposta indica que há 37 papers correspondentes a expressão de consulta.  Para o *ano* atributo, existem 3 valores distintos, um para cada ano desde 2013.  A contagem de papel total sobre os valores distintos 3 é 37.  Para cada *ano*, o histograma mostra o valor, a probabilidade de registo natural total e a contagem de correspondência de entidades.     

O histograma para *palavra-chave* mostra que existem 34 palavras-chave de distintas. Como um documento pode ser associado a vários de palavras-chave, a contagem total (53) pode ser maior que o número de entidades correspondentes.  Embora existam 34 valores distintos, a resposta inclui apenas a parte superior 4 devido a "contagem = 4" parâmetros.

```json
{
  "expr": "And(Composite(Author.Name=='jaime teevan'),Y>=2013)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -6.894,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -6.927,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -7.082,
          "count": 10
        }
      ]
    },
    {
      "attribute": "Keyword",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -7.142,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -7.389,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -7.623,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -7.674,
          "count": 2
        }
      ]
    }
  ]
}
``` 
