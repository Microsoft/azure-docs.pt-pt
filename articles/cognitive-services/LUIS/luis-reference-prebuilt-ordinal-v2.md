---
title: Entidade pré-construída Ordinal V2 - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações da entidade ordinal V2 pré-construída sinal em Compreensão da Língua (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 5e852313db75e598da647ea0f985e2ee18af16de
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270488"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída Ordinal V2 para uma app LUIS
O número Ordinal V2 expande [o Ordinal](luis-reference-prebuilt-ordinal.md) para fornecer referências relativas como, `next` `last`e `previous`. Estes não são extraídos utilizando a entidade ordinal pré-construída.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Resolução para entidade ordinal V2 pré-construída

Os seguintes objetos de entidade são devolvidos para consulta:

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON `verbose` está com `false`o parâmetro definido para:

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

O seguinte JSON `verbose` está com `true`o parâmetro definido para:

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

O exemplo seguinte mostra a resolução da entidade **builtin.ordinalV2.**

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

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

Conheça a [percentagem,](luis-reference-prebuilt-percentage.md) [número de telefone](luis-reference-prebuilt-phonenumber.md)e entidades de [temperatura.](luis-reference-prebuilt-temperature.md)
