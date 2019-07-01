---
title: Ordinal V2 pré-criados de entidade
titleSuffix: Language Understanding - Azure Cognitive Services
description: Este artigo contém ordinal V2 informações da entidade pré-criados na compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 862b962f5642e01d7ed8250f49d51a6132447083
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486142"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Ordinal V2 entidade criados previamente para uma aplicação LUIS
Número de V2 ordinal expande [Ordinal](luis-reference-prebuilt-ordinal.md) para fornecer como referências relativas `next`, `last`, e `previous`. Estes não são extraídos com a entidade pré-criados ordinal.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Resolução de entidade de V2 ordinal pré-criados

### <a name="api-version-2x"></a>Versão de API 2.x

O exemplo seguinte mostra a resolução do **builtin.ordinalV2** entidade.

```json
{
    "query": "what is the second to last choice in the list",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.823669851
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.823669851
        }
    ],
    "entities": [
        {
            "entity": "the second to last",
            "type": "builtin.ordinalV2.relative",
            "startIndex": 8,
            "endIndex": 25,
            "resolution": {
                "offset": "-1",
                "relativeTo": "end"
            }
        }
    ]
}
```

### <a name="preview-api-version-3x"></a>Versão de API de pré-visualização 3.x

O seguinte JSON é com o `verbose` parâmetro definido como `false`:

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ]
        }
    }
}
```

O seguinte JSON é com o `verbose` parâmetro definido como `true`:

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ],
            "$instance": {
                "ordinalV2": [
                    {
                        "type": "builtin.ordinalV2.relative",
                        "text": "the second to last",
                        "startIndex": 8,
                        "length": 18,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [percentagem](luis-reference-prebuilt-percentage.md), [número de telefone](luis-reference-prebuilt-phonenumber.md), e [temperatura](luis-reference-prebuilt-temperature.md) entidades. 
