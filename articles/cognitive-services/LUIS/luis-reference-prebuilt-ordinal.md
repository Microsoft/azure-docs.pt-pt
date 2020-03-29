---
title: Entidade Ordinal Pré-construída - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidades pré-construídas em Compreensão linguística (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bb3bb27db48255f534e873ed4e93ac62f07016af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273452"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Entidade ordinal pré-construída para uma app LUIS
O número ordinal é uma representação numérica de um objeto dentro de um conjunto: `first`, `second`. `third` Como esta entidade já está treinada, não precisa de adicionar declarações de exemplo contendo as intenções de aplicação. A entidade ordinal é apoiada em [muitas culturas.](luis-reference-prebuilt-entities.md)

## <a name="types-of-ordinal"></a>Tipos de ordinal
Ordinal é gerido a partir do repositório GitHub [de texto reconheço](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Resolução para entidade ordinal pré-construída

Os seguintes objetos de entidade são devolvidos para consulta:

`Order the second option`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON `verbose` está com `false`o parâmetro definido para:

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O seguinte JSON `verbose` está com `true`o parâmetro definido para:

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
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

O exemplo seguinte mostra a resolução da entidade **incorporada.ordinal.**

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

Conheça as entidades [OrdinalV2,](luis-reference-prebuilt-ordinal-v2.md)número de [telefone](luis-reference-prebuilt-phonenumber.md)e [temperatura.](luis-reference-prebuilt-temperature.md)
