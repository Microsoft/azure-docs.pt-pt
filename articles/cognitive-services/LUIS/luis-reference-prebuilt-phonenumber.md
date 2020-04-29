---
title: Número de telefone Entidades Pré-construídas - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações sobre entidades pré-construídas de número de telefone em Compreensão de Línguas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4e2c8e27c6d4195252c6a5b423fa98b2a4247182
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270478"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída para um aplicativo LUIS
A `phonenumber` entidade extrai uma variedade de números de telefone, incluindo código country. Como esta entidade já está treinada, não precisa de adicionar declarações de exemplo à aplicação. A `phonenumber` entidade é `en-us` apoiada apenas na cultura.

## <a name="types-of-a-phone-number"></a>Tipos de número de telefone
`Phonenumber`é gerido a partir do repositório GitHub [de texto reconhecíveis](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-this-prebuilt-entity"></a>Resolução para esta entidade pré-construída

Os seguintes objetos de entidade são devolvidos para consulta:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON `verbose` está com `false`o parâmetro definido para:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O seguinte JSON `verbose` está com `true`o parâmetro definido para:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ],
    "$instance": {

        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 (800) 642-7676",
                "startIndex": 13,
                "length": 16,
                "score": 1.0,
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

O exemplo seguinte mostra a resolução da entidade **builtin.phonenumber.**

```json
"entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

Conheça a [percentagem,](luis-reference-prebuilt-percentage.md) [número](luis-reference-prebuilt-number.md)e as entidades de [temperatura.](luis-reference-prebuilt-temperature.md)
