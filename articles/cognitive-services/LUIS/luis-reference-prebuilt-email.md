---
title: Referência de email de entidades pré-construídas LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidades pré-construídas por e-mail em Compreensão de Línguas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: f7aa2a341a82d30f7022af49e626a3cd358854b7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273471"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>E-mail entidade pré-construída para uma app LUIS
A extração de e-mail inclui todo o endereço de e-mail de uma expressão. Como esta entidade já está treinada, não precisa de adicionar declarações de exemplo contendo e-mail para as intenções da aplicação. A entidade de `en-us` e-mail é suportada apenas na cultura.

## <a name="resolution-for-prebuilt-email"></a>Resolução para e-mail pré-construído

Os seguintes objetos de entidade são devolvidos para consulta:

`please send the information to patti@contoso.com`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON `verbose` está com `false`o parâmetro definido para:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)

O seguinte JSON `verbose` está com `true`o parâmetro definido para:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ],
    "$instance": {
        "email": [
            {
                "type": "builtin.email",
                "text": "patti@contoso.com",
                "startIndex": 31,
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

O exemplo seguinte mostra a resolução da entidade **builtin.email.**

```json
"entities": [
    {
        "entity": "patti@contoso.com",
        "type": "builtin.email",
        "startIndex": 31,
        "endIndex": 55,
        "resolution": {
        "value": "patti@contoso.com"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

Conheça o [número](luis-reference-prebuilt-number.md), [ordinal](luis-reference-prebuilt-ordinal.md)e [percentagem](luis-reference-prebuilt-percentage.md).
