---
title: Percentagem Entidade Pré-construída - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações percentuais sobre entidades pré-construídas em Compreensão linguística (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 31ea1c36139abcb1e102161ad76a203073ba4dfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270499"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Percentagem de entidade pré-construída para uma app LUIS
Os números percentuais `3 1/2`podem aparecer como `2%`frações, ou em percentagem, . Como esta entidade já está treinada, não precisa de adicionar declarações de exemplo contendo percentagem às intenções de aplicação. A entidade percentual é apoiada em [muitas culturas.](luis-reference-prebuilt-entities.md)

## <a name="types-of-percentage"></a>Tipos de percentagem
A percentagem é gerida a partir do repositório GitHub [de texto de reconhecimento](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)

## <a name="resolution-for-prebuilt-percentage-entity"></a>Resolução para entidade de percentagem pré-construída

Os seguintes objetos de entidade são devolvidos para consulta:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON `verbose` está com `false`o parâmetro definido para:

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O seguinte JSON `verbose` está com `true`o parâmetro definido para:

```json
"entities": {
    "percentage": [
        2
    ],
    "$instance": {
        "percentage": [
            {
                "type": "builtin.percentage",
                "text": "2%",
                "startIndex": 36,
                "length": 2,
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

O exemplo seguinte mostra a resolução da entidade **incorporada.percentual.**

```json
"entities": [
    {
        "entity": "2%",
        "type": "builtin.percentage",
        "startIndex": 36,
        "endIndex": 37,
        "resolution": {
        "value": "2%"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

Conheça as entidades [de ordinal,](luis-reference-prebuilt-ordinal.md) [número](luis-reference-prebuilt-number.md)e [temperatura.](luis-reference-prebuilt-temperature.md)
