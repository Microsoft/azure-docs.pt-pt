---
title: A azure funciona como manipulador de eventos para eventos Azure Event Grid
description: Descreve como pode usar funções Azure como manipuladores de eventos para eventos de Grade de Eventos.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0152a9fc11562744f83ab9d20466a3dcc8e2e6e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83800419"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>A azure funciona como manipulador de eventos para eventos de Grade de Eventos

Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma uma ação para processar o evento. Vários serviços Azure são configurados automaticamente para lidar com eventos e **as Funções Azure** é um deles. 

Use **funções Azure** numa arquitetura sem servidor para responder a eventos da Grade de Eventos. Quando utilizar uma função Azure como manipulador, utilize o gatilho 'Grelha de Evento' em vez do gatilho GENÉRICO HTTP. A Grelha de Eventos valida automaticamente os gatilhos da Grelha de Eventos. Com os gatilhos GENÉRICOS HTTP, tem de implementar a [resposta de validação](webhook-event-delivery.md) por si mesmo.

Para obter mais informações, consulte [o gatilho da Grelha de Eventos para as Funções Azure](../azure-functions/functions-bindings-event-grid.md) para uma visão geral da utilização do gatilho da Grelha de Eventos em funções.

## <a name="tutorials"></a>Tutoriais

|Título  |Descrição  |
|---------|---------|
| [Quickstart: Lidar com eventos com função](custom-event-to-function.md) | Envia um evento personalizado para uma função de processamento. |
| [Tutorial: automatizar redimensionamento de imagens carregadas usando a Grade de Eventos](resize-images-on-storage-blob-upload-event.md) | Os utilizadores fazem o upload das imagens através da web app para a conta de armazenamento. Quando uma bolha de armazenamento é criada, o Event Grid envia um evento para a aplicação de função, que redimensiona a imagem carregada. |
| [Tutorial: transmitir big data para um armazém de dados](event-grid-event-hubs-integration.md) | Quando o Event Hubs cria um ficheiro Capture, o Event Grid envia um evento para uma aplicação de função. A aplicação recupera o ficheiro Captura e migra dados para um armazém de dados. |
| [Tutorial: Azure Service Bus para Azure Event Grid exemplos de integração](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Grade de Eventos envia mensagens do tópico Service Bus para uma aplicação de função e uma aplicação lógica. |

## <a name="rest-example-for-put"></a>Exemplo DE REPOUSO (para PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Próximos passos
Consulte o artigo [dos manipuladores do Evento](event-handlers.md) para obter uma lista de manipuladores de eventos suportados. 
