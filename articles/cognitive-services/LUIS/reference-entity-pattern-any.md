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
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: cda6c724a36a73dc34c2bf8e7158e3e3ec92d46b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563219"
---
# <a name="patternany-entity"></a>Entidade de Pattern.any 

Padrão. any é um espaço reservado de comprimento variável usado somente no modelo de um padrão expressão para marcar onde a entidade começa e termina.  

Padrão. todas as entidades precisam ser marcadas nos exemplos de modelo de [padrão](luis-how-to-model-intent-pattern.md) , não nos exemplos de usuário de intenção.

**A entidade é uma boa opção quando:**

* O final da entidade pode ser confundido com o texto restante do expressão. 

## <a name="usage"></a>Utilização

Dado um aplicativo cliente que pesquisa livros com base no título, o padrão. qualquer extração do título completo. Um modelo expressão usando o padrão. qualquer para essa pesquisa de `Was {BookTitle} written by an American this year[?]`livro é. 

Na tabela a seguir, cada linha tem duas versões do expressão. A principal expressão é como a LUIS inicialmente vê o expressão. Não fica claro onde o título do livro começa e termina. O expressão inferior usa um padrão. qualquer entidade para marcar o início e o fim da entidade. 

|Expressão com a entidade em negrito|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Foi **o homem que assumiu sua esposa por um chapéu e outras histórias clínicas** escritas por um American este ano?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>A **metade estava em suspensão no Frog Pajamas** escrito por um American este ano?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Foi **o tristeza particular do bolo de limão: Um romance** escrito por um American este ano?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Foi **um wocket em meu bolso!** escrito por um American este ano?|
||

## <a name="example-json"></a>JSON de exemplo

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```

## <a name="next-steps"></a>Passos Seguintes

Neste [tutorial](luis-tutorial-pattern-any.md), use o **padrão. qualquer** entidade para extrair dados de declarações em que o declarações está bem formatado e onde o final dos dados pode ser facilmente confundido com as palavras restantes do expressão.