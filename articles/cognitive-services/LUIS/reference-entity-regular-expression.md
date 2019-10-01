---
title: Tipo de entidade de expressão regular-LUIS
titleSuffix: Azure Cognitive Services
description: Uma expressão regular é melhor para texto bruto expressão. Ele ignora caso e ignora a variante cultural.  Correspondência de expressões regulares é aplicada após a verificação ortográfica alterações ao nível do caráter, não no nível de token.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: ae46df875d588186cd083134820f349158d7e307
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695205"
---
# <a name="regular-expression-entity"></a>Entidade de expressão regular 

Uma entidade de expressão regular extrai uma entidade com base em um padrão de expressão regular fornecido por você.

Uma expressão regular é melhor para texto bruto expressão. Ele ignora caso e ignora a variante cultural.  Correspondência de expressões regulares é aplicada após a verificação ortográfica alterações ao nível do caráter, não no nível de token. Se a expressão regular for muito complexa, como usar muitos colchetes, não será possível adicionar a expressão ao modelo. Usa parte, mas não toda a biblioteca [Regex .net](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) . 

**A entidade é uma boa opção quando:**

* Os dados são formatados consistentemente com qualquer variação que também seja consistente.
* A expressão regular não precisa de mais de 2 níveis de aninhamento. 

![Entidade de expressão regular](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Considerações de uso

Expressões regulares podem corresponder a mais do que você espera corresponder. Um exemplo disso é a correspondência de palavras numéricas, como `one` e `two`. Um exemplo é o seguinte Regex, que corresponde ao número `one` juntamente com outros números:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
``` 

Essa expressão Regex também corresponde a quaisquer palavras que terminem com esses números, como `phone`. Para corrigir problemas como esse, verifique se as correspondências de Regex levam em conta limites de palavras. O Regex para usar limites de palavras para este exemplo é usado no seguinte Regex:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>JSON de exemplo

Ao usar `kb[0-9]{6}`, como a definição de entidade de expressão regular, a resposta JSON a seguir é um exemplo de expressão com as entidades de expressão regular retornadas para a consulta:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Resposta de ponto de extremidade de previsão v2](#tab/V2)

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


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Resposta de ponto de extremidade de previsão v3](#tab/V3)


Esse é o JSON se `verbose=false` for definido na cadeia de caracteres de consulta:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Esse é o JSON se `verbose=true` for definido na cadeia de caracteres de consulta:

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

Neste [tutorial](luis-quickstart-intents-regex-entity.md), crie um aplicativo para extrair dados formatados de forma consistente de um expressão usando a entidade **expressão regular** .