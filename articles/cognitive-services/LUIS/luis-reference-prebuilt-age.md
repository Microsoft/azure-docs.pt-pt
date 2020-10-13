---
title: Idade Entidade pré-construída - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informação de entidade pré-construída em Compreensão de Línguas (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.openlocfilehash: 4e1a6b1b6fb99a2786de06e89960e8480a5e1338
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91532929"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída para uma app LUIS
A entidade etária pré-construída capta o valor da idade tanto numericamente como em termos de dias, semanas, meses e anos. Como esta entidade já está treinada, não precisa de adicionar palavras de exemplo que contenham idade às intenções de aplicação. A entidade etária é apoiada em [muitas culturas.](luis-reference-prebuilt-entities.md)

## <a name="types-of-age"></a>Tipos de idade
A idade é gerida a partir do repositório GitHub [do texto Recognisers](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3)

## <a name="resolution-for-prebuilt-age-entity"></a>Resolução para entidade etária pré-construída



#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON é com o `verbose` parâmetro definido `false` para:

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O seguinte JSON é com o `verbose` parâmetro definido `true` para:

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ],
    "$instance": {
        "age": [
            {
                "type": "builtin.age",
                "text": "90 day old",
                "startIndex": 2,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[Resposta V2](#tab/V2)

O exemplo a seguir mostra a resolução da entidade **builtin.age.**

```json
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final de [previsão V3](luis-migration-api-v3.md).

Conheça a [moeda,](luis-reference-prebuilt-currency.md) [dataV2](luis-reference-prebuilt-datetimev2.md)e entidades [de dimensão.](luis-reference-prebuilt-dimension.md)
