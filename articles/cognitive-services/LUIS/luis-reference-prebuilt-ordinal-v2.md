---
title: Entidade pré-construída Ordinal V2 - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidades pré-construídas ordinais V2 na Compreensão de Línguas (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 3fd80389dfa54cb8386c13e0e05a1e71ac4bd956
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "91541939"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída Ordinal V2 para uma aplicação LUIS
O número de V2 ordinal expande [o Ordinal](luis-reference-prebuilt-ordinal.md) para fornecer referências relativas `next` `last` como, e `previous` . Estes não são extraídos utilizando a entidade ordinal pré-construída.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Resolução para entidade ordinal V2 pré-construída

Os seguintes objetos de entidade são devolvidos para consulta:

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON é com o `verbose` parâmetro definido `false` para:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)

O seguinte JSON é com o `verbose` parâmetro definido `true` para:

```json
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
```
#### <a name="v2-response"></a>[Resposta V2](#tab/V2)

O exemplo a seguir mostra a resolução da entidade **builtin.ordinalV2.**

```json
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
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final de [previsão V3](luis-migration-api-v3.md).

Saiba mais sobre a [percentagem,](luis-reference-prebuilt-percentage.md) [número de telefone](luis-reference-prebuilt-phonenumber.md)e entidades de [temperatura.](luis-reference-prebuilt-temperature.md)
