---
title: Entidades pré-concebidas do URL
titleSuffix: Azure
description: Este artigo contém o url de informações da entidade pré-criados na compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: f9e331abd31ef37e9214214118748ebda3eb9470
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225802"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>URL de entidade criados previamente para uma aplicação LUIS
Entidade de URL extrai URLs com nomes de domínio ou endereços IP. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém os URLs para a aplicação. Entidade de URL é suportada no `en-us` apenas a cultura. 

## <a name="types-of-urls"></a>Tipos de URLs
URL é gerido a partir da [reconhecedores texto](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) repositório do GitHub

## <a name="resolution-for-prebuilt-url-entity"></a>Resolução de entidade pré-criados do URL
O exemplo seguinte mostra a resolução do **builtin.url** entidade.

```json
{
  "query": "http://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "http://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [ordinal](luis-reference-prebuilt-ordinal.md), [número](luis-reference-prebuilt-number.md), e [temperatura](luis-reference-prebuilt-temperature.md) entidades.
