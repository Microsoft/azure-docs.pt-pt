---
title: Fila de armazenamento como manipulador de eventos para eventos Azure Event Grid
description: Descreve como pode usar as filas de armazenamento Azure como manipuladores de eventos para eventos Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: f62f2b5bc01518af29bd1deb17a38e9fe105a4ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83800562"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>Fila de armazenamento como manipulador de eventos para eventos Azure Event Grid
Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma mais algumas medidas para processar o evento. Vários serviços Azure são configurados automaticamente para lidar com eventos e **o Azure Queue Storage** é um deles. 

Utilize **o Armazenamento de Fila** para receber eventos que precisam de ser puxados. Pode utilizar o armazenamento da fila quando tiver um processo de execução longo que demora muito tempo a responder. Ao enviar eventos para o armazenamento da fila, a aplicação pode puxar e processar eventos na sua própria programação.

## <a name="tutorials"></a>Tutoriais
Consulte o seguinte tutorial para um exemplo de utilização do armazenamento da fila como manipulador de eventos. 

|Título  |Descrição  |
|---------|---------|
| [Quickstart: encaminhar eventos personalizados para o armazenamento da Fila Azure com Azure CLI e Grade de Eventos](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um armazenamento de fila. |

## <a name="rest-examples-for-put"></a>Exemplos de REPOUSO (para PUT)

### <a name="storage-queue-as-the-event-handler"></a>Fila de armazenamento como o manipulador de eventos

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>",
                "queueName": "<QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="storage-queue-as-the-event-handler---delivery-with-managed-identity"></a>Fila de armazenamento como o manipulador de eventos - entrega com identidade gerida

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "StorageQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>",
                    "queueName": "<QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="storage-queue-as-a-deadletter-destination"></a>Fila de armazenamento como destino de deadletter

```json
{
    "name": "",
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DESTINATION STORAGE>",
                "queueName": "queue1"
            }
        },
        "eventDeliverySchema": "EventGridSchema",
        "deadLetterDestination": 
        {
            "endpointType": "StorageBlob",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DEADLETTER STORAGE>",
                "blobContainerName": "test"
            }
        }
    }
}
```

### <a name="storage-queue-as-a-deadletter-destination---managed-identity"></a>Fila de armazenamento como destino de deadletter - identidade gerida

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DESTINATION STORAGE>",
                "queueName": "queue1"
            }
        },
        "eventDeliverySchema": "EventGridSchema",
        "deadLetterWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "deadLetterDestination": 
            {
                "endpointType": "StorageBlob",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DEADLETTER STORAGE>",
                    "blobContainerName": "test"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Próximos passos
Consulte o artigo [dos manipuladores do Evento](event-handlers.md) para obter uma lista de manipuladores de eventos suportados. 
