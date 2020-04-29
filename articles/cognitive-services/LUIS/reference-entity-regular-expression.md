---
title: Tipo de entidade de expressão regular - LUIS
titleSuffix: Azure Cognitive Services
description: Uma expressão regular é melhor para texto de expressão crua. Ignora o caso e ignora a variante cultural.  A correspondência de expressão regular é aplicada após alterações de verificação ortográfica ao nível do carácter, e não no nível do símbolo.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: b9da76a80183f353a74d43e667bf6c9219eb6c05
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74841222"
---
# <a name="regular-expression-entity"></a>Entidade de expressão regular

Uma entidade de expressão regular extrai uma entidade com base num padrão de expressão regular que fornece.

Uma expressão regular é melhor para texto de expressão crua. Ignora o caso e ignora a variante cultural.  A correspondência de expressão regular é aplicada após alterações de verificação ortográfica ao nível do carácter, e não no nível do símbolo. Se a expressão regular for demasiado complexa, como usar muitos suportes, não é capaz de adicionar a expressão ao modelo. Usa parte, mas não toda a biblioteca [.NET Regex.](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions)

**A entidade é um bom ajuste quando:**

* Os dados são consistentemente formatados com qualquer variação que também seja consistente.
* A expressão regular não necessita de mais de 2 níveis de nidificação.

![Entidade de expressão regular](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Considerações de utilização

Expressões regulares podem corresponder mais do que espera. Um exemplo disso é a correspondência `one` `two`de palavras numéricas como e . Um exemplo é o seguinte regex, que combina com o número `one` juntamente com outros números:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Esta expressão regex também combina com quaisquer `phone`palavras que terminam com estes números, tais como . Para corrigir problemas como este, certifique-se de que os fósforos regex têm em conta os limites das palavras. O regex para utilizar limites de palavras para este exemplo é utilizado no seguinte regex:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Exemplo JSON

Ao `kb[0-9]{6}`utilizar, como definição de entidade de expressão regular, a seguinte resposta JSON é um exemplo de expressão com as entidades de expressão regular devolvidas para a consulta:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[Resposta final de previsão V2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[Resposta final de previsão V3](#tab/V3)


Este é o JSON se `verbose=false` estiver definido na corda de consulta:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Este é o JSON se `verbose=true` estiver definido na corda de consulta:

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Passos seguintes

Neste [tutorial,](tutorial-regex-entity.md)crie uma app para extrair dados consistentemente formatados de uma expressão utilizando a entidade **De Expressão Regular.**