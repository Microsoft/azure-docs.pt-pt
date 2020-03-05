---
title: Número de telefone Entidades Pré-construídas - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidade pré-criados numéricas na compreensão de idiomas (LUIS) do telefone.
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270478"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída para um aplicativo LUIS
A entidade `phonenumber` extrai uma variedade de números de telefone, incluindo código country. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo para a aplicação. A entidade `phonenumber` é apoiada apenas na cultura `en-us`.

## <a name="types-of-a-phone-number"></a>Tipos de número de telefone
`Phonenumber` é gerido a partir do repositório GitHub [de texto de reconhecimento](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-this-prebuilt-entity"></a>Resolução para esta entidade pré-construída

Os seguintes objetos de entidade são devolvidos para consulta:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON está com o parâmetro `verbose` definido para `false`:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O seguinte JSON está com o parâmetro `verbose` definido para `true`:

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
