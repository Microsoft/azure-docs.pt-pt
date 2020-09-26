---
title: Tipo de entidade de expressão regular - LUIS
description: Uma expressão regular é melhor para texto de expressão crua. Ignora o caso e ignora a variante cultural.  A correspondência regular da expressão é aplicada após alterações de verificação ortográfica ao nível do carácter, não ao nível do símbolo.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 0c8d4a5013f23a805c22dc5c44444de6b10ab369
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316244"
---
# <a name="regular-expression-entity"></a>Entidade de expressão regular

Uma entidade de expressão regular extrai uma entidade com base num padrão de expressão regular que fornece.

Uma expressão regular é melhor para texto de expressão crua. Ignora o caso e ignora a variante cultural.  A correspondência regular da expressão é aplicada após alterações de verificação ortográfica ao nível do carácter, não ao nível do símbolo. Se a expressão regular for demasiado complexa, como utilizar muitos suportes, não é capaz de adicionar a expressão ao modelo. Utiliza parte, mas não toda a biblioteca [.NET Regex.](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions)

**A entidade é um bom ajuste quando:**

* Os dados são consistentemente formatados com qualquer variação que também seja consistente.
* A expressão regular não precisa de mais de 2 níveis de nidificação.

![Entidade de expressão regular](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Considerações de utilização

Expressões regulares podem corresponder mais do que espera. Um exemplo disso é a palavra numérica que combina como `one` e `two` . Um exemplo é o seguinte regex, que corresponde ao número `one` juntamente com outros números:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Esta expressão regex também corresponde a quaisquer palavras que terminam com estes números, tais como `phone` . Para corrigir problemas como este, certifique-se de que os jogos da Regex têm em conta os limites das palavras. O regex para usar limites de palavras para este exemplo é usado no seguinte regex:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Exemplo JSON

Ao `kb[0-9]{6}` utilizar, como definição regular de entidade de expressão, a seguinte resposta JSON é um exemplo de expressão com as entidades de expressão regular devolvidas para a consulta:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[Resposta do ponto final da previsão V2](#tab/V2)

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


#### <a name="v3-prediction-endpoint-response"></a>[Resposta do ponto final de previsão V3](#tab/V3)


Este é o JSON se `verbose=false` for definido na cadeia de consulta:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Este é o JSON se `verbose=true` for definido na cadeia de consulta:

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