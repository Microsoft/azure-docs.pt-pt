---
title: Referência de email de entidades pré-construídas LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém e-mail informações da entidade pré-criados na compreensão de idiomas (LUIS).
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273471"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>E-mail entidade pré-construída para uma app LUIS
Extração de e-mail inclui o endereço de e-mail completo de uma expressão. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém o e-mail para os objetivos do aplicativo. A entidade de e-mail é suportada apenas na cultura `en-us`.

## <a name="resolution-for-prebuilt-email"></a>Resolução de correio eletrónico pré-criados

Os seguintes objetos de entidade são devolvidos para consulta:

`please send the information to patti@contoso.com`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON está com o parâmetro `verbose` definido para `false`:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)

O seguinte JSON está com o parâmetro `verbose` definido para `true`:

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
