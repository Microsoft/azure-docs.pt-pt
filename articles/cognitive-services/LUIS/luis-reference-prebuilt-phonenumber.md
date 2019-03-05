---
title: Entidades de pré-criados de número de telefone
titleSuffix: Azure
description: Este artigo contém informações de entidade pré-criados numéricas na compreensão de idiomas (LUIS) do telefone.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 2a3e84de6d956e4b45945c77c11f789d4455bfe2
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338795"
---
# <a name="phonenumber-prebuilt-entity-for-a-luis-app"></a>PhoneNumber entidade criados previamente para uma aplicação LUIS
O `phonenumber` entidade extrai uma variedade de números de telefone, incluindo o indicativo de país. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo para a aplicação. O `phonenumber` entidade é suportada no `en-us` apenas a cultura. 

## <a name="types-of-phonenumber"></a>Tipos de phonenumber
PhoneNumber é gerido a partir da [reconhecedores texto](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) repositório do GitHub

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Resolução de entidade phonenumber pré-criados
O exemplo seguinte mostra a resolução do **builtin.phonenumber** entidade.

```json
{
  "query": "my mobile is 00 44 161 1234567",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
      "entity": "00 44 161 1234567",
      "type": "builtin.phonenumber",
      "startIndex": 13,
      "endIndex": 29,
      "resolution": {
        "value": "00 44 161 1234567"
      }
    }
  ]
}
```


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [percentagem](luis-reference-prebuilt-percentage.md), [número](luis-reference-prebuilt-number.md), e [temperatura](luis-reference-prebuilt-temperature.md) entidades. 
