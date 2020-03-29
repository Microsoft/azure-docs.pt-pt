---
title: Entidade Pré-construída de Temperatura - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidades pré-construídas de temperatura em Compreensão linguística (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 7e2b48c6353f56ab2269a8718146cb765797adba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270371"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída de temperatura para uma app LUIS
A temperatura extrai uma variedade de tipos de temperatura. Como esta entidade já está treinada, não precisa de adicionar declarações de exemplo contendo temperatura à aplicação. A entidade de temperatura é apoiada em [muitas culturas.](luis-reference-prebuilt-entities.md)

## <a name="types-of-temperature"></a>Tipos de temperatura
A temperatura é gerida a partir do repositório GitHub [de texto de reconhecimento](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819)

## <a name="resolution-for-prebuilt-temperature-entity"></a>Resolução para entidade de temperatura pré-construída

Os seguintes objetos de entidade são devolvidos para consulta:

`set the temperature to 30 degrees`


#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON `verbose` está com `false`o parâmetro definido para:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O seguinte JSON `verbose` está com `true`o parâmetro definido para:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ],
    "$instance": {
        "temperature": [
            {
                "type": "builtin.temperature",
                "text": "30 degrees",
                "startIndex": 23,
                "length": 10,
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

O exemplo seguinte mostra a resolução da entidade **builtin.temperature.**

```json
"entities": [
    {
        "entity": "30 degrees",
        "type": "builtin.temperature",
        "startIndex": 23,
        "endIndex": 32,
        "resolution": {
        "unit": "Degree",
        "value": "30"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

Conheça a [percentagem](luis-reference-prebuilt-percentage.md), [número](luis-reference-prebuilt-number.md)e as entidades [etárias.](luis-reference-prebuilt-age.md)
