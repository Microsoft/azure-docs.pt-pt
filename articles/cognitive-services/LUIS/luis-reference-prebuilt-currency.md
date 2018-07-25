---
title: Referência de moeda de entidades de LUIS pré-criados - Azure | Documentos da Microsoft
titleSuffix: Azure
description: Este artigo contém moeda informações da entidade pré-criados na compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: b383e21f870d15818c540b79a9a56c1dd65fa342
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236827"
---
# <a name="currency-entity"></a>Entidade de moeda
A entidade de moeda pré-criados Deteta a moeda de diferentes denominations e países, independentemente da cultura de aplicação do LUIS. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém a moeda a dos objetivos do aplicativo. Entidade de moeda é suportada no [várias culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Tipos de moeda
Moeda é gerenciada a partir da [reconhecedores texto](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) repositório do Github

## <a name="resolution-for-currency-entity"></a>Resolução de entidades de moeda
O exemplo seguinte mostra a resolução do **builtin.currency** entidade.

```JSON
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimensão](luis-reference-prebuilt-dimension.md), e [e-mail](luis-reference-prebuilt-email.md) entidades. 