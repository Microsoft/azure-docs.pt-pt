---
title: Entidades pré-construídas dimensionárias - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informação de entidade pré-construída de dimensão na Compreensão de Línguas (LUIS).
services: cognitive-services
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 0b5f081dc8c0bc203ccafde4a513371d11e36879
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91538352"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída de dimensão para uma app LUIS
A entidade de dimensão pré-construída deteta vários tipos de dimensões, independentemente da cultura da app LUIS. Como esta entidade já está treinada, não precisa de adicionar palavras de exemplo que contenham dimensões às intenções da aplicação. A entidade de dimensão é apoiada em [muitas culturas.](luis-reference-prebuilt-entities.md)

## <a name="types-of-dimension"></a>Tipos de dimensão

A dimensão é gerida a partir do repositório GitHub [de texto Recognisers.](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml)

## <a name="resolution-for-dimension-entity"></a>Resolução para entidade de dimensão

Os seguintes objetos de entidade são devolvidos para consulta:

`10 1/2 miles of cable`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON é com o `verbose` parâmetro definido `false` para:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O seguinte JSON é com o `verbose` parâmetro definido `true` para:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ],
    "$instance": {
        "dimension": [
            {
                "type": "builtin.dimension",
                "text": "10 1/2 miles",
                "startIndex": 0,
                "length": 12,
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

O exemplo a seguir mostra a resolução da entidade **builtin.dimension.**

```json
{
    "entity": "10 1/2 miles",
    "type": "builtin.dimension",
    "startIndex": 0,
    "endIndex": 11,
    "resolution": {
    "unit": "Mile",
    "value": "10.5"
    }
}
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final de [previsão V3](luis-migration-api-v3.md).

Saiba mais sobre o [email,](luis-reference-prebuilt-email.md) [número](luis-reference-prebuilt-number.md)e entidades [ordinais.](luis-reference-prebuilt-ordinal.md)
