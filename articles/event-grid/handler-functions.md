---
title: Azure funciona como manipulador de eventos para eventos da Grelha de Eventos Azure
description: Descreve como pode utilizar as funções do Azure como manipuladores de eventos para eventos da Grelha de Eventos.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0152a9fc11562744f83ab9d20466a3dcc8e2e6e0
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800419"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Azure funciona como manipulador de eventos para eventos da Grelha de Eventos

Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma uma ação para processar o evento. Vários serviços Azure são configurados automaticamente para lidar com eventos e **as Funções Azure** são um deles. 

Utilize **funções Azure** numa arquitetura sem servidores para responder a eventos da Grelha de Eventos. Quando utilizar uma função Azure como manipulador, utilize o gatilho da Grelha de Eventos em vez do gatilho HTTP genérico. A Grelha de Eventos valida automaticamente os gatilhos da Grelha de Eventos. Com os gatilhos genéricos do HTTP, deve implementar a resposta de [validação](webhook-event-delivery.md) por si mesmo.

Para mais informações, consulte o gatilho da Grelha de [Eventos para funções Azure](../azure-functions/functions-bindings-event-grid.md) para obter uma visão geral da utilização do gatilho da Grelha de Eventos nas funções.

## <a name="tutorials"></a>Tutoriais

|Título  |Descrição  |
|---------|---------|
| [Quickstart: Lidar com eventos com função](custom-event-to-function.md) | Envia um evento personalizado para uma função de processamento. |
| [Tutorial: automatizar imagens carregadas usando a Grelha de Eventos](resize-images-on-storage-blob-upload-event.md) | Os utilizadores fazem o upload de imagens através da aplicação web para a conta de armazenamento. Quando uma bolha de armazenamento é criada, a Event Grid envia um evento para a aplicação de funções, que redimensiona a imagem carregada. |
| [Tutorial: transmitir big data em um armazém de dados](event-grid-event-hubs-integration.md) | Quando o Event Hubs cria um ficheiro Capture, a Rede de Eventos envia um evento para uma aplicação de função. A aplicação recupera o ficheiro Capture e migra dados para um armazém de dados. |
| [Tutorial: Ônibus de serviço azure para exemplos de integração da Rede de Eventos Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Event Grid envia mensagens do tópico do Service Bus para uma aplicação de função e uma aplicação lógica. |

## <a name="rest-example-for-put"></a>Exemplo de REPOUSO (para PUT)

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

## <a name="next-steps"></a>Passos seguintes
Consulte o artigo de manipuladores de [eventos](event-handlers.md) para obter uma lista de manipuladores de eventos suportados. 
