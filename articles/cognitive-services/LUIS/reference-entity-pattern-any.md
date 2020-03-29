---
title: Padrão.qualquer tipo de entidade - LUIS
titleSuffix: Azure Cognitive Services
description: Pattern.any é um espaço reservado de comprimento variável usado apenas na expressão do modelo de um padrão para marcar onde a entidade começa e termina.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 5164bf55ef8233cf34a470524da3bc852678d79a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75979170"
---
# <a name="patternany-entity"></a>Entidade Pattern.any

Pattern.any é um espaço reservado de comprimento variável usado apenas na expressão do modelo de um padrão para marcar onde a entidade começa e termina.  

Padrão.qualquer entidade precisa de ser marcada nos exemplos do modelo [Padrão,](luis-how-to-model-intent-pattern.md) não nos exemplos de utilizadores intencionais.

**A entidade é um bom ajuste quando:**

* O final da entidade pode ser confundido com o texto restante da expressão.

## <a name="usage"></a>Utilização

Dada uma aplicação de cliente que procura livros com base no título, o padrão.qualquer extrai o título completo. Uma expressão de modelo usando o padrão.qualquer para esta pesquisa de livro é `Was {BookTitle} written by an American this year[?]`.

Na tabela seguinte, cada linha tem duas versões da expressão. A expressão superior é como LUIS vê inicialmente a expressão. Não está claro onde o título do livro começa e termina. A expressão inferior usa um Padrão.qualquer entidade para marcar o início e o fim da entidade.

|Proclamação com entidade em negrito|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>O homem que confundiu a **sua mulher com um chapéu e outros contos clínicos** foi escrito por um americano este ano?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>**Half Asleep in Frog Pasjamas** foi escrito por um americano este ano?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Foi **a tristeza particular do bolo de limão: um romance** escrito por um americano este ano?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Estava **lá um Wocket no meu bolso!** escrito por um americano este ano?|
||



## <a name="example-json"></a>Exemplo JSON

Considere a consulta seguinte:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

Com o nome de formulário incorporado para extrair como padrão.qualquer:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[Resposta final de previsão V2](#tab/V2)

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


#### <a name="v3-prediction-endpoint-response"></a>[Resposta final de previsão V3](#tab/V3)

Este é o JSON se `verbose=false` estiver definido na corda de consulta:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Este é o JSON se `verbose=true` estiver definido na corda de consulta:

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

Neste [tutorial,](luis-tutorial-pattern.md)utilize o **Padrão.qualquer** entidade para extrair dados de expressões onde as expressões estão bem formatadas e onde o fim dos dados pode ser facilmente confundido com as restantes palavras da expressão.
