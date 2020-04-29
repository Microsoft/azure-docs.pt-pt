---
title: Entidade pré-construída PersonName - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidades pré-construídas em Compreensão de Línguas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 768c719211e8a8f2133d3798343d076e795a3da0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273425"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída personname para uma aplicação LUIS
A entidade pré-construída nome de pessoadetecta nomes de pessoas. Como esta entidade já está treinada, não precisa de adicionar declarações de exemplo contendo personName às intenções da aplicação. personName entidade é suportada em culturas inglesas e [chinesas.](luis-reference-prebuilt-entities.md)

## <a name="resolution-for-personname-entity"></a>Resolução para entidade personName

Os seguintes objetos de entidade são devolvidos para consulta:

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[Resposta V3](#tab/V3)


O seguinte JSON `verbose` está com `false`o parâmetro definido para:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O seguinte JSON `verbose` está com `true`o parâmetro definido para:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[Resposta V2](#tab/V2)

O exemplo seguinte mostra a resolução da entidade **builtin.personName.**

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

Conheça o [e-mail](luis-reference-prebuilt-email.md), [número,](luis-reference-prebuilt-number.md)e entidades [ordinárias.](luis-reference-prebuilt-ordinal.md)
