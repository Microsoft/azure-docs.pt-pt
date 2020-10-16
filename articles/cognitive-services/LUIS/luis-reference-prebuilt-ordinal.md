---
title: Entidade pré-construída ordinal - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidades pré-construídas ordinais na Compreensão de Línguas (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 1fa86c8960ea9f32163ebd7991260a19ef7a5d79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535462"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída ordinal para uma aplicação LUIS
Número ordinal é uma representação numérica de um objeto dentro de um conjunto: `first` . . . . . . . . . . `second` `third` . Como esta entidade já está treinada, não precisa de adicionar palavras de exemplo contendo ordinal às intenções de aplicação. A entidade ordinal é apoiada em [muitas culturas.](luis-reference-prebuilt-entities.md)

## <a name="types-of-ordinal"></a>Tipos de ordinal
Ordinal é gerido a partir do repositório GitHub [de texto recognisers](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Resolução para entidade ordinal pré-construída

Os seguintes objetos de entidade são devolvidos para consulta:

`Order the second option`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON é com o `verbose` parâmetro definido `false` para:

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O seguinte JSON é com o `verbose` parâmetro definido `true` para:

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

O exemplo a seguir mostra a resolução da entidade **construída.ordinal.**

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

Saiba mais sobre o ponto final de [previsão V3](luis-migration-api-v3.md).

Conheça as [entidades OrdinalV2,](luis-reference-prebuilt-ordinal-v2.md) [número de telefone](luis-reference-prebuilt-phonenumber.md)e [temperatura.](luis-reference-prebuilt-temperature.md)
