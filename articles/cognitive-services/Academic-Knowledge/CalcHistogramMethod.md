---
title: Método CalcHistogram - API de conhecimento académico
titlesuffix: Azure Cognitive Services
description: Utilize o método CalcHistogram para calcular a distribuição dos valores de atributo para um conjunto de entidades do documento.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a228c5b90e47c9c24c5da70484a1a28f9a3054b1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58100484"
---
# <a name="calchistogram-method"></a>Método CalcHistogram

O **calchistogram** REST API é utilizada para calcular a distribuição dos valores de atributo para um conjunto de entidades do documento.          


**Ponto final REST:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>

## <a name="request-parameters"></a>Parâmetros do Pedido

Name  |Value | Necessário?  |Descrição
-----------|----------|--------|----------
**expr**    |Cadeia de texto | Sim  |Uma expressão de consulta que especifica as entidades sobre o qual calcular histogramas.
**Modelo** |Cadeia de texto | Não |Selecione o nome do modelo que pretende consultar.  Atualmente, o valor é predefinido para *mais recente*.
**Atributos** | Cadeia de texto | Não<br>predefinição: | Uma lista delimitada por vírgulas que especifica os valores de atributos que estão incluídos na resposta. Nomes de atributo diferenciam maiúsculas de minúsculas.
**count** |Number | Não<br>Predefinição: 10 |Número de resultados a devolver.
**offset**  |Number | Não<br>Predefinição: 0 |Índice do primeiro resultado a devolver.
**timeout**  |Number | Não<br>Predefinição: 1000 |Tempo limite em milissegundos. Só foram encontradas quaisquer interpretações antes do tempo limite decorreu são devolvidas.

## <a name="response-json"></a>Resposta (JSON)

Name | Descrição
--------|---------
**expr**  |O parâmetro expr no pedido.
**num_entities** | Número total de entidades correspondentes.
**histograms** |  Uma matriz de histogramas, um para cada atributo especificado no pedido.
**histograms[x].attribute** | Nome do atributo na qual o histograma foi computado.
**histograms[x].distinct_values** | Número de valores distintos entre entidades para este atributo de correspondência.
**histograms[x].total_count** | Número total de instâncias de valor entre entidades para este atributo de correspondência.
**histograms[x].histogram** | Dados de histograma para este atributo.
**histograms[x].histogram[y].value** |  Um valor para o atributo.
**histograms[x].histogram[y].logprob**  |Probabilidade de registo natural total de correspondência de entidades com este valor de atributo.
**histograms[x].histogram[y].count**  |Número de entidades correspondentes com este valor de atributo.
**aborted** | VERDADEIRO se o pedido excedeu o limite de tempo.


#### <a name="example"></a>Exemplo:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Neste exemplo, para gerar um histograma da contagem de publicações por ano de um determinado autor desde 2010, podemos primeiro gerar a expressão de consulta utilizando o **interpretar** API com a cadeia de consulta: *papers por Jaime teevan após 2012*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>A expressão na interpretação primeiro retornado pela interpretação de API é *e (composição (AA. AuN = = "jaime teevan"), Y > 2012)*.
<br>Este valor de expressão, em seguida, é passado para o **calchistogram** API. O *attributes=Y,F.FN* parâmetro indica que as distribuições de contagens de documento devem ser por ano e o campo de estudo, por exemplo:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>A resposta a essa solicitação primeiro indica que há 37 papers que correspondem à expressão de consulta.  Para o *ano* atributo, existem 3 valores distintos, um para cada ano depois de 2012 (ou seja, 2013, 2014 e 2015), conforme especificado na consulta.  A contagem de papel total sobre os valores distintos 3 é 37.  Para cada *ano*, o histograma mostra o valor, a probabilidade de registo natural total e a contagem de correspondência de entidades.     

O histograma para *campo de estudo* mostra que existem 34 campos distintos de estudo. Como um documento pode ser associado a vários campos de estudo, a contagem total (53) pode ser maior que o número de entidades correspondentes.  Embora existam 34 valores distintos, a resposta inclui apenas a parte superior 4 devido a *contagem = 4* parâmetro.

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
