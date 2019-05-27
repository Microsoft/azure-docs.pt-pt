---
title: Entidade pré-criados de moeda
titleSuffix: Azure
description: Este artigo contém moeda informações da entidade pré-criados na compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 7e882a66ae5a090e1fd3a0850ff35281dc4e692d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "65072013"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Moeda entidade criados previamente para uma aplicação LUIS
A entidade de moeda pré-criados Deteta a moeda de diferentes denominations e países/regiões, independentemente da cultura de aplicação do LUIS. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém a moeda a dos objetivos do aplicativo. Entidade de moeda é suportada no [várias culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Tipos de moeda
Moeda é gerenciada a partir da [reconhecedores texto](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) repositório do GitHub

## <a name="resolution-for-currency-entity"></a>Resolução de entidades de moeda

### <a name="api-version-2x"></a>Versão de API 2.x

O exemplo seguinte mostra a resolução do **builtin.currency** entidade.

```json
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```



### <a name="preview-api-version-3x"></a>Versão de API de pré-visualização 3.x

O seguinte JSON é com o `verbose` parâmetro definido como `false`:

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ]
        }
    }
}
```

O seguinte JSON é com o `verbose` parâmetro definido como `true`:

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ],
            "$instance": {
                "money": [
                    {
                        "type": "builtin.currency",
                        "text": "$10.99",
                        "startIndex": 23,
                        "length": 6,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimensão](luis-reference-prebuilt-dimension.md), e [e-mail](luis-reference-prebuilt-email.md) entidades. 
