---
title: Entidade Pré-construída de Temperatura - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém temperatura informações da entidade pré-criados na compreensão de idiomas (LUIS).
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270371"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída de temperatura para uma app LUIS
Temperatura extrai uma variedade de tipos de temperatura. Uma vez que esta entidade já está preparada, não é necessário adicionar expressões de exemplo que contém a temperatura à aplicação. A entidade de temperatura é apoiada em [muitas culturas.](luis-reference-prebuilt-entities.md)

## <a name="types-of-temperature"></a>Tipos de temperatura
A temperatura é gerida a partir do repositório GitHub [de texto de reconhecimento](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819)

## <a name="resolution-for-prebuilt-temperature-entity"></a>Resolução de entidades de temperatura pré-criados

Os seguintes objetos de entidade são devolvidos para consulta:

`set the temperature to 30 degrees`


#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON está com o parâmetro `verbose` definido para `false`:

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
O seguinte JSON está com o parâmetro `verbose` definido para `true`:

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
