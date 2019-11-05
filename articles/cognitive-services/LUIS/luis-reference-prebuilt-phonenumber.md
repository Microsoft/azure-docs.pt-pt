---
title: Entidades predefinidas de número de telefone – LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidade predefinidas de número de telefone em Reconhecimento vocal (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 1cc7469bf6b29ed864fac3955dc8770aa879f84d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499533"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Entidade de número de telefone predefinida para um aplicativo LUIS
A entidade `phonenumber` extrai uma variedade de números de telefone, incluindo o código do país. Como essa entidade já está treinada, você não precisa adicionar o exemplo declarações ao aplicativo. A entidade `phonenumber` tem suporte apenas na cultura `en-us`. 

## <a name="types-of-a-phone-number"></a>Tipos de um número de telefone
`Phonenumber` é gerenciado no repositório GitHub de [texto de reconhecedores](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-this-prebuilt-entity"></a>Resolução para esta entidade predefinida

Os seguintes objetos de entidade são retornados para a consulta:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-responsetabv3"></a>[Resposta v3](#tab/V3)

O JSON a seguir é com o parâmetro `verbose` definido como `false`:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3, resposta detalhada](#tab/V3-verbose)
O JSON a seguir é com o parâmetro `verbose` definido como `true`:

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
#### <a name="v2-responsetabv2"></a>[Resposta v2](#tab/V2)

O exemplo a seguir mostra a resolução da entidade **Builtin. PhoneNumber** .

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

Saiba mais sobre o [ponto de extremidade de previsão v3](luis-migration-api-v3.md).

Saiba mais sobre as entidades [percentual](luis-reference-prebuilt-percentage.md), [número](luis-reference-prebuilt-number.md)e [temperatura](luis-reference-prebuilt-temperature.md) . 
