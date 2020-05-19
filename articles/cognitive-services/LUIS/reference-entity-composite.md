---
title: Tipo de entidade composta - LUIS
titleSuffix: Azure Cognitive Services
description: Uma entidade composta é constituída por outras entidades, como entidades pré-construídas, expressão simples, regular e entidades de lista. As entidades separadas formam uma entidade inteira.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: bac37e91933d16f36f2d8917760968122a4f5619
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588909"
---
# <a name="composite-entity"></a>Entidade composta

Uma entidade composta é constituída por outras entidades, como entidades pré-construídas, expressão simples, regular e entidades de lista. As entidades separadas formam uma entidade inteira.

> [!CAUTION]
> Esta entidade está **depreciada.** Por favor, emigra para a [entidade aprendida](reference-entity-machine-learned-entity.md)pela máquina.

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

Note `2` que, o número, e `cairo` o ToLocation têm palavras entre eles que não fazem parte de nenhuma das entidades. O sublinhado verde, usado numa expressão rotulada no site da [LUIS,](luis-reference-regions.md) indica uma entidade composta.

![Entidade Composta](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[Resposta final de previsão V2](#tab/V2)

As entidades compostas são devolvidas numa `compositeEntities` matriz e todas as entidades dentro do composto também são devolvidas na `entities` matriz:

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

#### <a name="v3-prediction-endpoint-response"></a>[Resposta final de previsão V3](#tab/V3)

Este é o JSON se estiver definido na corda de `verbose=false` consulta:

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

Este é o JSON se estiver definido na corda de `verbose=true` consulta:

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
|Entidade Pré-Construída - número|"builtin.number"|"2"|
|Entidade Pré-Construída - GeografiaV2|"Localização::Localização"|"Cairo"|

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as entidades:

* [Conceitos](luis-concept-entity-types.md)
* [Como criar](luis-how-to-add-entities.md)
