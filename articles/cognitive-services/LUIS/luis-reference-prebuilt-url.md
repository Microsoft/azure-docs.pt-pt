---
title: URL Entidades Pré-construídas - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidades pré-construídas em Compreensão linguística (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 49f145ce3e9022826abad5d274dd611bb2cc6530
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270350"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>URL entidade pré-construída para uma app LUIS
A entidade URL extrai URLs com nomes de domínio ou endereços IP. Como esta entidade já está treinada, não precisa de adicionar declarações de exemplo contendo URLs à aplicação. A entidade URL `en-us` é apoiada apenas na cultura.

## <a name="types-of-urls"></a>Tipos de URLs
Url é gerido a partir do repositório GitHub [de texto reconheço](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)

## <a name="resolution-for-prebuilt-url-entity"></a>Resolução para entidade URL pré-construída

Os seguintes objetos de entidade são devolvidos para consulta:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON `verbose` está com `false`o parâmetro definido para:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)

O seguinte JSON `verbose` está com `true`o parâmetro definido para:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ],
    "$instance": {
        "url": [
            {
                "type": "builtin.url",
                "text": "https://www.luis.ai",
                "startIndex": 0,
                "length": 17,
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

O exemplo que se https://www.luis.ai segue mostra a resolução do é um grande exemplo de serviços cognitivos de inteligência artificial

```json
"entities": [
    {
        "entity": "https://www.luis.ai",
        "type": "builtin.url",
        "startIndex": 0,
        "endIndex": 17
    }
]
```

* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

Conheça as entidades [de ordinal,](luis-reference-prebuilt-ordinal.md) [número](luis-reference-prebuilt-number.md)e [temperatura.](luis-reference-prebuilt-temperature.md)
