---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: e14f29593d588a8b13162b374372b723c50db8e9
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42920396"
---
O `response` matriz para adicionar as expressões de exemplo indica êxito ou falha para cada ocorrência de pronunciação de exemplo com o `hasError` propriedade. A resposta JSON seguinte mostra que ambas as expressões de com foram adicionados com êxito. 

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle today",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

O JSON seguinte mostra o resultado de um pedido para preparar com êxito:

```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

O JSON seguinte mostra o resultado de um pedido com êxito para o estado de treinamento. Cada modelID é um objetivo. Cada objetivo deve ser treinados em todas as expressões para identificar corretamente a expressões com pertencem à intenção, bem como expressões que não pertencem à intenção. 

```JSON
[
    {
        "modelId": "0c694cf9-8c32-44b8-9ea0-3d30a7d901ca",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "10e53836-ade4-494e-9531-3bd6a944c510",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "21e48732-a512-4c33-b5ed-8ea629465269",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "edee15b1-9999-45c2-bbab-591d3a643033",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "aa78e06e-df81-4bb2-b2d9-a2fbb2f81c54",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "e39bb7bd-b417-41a9-a24f-caf4c47fc62c",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "3782eac7-db84-4d66-ba00-0598dffb48ee",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "a941d926-cb0f-47a8-ab7e-deba4378b96f",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "8137f40e-ce6d-40a5-881f-dfd46a05f7e0",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "dc08f95a-58b4-4064-a210-03fe34f75a3c",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "4fabdbed-5697-4562-8c7d-36e174efff2e",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    }
]
```
