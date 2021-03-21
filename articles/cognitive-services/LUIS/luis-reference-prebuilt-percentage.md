---
title: Percentagem de entidade pré-construída - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informação de entidade pré-construída percentual na Compreensão de Línguas (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: bb64a32e2bdd3976fba3ce63433b13eb4891afc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541701"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída percentual para uma app LUIS
Os números percentuais podem aparecer como `3 1/2` frações, ou em percentagem, `2%` . Como esta entidade já está treinada, não precisa de adicionar palavras de exemplo contendo percentagem às intenções de candidatura. A entidade percentual é apoiada em [muitas culturas.](luis-reference-prebuilt-entities.md)

## <a name="types-of-percentage"></a>Tipos de percentagem
A percentagem é gerida a partir do repositório GitHub [do texto Recognisers](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)

## <a name="resolution-for-prebuilt-percentage-entity"></a>Resolução para entidade percentual pré-construída

Os seguintes objetos de entidade são devolvidos para consulta:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON é com o `verbose` parâmetro definido `false` para:

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O seguinte JSON é com o `verbose` parâmetro definido `true` para:

```json
"entities": {
    "percentage": [
        2
    ],
    "$instance": {
        "percentage": [
            {
                "type": "builtin.percentage",
                "text": "2%",
                "startIndex": 36,
                "length": 2,
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

O exemplo a seguir mostra a resolução da entidade **incorporada.percentagem.**

```json
"entities": [
    {
        "entity": "2%",
        "type": "builtin.percentage",
        "startIndex": 36,
        "endIndex": 37,
        "resolution": {
        "value": "2%"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final de [previsão V3](luis-migration-api-v3.md).

Conheça as [entidades ordinais,](luis-reference-prebuilt-ordinal.md) [número](luis-reference-prebuilt-number.md)e [temperatura.](luis-reference-prebuilt-temperature.md)
