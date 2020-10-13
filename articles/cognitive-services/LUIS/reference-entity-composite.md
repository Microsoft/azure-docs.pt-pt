---
title: Tipo de entidade composta - LUIS
titleSuffix: Azure Cognitive Services
description: Uma entidade composta é constituída por outras entidades, como entidades pré-construídas, expressão simples, regular e entidades de lista. As entidades separadas formam toda uma entidade.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 97bd079034a39f158e3ca438c484929cb6f00363
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542262"
---
# <a name="composite-entity"></a>Entidade composta

Uma entidade composta é constituída por outras entidades, como entidades pré-construídas, expressão simples, regular e entidades de lista. As entidades separadas formam toda uma entidade.

> [!CAUTION]
> Esta entidade é **depreciada.** Por favor, migrar para a [entidade de aprendizagem automática.](reference-entity-machine-learned-entity.md)

**Esta entidade é um bom ajuste quando os dados:**

* Estão relacionados um com o outro.
* Estão relacionados entre si no contexto da expressão.
* Use uma variedade de tipos de entidades.
* Precisa de ser agrupado e processado pela aplicação do cliente como uma unidade de informação.
* Tenha uma variedade de expressões de utilizador que requerem aprendizagem automática.

![entidade composta](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Exemplo JSON

Considere uma entidade composta de pré-construído `number` e com a seguinte `Location::ToLocation` expressão:

`book 2 tickets to cairo`

Note-se `2` que, o número, e `cairo` , a ToLocation têm palavras entre eles que não fazem parte de nenhuma das entidades. O sublinhado verde, usado numa expressão rotulada no site da [LUIS,](luis-reference-regions.md) indica uma entidade composta.

![Entidade Composta](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[Resposta do ponto final da previsão V2](#tab/V2)

As entidades compostas são devolvidas num `compositeEntities` conjunto e todas as entidades dentro do compósito são também devolvidas no `entities` conjunto:

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[Resposta do ponto final de previsão V3](#tab/V3)

Este é o JSON se `verbose=false` for definido na cadeia de consulta:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

Este é o JSON se `verbose=true` for definido na cadeia de consulta:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *


|Objeto de dados|Nome da entidade|Valor|
|--|--|--|
|Entidade Pré-construída - número|"builtin.number"|"2"|
|Entidade Pré-construída - GeografiaV2|"Localização::ToLocation"|"Cairo"|

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as entidades:

* [Conceitos](luis-concept-entity-types.md)
* [Como criar](luis-how-to-add-entities.md)
