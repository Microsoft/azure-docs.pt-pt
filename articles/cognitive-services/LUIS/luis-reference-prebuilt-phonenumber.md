---
title: Entidades predefinidas de número de telefone – LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidade pré-criados numéricas na compreensão de idiomas (LUIS) do telefone.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a640412fb98fb1d4215bd34c205d4fba253cd7e0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560165"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Entidade de número de telefone predefinida para um aplicativo LUIS
O `phonenumber` entidade extrai uma variedade de números de telefone, incluindo o indicativo de país. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo para a aplicação. O `phonenumber` entidade é suportada no `en-us` apenas a cultura. 

## <a name="types-of-a-phone-number"></a>Tipos de um número de telefone
`Phonenumber`é gerenciado do repositório GitHub de [texto](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) de reconhecedores

## <a name="resolution-for-this-prebuilt-entity"></a>Resolução para esta entidade predefinida

### <a name="api-version-2x"></a>Versão da API 2. x

O exemplo seguinte mostra a resolução do **builtin.phonenumber** entidade.

```json
{
  "query": "my mobile is 1 (800) 642-7676",
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
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Versão prévia da API 3. x

O JSON a seguir é com `verbose` o parâmetro definido `false`como:

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ]
        }
    }
}
```

O JSON a seguir é com `verbose` o parâmetro definido `true`como:

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ],
            "$instance": {
                "phonenumber": [
                    {
                        "type": "builtin.phonenumber",
                        "text": "1 (800) 642-7676",
                        "startIndex": 13,
                        "length": 16,
                        "score": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [percentagem](luis-reference-prebuilt-percentage.md), [número](luis-reference-prebuilt-number.md), e [temperatura](luis-reference-prebuilt-temperature.md) entidades. 
