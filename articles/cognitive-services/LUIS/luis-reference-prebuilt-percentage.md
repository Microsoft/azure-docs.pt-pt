---
title: Entidade pré-criados de percentagem
titleSuffix: Azure
description: Este artigo contém a percentagem de informações da entidade pré-criados na compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2018
ms.author: diberry
ms.openlocfilehash: 7f2808913178eb1d9a3de78b9f4948ee8031e4f4
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214423"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Percentagem de entidade criados previamente para uma aplicação LUIS
Percentagem números podem aparecer como frações, `3 1/2`, ou como percentagem, `2%`. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém a percentagem para os objetivos do aplicativo. Percentagem de entidade é suportada no [várias culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Tipos de percentagem
Percentagem é gerenciada a partir da [reconhecedores texto](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) repositório do GitHub

## <a name="resolution-for-prebuilt-percentage-entity"></a>Resolução de entidade de percentagem pré-criados
O exemplo seguinte mostra a resolução do **builtin.percentage** entidade.

```json
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    }
  ],
  "entities": [
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [ordinal](luis-reference-prebuilt-ordinal.md), [número](luis-reference-prebuilt-number.md), e [temperatura](luis-reference-prebuilt-temperature.md) entidades. 
