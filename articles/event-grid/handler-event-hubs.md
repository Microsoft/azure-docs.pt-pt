---
title: Centro de eventos como manipulador de eventos para eventos Azure Event Grid
description: Descreve como pode usar os centros de eventos como manipuladores de eventos para eventos Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 446fef6df65f59206519e282c74d59c2ed1bfa9d
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "96005639"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Centro de eventos como manipulador de eventos para eventos Azure Event Grid
Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma uma ação para processar o evento. Vários serviços Azure são automaticamente configurados para lidar com eventos e **a Azure Event Hubs** é um deles. 

Use **Os Centros de Eventos** quando a sua solução obtém eventos da Grade de Eventos mais rapidamente do que pode processar os eventos. Uma vez que os eventos estejam em um centro de eventos, a sua aplicação pode processar eventos a partir do centro de eventos na sua própria agenda. Pode escalar o processamento do evento para lidar com os eventos que chegam.

## <a name="tutorials"></a>Tutoriais
Veja os exemplos seguintes: 

|Título  |Descrição  |
|---------|---------|
| [Quickstart: Encaminhe eventos personalizados para Azure Event Hubs com Azure CLI](custom-event-to-eventhub.md) | Envia um evento personalizado para um centro de eventos para processamento através de uma aplicação. |
| [Modelo de Gestor de Recursos: Crie um tópico personalizado da Grade de Eventos e envie eventos para um centro de eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo de Gestor de Recursos que cria uma subscrição para um tópico personalizado. Envia eventos para um Azure Event Hubs. |

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]


## <a name="rest-examples-for-put"></a>Exemplos de REPOUSO (para PUT)


### <a name="event-hub"></a>Hub de eventos

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "EventHub",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>Centro de eventos - entrega com identidade gerida

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "EventHub",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Consulte o artigo [dos manipuladores do Evento](event-handlers.md) para obter uma lista de manipuladores de eventos suportados. 
