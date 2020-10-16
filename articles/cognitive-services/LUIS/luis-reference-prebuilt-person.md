---
title: PersonName entidade pré-construída - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidade pré-incorporadas no nome de pessoas em Compreensão de Línguas (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.openlocfilehash: 7b0153d79aaf7b88fea958ab36183e57b41af204
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535428"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>PersonName entidade pré-incorporada para uma aplicação LUIS
A pessoa pré-construída A entidade nome de nome deteta nomes de pessoas. Como esta entidade já está treinada, não precisa de adicionar palavras de exemplo que contenham pessoaName às intenções da aplicação. personAme entidade é apoiada em [culturas](luis-reference-prebuilt-entities.md)inglesa e chinesa.

## <a name="resolution-for-personname-entity"></a>Resolução para pessoaSAme entidade

Os seguintes objetos de entidade são devolvidos para consulta:

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[Resposta V3](#tab/V3)


O seguinte JSON é com o `verbose` parâmetro definido `false` para:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O seguinte JSON é com o `verbose` parâmetro definido `true` para:

```json
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
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[Resposta V2](#tab/V2)

O exemplo a seguir mostra a resolução da entidade **builtin.personName.**

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final de [previsão V3](luis-migration-api-v3.md).

Saiba mais sobre o [email,](luis-reference-prebuilt-email.md) [número](luis-reference-prebuilt-number.md)e entidades [ordinais.](luis-reference-prebuilt-ordinal.md)
