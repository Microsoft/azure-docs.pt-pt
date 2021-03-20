---
title: Entidade pré-construída de moeda - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informação de entidade pré-construída em Informação de Línguas (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: c89de0ba74d04c510f3d5ba537f3a6dcc4819169
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91534341"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída de moeda para uma aplicação LUIS
A entidade monetária pré-construída deteta moeda em muitas denominações e países/regiões, independentemente da cultura de aplicações LUIS. Como esta entidade já está treinada, não precisa de adicionar palavras de exemplo que contenham moeda às intenções de aplicação. A entidade monetária é apoiada em [muitas culturas.](luis-reference-prebuilt-entities.md)

## <a name="types-of-currency"></a>Tipos de moeda
A moeda é gerida a partir do repositório GitHub [do texto Recognisers](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Resolução para entidade monetária

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON é com o `verbose` parâmetro definido `false` para:

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "units": "Dollar"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O seguinte JSON é com o `verbose` parâmetro definido `true` para:

```json
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
```

#### <a name="v2-response"></a>[Resposta V2](#tab/V2)

O exemplo a seguir mostra a resolução da entidade **builtin.currency.**

```json
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
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final de [previsão V3](luis-migration-api-v3.md).

Conheça as [entidades de dataV2,](luis-reference-prebuilt-datetimev2.md) [dimensão](luis-reference-prebuilt-dimension.md) [e](luis-reference-prebuilt-email.md) email.
