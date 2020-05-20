---
title: Tipo de entidade simples - LUIS
titleSuffix: Azure Cognitive Services
description: Uma entidade simples descreve um único conceito do contexto de aprendizagem automática. Adicione uma lista de frases ao utilizar uma entidade simples para melhorar os resultados.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 1b5754be3c9941101a53f332841ace93caf9acdd
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684553"
---
# <a name="simple-entity"></a>Entidade simples

Uma entidade simples é uma entidade genérica que descreve um único conceito e é aprendida com o contexto de aprendizagem automática. Como as entidades simples são geralmente nomes como nomes de empresa, nomes de produtos ou outras categorias de nomes, adicionam uma lista de [frases](luis-concept-feature.md) quando se usa uma entidade simples para impulsionar o sinal dos nomes utilizados.

**A entidade é um bom ajuste quando:**

* Os dados não são consistentemente formatados, mas indicam a mesma coisa.

![entidade simples](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Exemplo JSON

`Bob Jones wants 3 meatball pho`

Na expressão anterior, `Bob Jones` é rotulado como uma entidade `Customer` simples.

Os dados devolvidos do ponto final incluem o nome da entidade, o texto descoberto da expressão, a localização do texto descoberto e a pontuação:

#### <a name="v2-prediction-endpoint-response"></a>[Resposta final de previsão V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Resposta final de previsão V3](#tab/V3)

Este é o JSON se estiver definido na corda de `verbose=false` consulta:

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