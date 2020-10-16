---
title: Tipo de entidade simples - LUIS
titleSuffix: Azure Cognitive Services
description: Uma entidade simples descreve um único conceito do contexto de aprendizagem automática. Adicione uma lista de frases ao utilizar uma entidade simples para melhorar os resultados.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.openlocfilehash: 384d3df2de551e7c79f13a0fe47ffb26c7825f1b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91539291"
---
# <a name="simple-entity"></a>Entidade simples

Uma entidade simples é uma entidade genérica que descreve um único conceito e é aprendida a partir do contexto de aprendizagem automática. Como as entidades simples são geralmente nomes como nomes de empresas, nomes de produtos ou outras categorias de nomes, adicione uma lista de [frases](luis-concept-feature.md) quando utilizar uma entidade simples para aumentar o sinal dos nomes utilizados.

**A entidade é um bom ajuste quando:**

* Os dados não são consistentemente formatados, mas indicam a mesma coisa.

![entidade simples](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Exemplo JSON

`Bob Jones wants 3 meatball pho`

Na expressão anterior, `Bob Jones` é rotulado como uma entidade `Customer` simples.

Os dados devolvidos do ponto final incluem o nome da entidade, o texto descoberto a partir da expressão, a localização do texto descoberto e a pontuação:

#### <a name="v2-prediction-endpoint-response"></a>[Resposta do ponto final da previsão V2](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[Resposta do ponto final de previsão V3](#tab/V3)

Este é o JSON se `verbose=false` for definido na cadeia de consulta:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
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
|Entidade Simples|`Customer`|`bob jones`|

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aprenda sintaxe de padrão](reference-pattern-syntax.md)