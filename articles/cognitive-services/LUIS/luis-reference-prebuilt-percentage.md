---
title: Percentagem Entidade Pré-construída - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém a percentagem de informações da entidade pré-criados na compreensão de idiomas (LUIS).
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270499"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Percentagem de entidade pré-construída para uma app LUIS
Os números percentuais podem aparecer como frações, `3 1/2`, ou em percentagem, `2%`. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém a percentagem para os objetivos do aplicativo. A entidade percentual é apoiada em [muitas culturas.](luis-reference-prebuilt-entities.md)

## <a name="types-of-percentage"></a>Tipos de percentagem
A percentagem é gerida a partir do repositório GitHub [de texto de reconhecimento](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)

## <a name="resolution-for-prebuilt-percentage-entity"></a>Resolução de entidade de percentagem pré-criados

Os seguintes objetos de entidade são devolvidos para consulta:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON está com o parâmetro `verbose` definido para `false`:

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O seguinte JSON está com o parâmetro `verbose` definido para `true`:

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
