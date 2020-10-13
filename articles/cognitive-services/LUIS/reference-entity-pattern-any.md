---
title: Padrão.qualquer tipo de entidade - LUIS
titleSuffix: Azure Cognitive Services
description: Padrão.qualquer um é um espaço reservado de comprimento variável usado apenas na expressão do modelo de um padrão para marcar onde a entidade começa e termina.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.openlocfilehash: ec23be3709cebc534c059a21c52452abff683b18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542211"
---
# <a name="patternany-entity"></a>Entidade Pattern.any

Padrão.qualquer um é um espaço reservado de comprimento variável usado apenas na expressão do modelo de um padrão para marcar onde a entidade começa e termina.  

Padrão.qualquer entidade precisa ser marcada nos exemplos do modelo [Padrão,](luis-how-to-model-intent-pattern.md) não nos exemplos do utilizador intencional.

**A entidade é um bom ajuste quando:**

* O fim da entidade pode ser confundido com o restante texto da expressão.

## <a name="usage"></a>Utilização

Dada a aplicação do cliente que procura livros com base no título, o padrão.qualquer um extrai o título completo. Uma expressão de modelo usando padrão.qualquer para esta pesquisa de livro é `Was {BookTitle} written by an American this year[?]` .

Na tabela seguinte, cada linha tem duas versões da expressão. A expressão de cima é como LUIS inicialmente vê a expressão. Não é claro onde o título do livro começa e termina. A expressão inferior usa um Padrão.qualquer entidade para marcar o início e o fim da entidade.

|Expressão com entidade em negrito|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>O **homem que confundiu a sua mulher com um chapéu e outros contos clínicos** escritos por um americano este ano?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>O **Half Asleep in Frog Pijama foi** escrito por um americano este ano?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Foi **A Tristeza Particular do Bolo de Limão: Um romance** escrito por um americano este ano?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>**Havia um Wocket no meu bolso!** escrito por um americano este ano?|
||



## <a name="example-json"></a>Exemplo JSON

Considere a consulta seguinte:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

Com o nome incorporado para extrair como um Padrão.any:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[Resposta do ponto final da previsão V2](#tab/V2)

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


#### <a name="v3-prediction-endpoint-response"></a>[Resposta do ponto final de previsão V3](#tab/V3)

Este é o JSON se `verbose=false` for definido na cadeia de consulta:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Este é o JSON se `verbose=true` for definido na cadeia de consulta:

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

Neste [tutorial](luis-tutorial-pattern.md), utilize o **Padrão.qualquer** entidade para extrair dados de expressões onde as expressões são bem formatadas e onde o fim dos dados pode ser facilmente confundido com as restantes palavras da expressão.
