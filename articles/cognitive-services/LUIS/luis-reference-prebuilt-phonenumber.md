---
title: Número de telefone Entidades pré-construídas - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidade pré-incorporadas em Informação de Línguas (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 598ecaddbab3b70297a460521c7ec3386b390a8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535394"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Entidade pré-incorporada de número de telefone para uma aplicação LUIS
A `phonenumber` entidade extrai uma variedade de números de telefone, incluindo código de país. Como esta entidade já está treinada, não precisa de adicionar palavras de exemplo à aplicação. A `phonenumber` entidade é apoiada apenas na `en-us` cultura.

## <a name="types-of-a-phone-number"></a>Tipos de um número de telefone
`Phonenumber`é gerido a partir do repositório GitHub [de texto Recognisers](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-this-prebuilt-entity"></a>Resolução para esta entidade pré-construída

Os seguintes objetos de entidade são devolvidos para consulta:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON é com o `verbose` parâmetro definido `false` para:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O seguinte JSON é com o `verbose` parâmetro definido `true` para:

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

O exemplo a seguir mostra a resolução da entidade **builtin.phonenumber.**

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

Saiba mais sobre o ponto final de [previsão V3](luis-migration-api-v3.md).

Saiba mais sobre a [percentagem,](luis-reference-prebuilt-percentage.md) [número](luis-reference-prebuilt-number.md)e entidades [de temperatura.](luis-reference-prebuilt-temperature.md)
