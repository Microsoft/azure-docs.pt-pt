---
title: Entidade Pré-construída idade - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações sobre entidades pré-construídas de idade em Compreensão da Língua (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 1607b80276a9c35bf0ac2f0a00bd2226e2c07f97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270800"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída para uma app LUIS
A entidade etária pré-construída captura o valor da idade tanto numericamente como em termos de dias, semanas, meses e anos. Como esta entidade já está treinada, não precisa de adicionar declarações de exemplo contendo idade às intenções de aplicação. A entidade etária é apoiada em [muitas culturas.](luis-reference-prebuilt-entities.md)

## <a name="types-of-age"></a>Tipos de idade
A idade é gerida a partir do repositório GitHub [de texto de reconhecimento](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3)

## <a name="resolution-for-prebuilt-age-entity"></a>Resolução para entidade etária pré-construída



#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON `verbose` está com `false`o parâmetro definido para:

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
O seguinte JSON `verbose` está com `true`o parâmetro definido para:

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

O exemplo seguinte mostra a resolução da entidade **builtin.age.**

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

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

Conheça a [moeda,](luis-reference-prebuilt-currency.md) [datav2,](luis-reference-prebuilt-datetimev2.md)e entidades [dimensionais.](luis-reference-prebuilt-dimension.md)
