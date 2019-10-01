---
title: Tipo de entidade simples-LUIS
titleSuffix: Azure Cognitive Services
description: Uma entidade simples é uma entidade genérica que descreve um único conceito e é aprendida do contexto aprendido pelo computador. Como as entidades simples são geralmente nomes como nomes de empresa, nomes de produtos ou outras categorias de nomes, adicione uma lista de frases ao usar uma entidade simples para aumentar o sinal dos nomes usados.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 2eb3ff847f9bfc162adfb281d2ac1fad6f8c5093
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695134"
---
# <a name="simple-entity"></a>Entidade simples 

Uma entidade simples é uma entidade genérica que descreve um único conceito e é aprendida do contexto aprendido pelo computador. Como as entidades simples são geralmente nomes como nomes de empresa, nomes de produtos ou outras categorias de nomes, adicione uma [lista de frases](luis-concept-feature.md) ao usar uma entidade simples para aumentar o sinal dos nomes usados. 

**A entidade é uma boa opção quando:**

* Os dados não são formatados consistentemente, mas indicam a mesma coisa. 

![entidade Simple](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>JSON de exemplo

`Bob Jones wants 3 meatball pho`

Na expressão anterior, `Bob Jones` é identificado como um simples `Customer` entidade.

Os dados devolvidos a partir do ponto final incluem o nome da entidade, o texto detetado da expressão, a localização do texto detetado e a pontuação:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Resposta de ponto de extremidade de previsão v2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Resposta de ponto de extremidade de previsão v3](#tab/V3)

Esse é o JSON se `verbose=false` for definido na cadeia de caracteres de consulta:

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
|Entidade Simple|`Customer`|`bob jones`|

## <a name="next-steps"></a>Passos seguintes

Neste [tutorial](luis-quickstart-primary-and-secondary-data.md), extraia os dados aprendidos por máquina de nome do trabalho de emprego de um expressão usando a **entidade simples**. Para aumentar a precisão de extração, adicione uma [lista de frases](luis-concept-feature.md) de termos específicos à entidade simples.