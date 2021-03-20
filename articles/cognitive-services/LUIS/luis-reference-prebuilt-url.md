---
title: URL Entidades Pré-construídas - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidades pré-construídas url na Compreensão de Línguas (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.openlocfilehash: bc33fd1b90306a016c419f227fb9e73e83e8ea83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535258"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Entidade pré-incorporada URL para uma aplicação LUIS
A entidade URL extrai URLs com nomes de domínio ou endereços IP. Como esta entidade já está treinada, não precisa de adicionar palavras de exemplo que contenham URLs à aplicação. A entidade URL é suportada apenas na `en-us` cultura.

## <a name="types-of-urls"></a>Tipos de URLs
A url é gerida a partir do repositório GitHub [de texto recognisers](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)

## <a name="resolution-for-prebuilt-url-entity"></a>Resolução para entidade URL pré-incorporada

Os seguintes objetos de entidade são devolvidos para consulta:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON é com o `verbose` parâmetro definido `false` para:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)

O seguinte JSON é com o `verbose` parâmetro definido `true` para:

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

O exemplo a seguir mostra a resolução do https://www.luis.ai é um grande exemplo de serviços cognitivos de inteligência artificial

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

Saiba mais sobre o ponto final de [previsão V3](luis-migration-api-v3.md).

Conheça as [entidades ordinais,](luis-reference-prebuilt-ordinal.md) [número](luis-reference-prebuilt-number.md)e [temperatura.](luis-reference-prebuilt-temperature.md)
