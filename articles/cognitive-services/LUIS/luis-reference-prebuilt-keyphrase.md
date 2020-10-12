---
title: Entidade pré-construída - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidade pré-construídas em Informação de Línguas (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 02a48c8d106795b970d573bb71f52ba92e0a90ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541990"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>entidade pré-construída keyPhrase para uma aplicação LUIS
A entidade-chavePhrase extrai uma variedade de frases-chave de uma expressão. Não é necessário adicionar palavras de exemplo que contenham a chavePhrase à aplicação. A entidade-chavePhrase é suportada em [muitas culturas](luis-language-support.md#languages-supported) como parte das funcionalidades de análise de [texto.](../text-analytics/overview.md)

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Resolução para entidade-chave de assinatura pré-construída

Os seguintes objetos de entidade são devolvidos para consulta:

`where is the educational requirements form for the development and engineering group`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON é com o `verbose` parâmetro definido `false` para:

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O seguinte JSON é com o `verbose` parâmetro definido `true` para:

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "educational requirements",
                "startIndex": 13,
                "length": 24,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "development",
                "startIndex": 51,
                "length": 11,
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

O exemplo a seguir mostra a resolução da entidade **builtin.keyPhrase.**

```json
"entities": [
    {
        "entity": "development",
        "type": "builtin.keyPhrase",
        "startIndex": 51,
        "endIndex": 61
    },
    {
        "entity": "educational requirements",
        "type": "builtin.keyPhrase",
        "startIndex": 13,
        "endIndex": 36
    }
]
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final de [previsão V3](luis-migration-api-v3.md).

Saiba mais sobre a [percentagem,](luis-reference-prebuilt-percentage.md) [número](luis-reference-prebuilt-number.md)e entidades [etárias.](luis-reference-prebuilt-age.md)
