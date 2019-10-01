---
title: Padrão. qualquer tipo de entidade-LUIS
titleSuffix: Azure Cognitive Services
description: Padrão. any é um espaço reservado de comprimento variável usado somente no modelo de um padrão expressão para marcar onde a entidade começa e termina.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 2239387ffff4c30e1183721a528e666199316bed
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695099"
---
# <a name="patternany-entity"></a>Entidade de Pattern.any 

Padrão. any é um espaço reservado de comprimento variável usado somente no modelo de um padrão expressão para marcar onde a entidade começa e termina.  

Padrão. todas as entidades precisam ser marcadas nos exemplos de modelo de [padrão](luis-how-to-model-intent-pattern.md) , não nos exemplos de usuário de intenção.

**A entidade é uma boa opção quando:**

* O final da entidade pode ser confundido com o texto restante do expressão. 

## <a name="usage"></a>Utilização

Dado um aplicativo cliente que pesquisa livros com base no título, o padrão. qualquer extração do título completo. Um modelo expressão usando o padrão. qualquer para essa pesquisa de livro é `Was {BookTitle} written by an American this year[?]`. 

Na tabela a seguir, cada linha tem duas versões do expressão. A principal expressão é como a LUIS inicialmente vê o expressão. Não fica claro onde o título do livro começa e termina. O expressão inferior usa um padrão. qualquer entidade para marcar o início e o fim da entidade. 

|Expressão com a entidade em negrito|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Foi **o homem que assumiu sua esposa por um chapéu e outras histórias clínicas** escritas por um American este ano?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>A **metade estava em suspensão no Frog Pajamas** escrito por um American este ano?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Foi @no__t tristeza particular do bolo de limão: Um romance @ no__t-0 escrito por um American este ano?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Foi **um wocket em meu bolso!** escrito por um American este ano?|
||



## <a name="example-json"></a>JSON de exemplo

Considere a seguinte consulta:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

Pelo nome do formulário inserido a ser extraído como padrão. any:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Resposta de ponto de extremidade de previsão v2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Resposta de ponto de extremidade de previsão v3](#tab/V3)

Esse é o JSON se `verbose=false` for definido na cadeia de caracteres de consulta:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Esse é o JSON se `verbose=true` for definido na cadeia de caracteres de consulta:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
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

Neste [tutorial](luis-tutorial-pattern-any.md), use o **padrão. qualquer** entidade para extrair dados de declarações em que o declarações está bem formatado e onde o final dos dados pode ser facilmente confundido com as palavras restantes do expressão.