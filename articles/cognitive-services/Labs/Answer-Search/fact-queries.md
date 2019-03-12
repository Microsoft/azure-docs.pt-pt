---
title: 'Início rápido: Consulta de fatos de pesquisa de resposta de projeto'
titlesuffix: Azure Cognitive Services
description: Consultas de factos com a Pesquisa de Respostas do Projeto
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/16/2018
ms.author: rosh
ms.openlocfilehash: fca459291fbf25c5404427fdcfc96947c52a1e74
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544312"
---
# <a name="quickstart-query-for-facts"></a>Início rápido: Consultar os fatos

Se a consulta for de um facto como uma data ou conhecimentos identificáveis, a resposta pode conter respostas de `facts`. As respostas de facto contêm resultados relevantes, extraídos de parágrafos em documentos Web.  Estas consultas devolvem sempre páginas Web, e os [factos](fact-queries.md) e/ou as [entidades](entity-queries.md) são dependentes da consulta.

As consultas como valentines+2016 (dia+de+são+valentim+2016), when+is+ramadan (quando+é+o+ramadão) são consideradas consultas relacionadas com datas. Se o Bing determinar que a consulta está relacionada com uma data, a resposta contém uma resposta de `facts`.

Segue-se um exemplo de uma resposta de `facts` relacionada com uma data.

**Consulta:**
```
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=valentines+2016

```

**Resposta:** O `subjectName` campo contém uma versão de exibição de consulta do utilizador que pode utilizar como uma etiqueta ao exibir o fato. Se a cadeia de consulta for valentines+2016 (dia+de+são+valentim+2016), o Bing poderá mudá-la para Valentine's Day 2016 (Dia de São Valentim 2016). O campo de descrição contém o facto.

```
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "valentines 2016"
    },
    "facts" : {
        "id" : "https:\/\/www.bingapis.com\/api\/v7\/#Facts",
        "value" : [{
            "description" : "Valentine's Day was on Sunday, February 14, 2016.",
            "subjectName" : "Valentine's Day 2016"
        }]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Facts",
                "value" : {
                    "id" : "https:\/\/www.bingapis.com\/api\/v7\/knowledge\/#Facts"
                }
            }]
        }
    }
}

```

A consulta “Why is the sky blue?” (Por que o céu azul é azul?) devolve um exemplo de uma resposta relacionada com conhecimentos.

**Consulta:**

```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=why+is+the+sky+blue

```

**Resposta:** O `value/description` campo contém os dados de conhecimento ou informações solicitadas pela consulta.

```
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "url": "https://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "spaceplace.nasa.gov/blue-sky/en/",
        "url": "https://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "seeMoreUrl": "https://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "providerDisplayName": "spaceplace.nasa.gov/blue-sky/en/",
        "seeMoreUrl": "https://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "value": [
      {
        "image": {
          "webSearchUrl": "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&q=&id=B632272C4E934D7F0B18790300B2DE34E7676C7A&simid=608045681196075791&iss=eqna",
          "name": "Why is the sky blue in the sky?",
          "thumbnailUrl": "https://www.bing.com/th?id=ODE.858093005&w=140&h=140&c=8&rs=1&qlt=100&pid=3.1",
          "isFamilyFriendly": true
        },
        "description": "When sunlight reaches Earth's atmosphere, it is scattered in all directions by all the gases and particles in the air. Blue is scattered by air molecules more than other colors because it travels as shorter, smaller waves. This is why we see a blue sky most of the time.\n\nCloser to the horizon, the sky fades to a lighter blue or white. The sunlight reaching us from low in the sky has passed through even more air than the sunlight reaching us from overhead. As the sunlight has passed through all this air, the air molecules have scattered and rescattered the blue light many times in many directions. Also, the surface of Earth has reflected and scattered the light. All this scattering mixes the colors together again so we see more white and less blue.",
        "subjectName": "Why is the sky blue in the sky?",
        "primaryData": [
          "The atmosphere scatters more blue light"
        ]
      }
    ]
  },

```

## <a name="tabular-data"></a>Dados tabulares
Em alguns casos, os factos podem ser devolvidos como `_type: StructuredValue/TabularData`. A consulta seguinte obtém dados tabulares com informações que comparam café e chá.

```
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=coffee+vs+tea&mkt=en-us

```
Os resultados dos `facts` incluem as seguintes linhas e células:
```
    "value": [
      {
        "subjectName": "Coffee vs. Tea",
        "richCaption": {
          "_type": "StructuredValue/TabularData",
          "header": [
            "",
            "Coffee",
            "Tea"
          ],
          "rows": [
            {
              "cells": [
                {
                  "text": "Part of plant used"
                },
                {
                  "text": "Bean"
                },
                {
                  "text": "Leaf"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Caffeine Content"
                },
                {
                  "text": "80-185 mg per 8 ounce cup depending upon the brew and the..."
                },
                {
                  "text": "15 - 70 mg per cup"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Types of Consumption"
                },
                {
                  "text": "Drip Coffee"
                },
                {
                  "text": "White Tea"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Additions"
                },
                {
                  "text": "Sugar"
                },
                {
                  "text": "Milk"
                }
              ]
            }
          ],
          "seeMoreUrl": {
            "text": "8 more rows",
            "url": "https://www.diffen.com/difference/Coffee_vs_Tea"
          }
        }
      }
    ]
  },

```

## <a name="next-steps"></a>Passos Seguintes
- [Início rápido do C#](c-sharp-quickstart.md)
- [Início Rápido do Java](java-quickstart.md)
- [Início rápido de Node](node-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)
