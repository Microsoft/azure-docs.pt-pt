---
title: Entidade predefinida Person-LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém personName informações da entidade pré-criados na compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a9da5089c9309ec06f29fd3e0868102cfa538049
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933447"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Entidade predefinida PersonName para um aplicativo LUIS
A entidade de personName pré-criados Deteta os nomes de pessoas. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém personName para os objetivos do aplicativo. entidade de personName é suportada em inglês e chinês [culturas](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Resolução de entidades de personName

### <a name="api-version-2x"></a>Versão da API 2. x

O exemplo seguinte mostra a resolução do **builtin.personName** entidade.

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  },
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

### <a name="preview-api-version-3x"></a>Versão prévia da API 3. x

O JSON a seguir é com `verbose` o parâmetro definido `false`como:

```json
{
    "query": "Is Jill Jones in Cairo?",
    "prediction": {
        "normalizedQuery": "is jill jones in cairo?",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6544678
            }
        },
        "entities": {
            "personName": [
                "Jill Jones"
            ]
        }
    }
}
```

O JSON a seguir é com `verbose` o parâmetro definido `true`como:

```json
{
    "query": "Is Jill Jones in Cairo?",
    "prediction": {
        "normalizedQuery": "is jill jones in cairo?",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6544678
            }
        },
        "entities": {
            "personName": [
                "Jill Jones"
            ],
            "$instance": {
                "personName": [
                    {
                        "type": "builtin.personName",
                        "text": "Jill Jones",
                        "startIndex": 3,
                        "length": 10,
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

Saiba mais sobre o [e-mail](luis-reference-prebuilt-email.md), [número](luis-reference-prebuilt-number.md), e [ordinal](luis-reference-prebuilt-ordinal.md) entidades. 
