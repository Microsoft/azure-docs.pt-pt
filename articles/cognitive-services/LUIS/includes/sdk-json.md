---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 08/12/2020
ms.topic: include
ms.custom: include file, devx-track-javascript
ms.author: diberry
ms.openlocfilehash: 7d1c9e396f088fb64c3a71a868cdefee803e4730
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88237432"
---
A resposta de previsão é um objeto JSON, incluindo a intenção e quaisquer entidades encontradas.

```JSON
{
    "query": "I want two small pepperoni pizzas with more salsa",
    "prediction": {
        "topIntent": "OrderPizzaIntent",
        "intents": {
            "OrderPizzaIntent": {
                "score": 0.753606856
            },
            "None": {
                "score": 0.119097039
            }
        },
        "entities": {
            "Pizza order": [
                {
                    "Pizza": [
                        {
                            "Quantity": [
                                2
                            ],
                            "Type": [
                                "pepperoni"
                            ],
                            "Size": [
                                "small"
                            ],
                            "$instance": {
                                "Quantity": [
                                    {
                                        "type": "builtin.number",
                                        "text": "two",
                                        "startIndex": 7,
                                        "length": 3,
                                        "score": 0.968156934,
                                        "modelTypeId": 1,
                                        "modelType": "Entity Extractor",
                                        "recognitionSources": [
                                            "model"
                                        ]
                                    }
                                ],
                                "Type": [
                                    {
                                        "type": "Type",
                                        "text": "pepperoni",
                                        "startIndex": 17,
                                        "length": 9,
                                        "score": 0.9345611,
                                        "modelTypeId": 1,
                                        "modelType": "Entity Extractor",
                                        "recognitionSources": [
                                            "model"
                                        ]
                                    }
                                ],
                                "Size": [
                                    {
                                        "type": "Size",
                                        "text": "small",
                                        "startIndex": 11,
                                        "length": 5,
                                        "score": 0.9592077,
                                        "modelTypeId": 1,
                                        "modelType": "Entity Extractor",
                                        "recognitionSources": [
                                            "model"
                                        ]
                                    }
                                ]
                            }
                        }
                    ],
                    "Toppings": [
                        {
                            "Type": [
                                "salsa"
                            ],
                            "Quantity": [
                                "more"
                            ],
                            "$instance": {
                                "Type": [
                                    {
                                        "type": "Type",
                                        "text": "salsa",
                                        "startIndex": 44,
                                        "length": 5,
                                        "score": 0.7292897,
                                        "modelTypeId": 1,
                                        "modelType": "Entity Extractor",
                                        "recognitionSources": [
                                            "model"
                                        ]
                                    }
                                ],
                                "Quantity": [
                                    {
                                        "type": "Quantity",
                                        "text": "more",
                                        "startIndex": 39,
                                        "length": 4,
                                        "score": 0.9320932,
                                        "modelTypeId": 1,
                                        "modelType": "Entity Extractor",
                                        "recognitionSources": [
                                            "model"
                                        ]
                                    }
                                ]
                            }
                        }
                    ],
                    "$instance": {
                        "Pizza": [
                            {
                                "type": "Pizza",
                                "text": "two small pepperoni pizzas",
                                "startIndex": 7,
                                "length": 26,
                                "score": 0.812199831,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ],
                        "Toppings": [
                            {
                                "type": "Toppings",
                                "text": "more salsa",
                                "startIndex": 39,
                                "length": 10,
                                "score": 0.7250252,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ]
                    }
                }
            ],
            "$instance": {
                "Pizza order": [
                    {
                        "type": "Pizza order",
                        "text": "two small pepperoni pizzas with more salsa",
                        "startIndex": 7,
                        "length": 42,
                        "score": 0.769223332,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```