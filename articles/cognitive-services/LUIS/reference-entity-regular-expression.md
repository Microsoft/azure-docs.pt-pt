---
title: Tipo de entidade de expressão regular - LUIS
description: Uma expressão regular é melhor para texto de expressão crua. Ignora o caso e ignora a variante cultural.  A correspondência de expressão regular é aplicada após alterações de verificação ortográfica ao nível do carácter, e não no nível do símbolo.
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 90260fca10fc087225f6b1286e9fa2dd6d17c836
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585611"
---
# <a name="regular-expression-entity"></a>Entidade de expressão regular

Uma entidade de expressão regular extrai uma entidade com base num padrão de expressão regular que fornece.

Uma expressão regular é melhor para texto de expressão crua. Ignora o caso e ignora a variante cultural.  A correspondência de expressão regular é aplicada após alterações de verificação ortográfica ao nível do carácter, e não no nível do símbolo. Se a expressão regular for demasiado complexa, como usar muitos suportes, não é capaz de adicionar a expressão ao modelo. Usa parte, mas não toda a biblioteca [.NET Regex.](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions)

**A entidade é um bom ajuste quando:**

* Os dados são consistentemente formatados com qualquer variação que também seja consistente.
* A expressão regular não necessita de mais de 2 níveis de nidificação.

![Entidade de expressão regular](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Considerações de utilização

Expressões regulares podem corresponder mais do que espera. Um exemplo disso é a correspondência de palavras numéricas como `one` e `two` . Um exemplo é o seguinte regex, que combina com o número `one` juntamente com outros números:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Esta expressão regex também combina com quaisquer palavras que terminam com estes números, tais como `phone` . Para corrigir problemas como este, certifique-se de que os fósforos regex têm em conta os limites das palavras. O regex para utilizar limites de palavras para este exemplo é utilizado no seguinte regex:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Exemplo JSON

Ao `kb[0-9]{6}` utilizar, como definição de entidade de expressão regular, a seguinte resposta JSON é um exemplo de expressão com as entidades de expressão regular devolvidas para a consulta:

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


Este é o JSON se estiver definido na corda de `verbose=false` consulta:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Este é o JSON se estiver definido na corda de `verbose=true` consulta:

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

Saiba mais sobre as entidades:

* [Conceitos](luis-concept-entity-types.md)
* [Como criar](luis-how-to-add-entities.md)