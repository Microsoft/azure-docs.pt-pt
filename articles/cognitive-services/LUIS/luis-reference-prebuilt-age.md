---
title: Entidade pré-criados de idade
titleSuffix: Azure
description: Este artigo contém idade informações da entidade pré-criados na compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: c901a384d7629a187a2a4fbd4295a1a68615abdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712802"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Idade entidade criados previamente para uma aplicação LUIS
A entidade de idade pré-criados captura o valor de idade tanto numericamente e em termos de dias, semanas, meses e anos. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém a idade para os objetivos do aplicativo. Entidade de idade é suportada no [várias culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Tipos de idade
Idade é gerenciada a partir da [reconhecedores texto](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) repositório do GitHub

## <a name="resolution-for-prebuilt-age-entity"></a>Resolução de entidades de idade pré-criados
O exemplo seguinte mostra a resolução do **builtin.age** entidade.

```json
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8236133
    }
  ],
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [moeda](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), e [dimensão](luis-reference-prebuilt-dimension.md) entidades. 
