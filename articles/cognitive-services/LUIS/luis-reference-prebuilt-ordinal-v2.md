---
title: Entidade predefinida do ordinal v2-LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidade predefinidas do ordinal V2 em Reconhecimento vocal (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 1579c50f2dcf78e350ce014ffb1516cec4ac85b3
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933491"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Entidade predefinida do ordinal v2 para um aplicativo LUIS
`previous`O `next`número do ordinal v2 expande o [ordinal](luis-reference-prebuilt-ordinal.md) para fornecer referências relativas `last`, como, e. Eles não são extraídos usando a entidade predefinida ordinal.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Resolução para a entidade do ordinal v2 predefinida

### <a name="api-version-2x"></a>Versão da API 2. x

O exemplo a seguir mostra a resolução da entidade **Builtin. ordinalV2** .

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

### <a name="preview-api-version-3x"></a>Versão prévia da API 3. x

O JSON a seguir é com `verbose` o parâmetro definido `false`como:

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

O JSON a seguir é com `verbose` o parâmetro definido `true`como:

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

Saiba mais sobre a [porcentagem](luis-reference-prebuilt-percentage.md), o [número de telefone](luis-reference-prebuilt-phonenumber.md)e as entidades de [temperatura](luis-reference-prebuilt-temperature.md) . 
