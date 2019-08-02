---
title: Método CalcHistogram – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Use o método CalcHistogram para calcular a distribuição de valores de atributo para um conjunto de entidades de papel.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 9e84b1ad37b3224ec5553d0a66ba0fc84bc88f55
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705070"
---
# <a name="calchistogram-method"></a>Método CalcHistogram

A API REST do **calchistogram** é usada para calcular a distribuição de valores de atributo para um conjunto de entidades de papel.          


**Ponto de extremidade REST:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>

## <a name="request-parameters"></a>Parâmetros do Pedido

Nome  |Value | Obrigatório?  |Descrição
-----------|----------|--------|----------
**expr**    |Cadeia de texto | Sim  |Uma expressão de consulta que especifica as entidades sobre as quais calcular os histogramas.
**deprecia** |Cadeia de texto | Não |Selecione o nome do modelo que você deseja consultar.  Atualmente, o valor padrão é o *mais recente*.
**atributos** | Cadeia de texto | Não<br>os | Uma lista delimitada por vírgula que especifica os valores de atributo que são incluídos na resposta. Os nomes de atributo diferenciam maiúsculas de minúsculas.
**count** |Número | Não<br>Predefinição: 10 |Número de resultados a serem retornados.
**offset**  |Número | Não<br>Predefinição: 0 |Índice do primeiro resultado a ser retornado.
**cedido**  |Número | Não<br>Predefinição: 1000 |Tempo limite em milissegundos. Somente as interpretações encontradas antes do tempo limite expirado são retornadas.

## <a name="response-json"></a>Resposta (JSON)

Nome | Descrição
--------|---------
**expr**  |O parâmetro expr da solicitação.
**num_entities** | Número total de entidades correspondentes.
**histogramas** |  Uma matriz de histogramas, uma para cada atributo especificado na solicitação.
**histograms[x].attribute** | Nome do atributo sobre o qual o histograma foi computado.
**histograms[x].distinct_values** | Número de valores distintos entre entidades correspondentes para este atributo.
**histograms[x].total_count** | Número total de instâncias de valor entre entidades correspondentes para este atributo.
**histograms[x].histogram** | Dados de histograma para este atributo.
**histograms[x].histogram[y].value** |  Um valor para o atributo.
**histograms[x].histogram[y].logprob**  |Probabilidade de log natural total de entidades correspondentes com esse valor de atributo.
**histograms[x].histogram[y].count**  |Número de entidades correspondentes com este valor de atributo.
**anulada** | True se a solicitação atingir o tempo limite.


#### <a name="example"></a>Exemplo:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Neste exemplo, para gerar um histograma da contagem de publicações por ano para um autor específico desde 2010, podemos primeiro gerar a expressão de consulta usando a API de **interpretação** com a cadeia de caracteres de consulta: *papers por jaime teevan após 2012*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>A expressão na primeira interpretação que é retornada da API de interpretação é *e (composta (AA). AuN = = ' Jaime teevan '), Y > 2012)* .
<br>Esse valor de expressão é passado para a API **calchistogram** . O parâmetro *Attributes = Y, F. FN* indica que as distribuições de contagens de papel devem ser por ano e campo de estudo, por exemplo:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>A resposta a essa solicitação primeiro indica que há 37 documentos que correspondem à expressão de consulta.  Para o atributo *year* , há três valores distintos, um para cada ano após 2012 (ou seja, 2013, 2014 e 2015), conforme especificado na consulta.  A contagem total de papel sobre os 3 valores distintos é 37.  Para cada *ano*, o histograma mostra o valor, a probabilidade de log natural total e a contagem de entidades correspondentes.     

O histograma para o *campo de estudo* mostra que há 34 campos distintos de estudo. Como um papel pode ser associado a vários campos de estudo, a contagem total (53) pode ser maior do que o número de entidades correspondentes.  Embora haja 34 valores distintos, a resposta inclui apenas os 4 principais devido ao parâmetro *Count = 4* .

```JSON
{
  "expr": "And(Composite(AA.AuN=='jaime teevan'),Y>2012)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -15.753,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -15.805,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -16.035,
          "count": 10
        }
      ]
    },
    {
      "attribute": "F.FN",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -15.258,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -16.002,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -16.226,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -17.228,
          "count": 2
        }
      ]
    }
  ]
}
```
