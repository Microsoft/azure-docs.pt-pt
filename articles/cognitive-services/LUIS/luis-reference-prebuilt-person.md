---
title: PersonName pré-criados de entidade
titleSuffix: Azure Cognitive Services
description: Este artigo contém personName informações da entidade pré-criados na compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 7b748c507d5c848cc83a8a0c55cb7b05903bc542
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958537"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>PersonName entidade criados previamente para uma aplicação LUIS
A entidade de personName pré-criados Deteta os nomes de pessoas. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém personName para os objetivos do aplicativo. entidade de personName é suportada em inglês e chinês [culturas](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Resolução de entidades de personName
O exemplo seguinte mostra a resolução do **builtin.personName** entidade.

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  }
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [e-mail](luis-reference-prebuilt-email.md), [número](luis-reference-prebuilt-number.md), e [ordinal](luis-reference-prebuilt-ordinal.md) entidades. 
