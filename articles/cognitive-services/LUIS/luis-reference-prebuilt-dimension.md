---
title: Dimensão Entidades Pré-construídas - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidade pré-criados na compreensão de idiomas (LUIS) de dimensão.
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 3c7bebb38d0331c76eeaaa1d9d6464c568a61b83
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270639"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída dimensão para uma app LUIS
A entidade de dimensão pré-criados detecta vários tipos de dimensões, independentemente da cultura de aplicação do LUIS. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém as dimensões para os objetivos do aplicativo. A entidade dimensão é apoiada em [muitas culturas.](luis-reference-prebuilt-entities.md)

## <a name="types-of-dimension"></a>Tipos de dimensão

A dimensão é gerida a partir do repositório GitHub [de texto reconheço.](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml)

## <a name="resolution-for-dimension-entity"></a>Resolução de entidades de dimensão

Os seguintes objetos de entidade são devolvidos para consulta:

`10 1/2 miles of cable`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON está com o parâmetro `verbose` definido para `false`:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O seguinte JSON está com o parâmetro `verbose` definido para `true`:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ],
    "$instance": {
        "dimension": [
            {
                "type": "builtin.dimension",
                "text": "10 1/2 miles",
                "startIndex": 0,
                "length": 12,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[Resposta V2](#tab/V2)

O exemplo seguinte mostra a resolução da entidade **builtin.dimension.**

```json
{
    "entity": "10 1/2 miles",
    "type": "builtin.dimension",
    "startIndex": 0,
    "endIndex": 11,
    "resolution": {
    "unit": "Mile",
    "value": "10.5"
    }
}
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

Conheça o [e-mail](luis-reference-prebuilt-email.md), [número,](luis-reference-prebuilt-number.md)e entidades [ordinárias.](luis-reference-prebuilt-ordinal.md)
