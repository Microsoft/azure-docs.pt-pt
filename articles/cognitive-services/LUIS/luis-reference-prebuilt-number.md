---
title: Entidade Pré-construída número - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidades pré-construídas em Compreensão linguística (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 85e3589d7467691e2b9a11879510ab980bbd875a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273470"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Número de entidade pré-construída para uma app LUIS
Há muitas maneiras pelas quais os valores numéricos são usados para quantificar, expressar e descrever pedaços de informação. Este artigo abrange apenas alguns dos exemplos possíveis. Luis interpreta as variações nas expressões dos utilizadores e devolve valores numéricos consistentes. Como esta entidade já está treinada, não precisa de adicionar declarações de exemplo contendo número às intenções de aplicação.

## <a name="types-of-number"></a>Tipos de número
O número é gerido a partir do repositório GitHub [de texto de reconhecimento](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml)

## <a name="examples-of-number-resolution"></a>Exemplos de resolução de números

| Expressão        | Entidade   | Resolução |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      |
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


A LUIS inclui o **`builtin.number`** valor `resolution` reconhecido de uma entidade no campo da resposta da JSON que devolve.

## <a name="resolution-for-prebuilt-number"></a>Resolução para número pré-construído

Os seguintes objetos de entidade são devolvidos para consulta:

`order two dozen eggs`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O seguinte JSON `verbose` está com `false`o parâmetro definido para:

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)

O seguinte JSON `verbose` está com `true`o parâmetro definido para:

```json
"entities": {
    "number": [
        24
    ],
    "$instance": {
        "number": [
            {
                "type": "builtin.number",
                "text": "two dozen",
                "startIndex": 6,
                "length": 9,
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

O exemplo que se segue mostra uma resposta da JSON da LUIS, que inclui a resolução do valor 24, para a expressão "duas dezenas".

```json
"entities": [
  {
    "entity": "two dozen",
    "type": "builtin.number",
    "startIndex": 6,
    "endIndex": 14,
    "resolution": {
      "subtype": "integer",
      "value": "24"
    }
  }
]
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

Conheça a [moeda,](luis-reference-prebuilt-currency.md) [ordinal](luis-reference-prebuilt-ordinal.md)e [percentagem](luis-reference-prebuilt-percentage.md).
