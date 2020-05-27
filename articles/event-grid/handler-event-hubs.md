---
title: Hub de eventos como manipulador de eventos para eventos azure event grid
description: Descreve como pode usar os centros de eventos como manipuladores de eventos para eventos da Rede de Eventos Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: f46b17fdffc870e6afc5f3b0711169db8270a540
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800446"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Hub de eventos como manipulador de eventos para eventos azure event grid
Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma uma ação para processar o evento. Vários serviços Azure são configurados automaticamente para lidar com eventos e **o Azure Event Hubs** é um deles. 

Use Os Centros de **Eventos** quando a sua solução obtém eventos da Rede de Eventos mais rapidamente do que pode processar os eventos. Uma vez que os eventos estejam em um centro de eventos, a sua aplicação pode processar eventos a partir do centro de eventos na sua própria agenda. Pode escalar o processamento do seu evento para lidar com os eventos que estão a chegar.

## <a name="tutorials"></a>Tutoriais
Veja os exemplos seguintes: 

|Título  |Descrição  |
|---------|---------|
| [Quickstart: Rota eventos personalizados para Hubs de Eventos Azure com Azure CLI](custom-event-to-eventhub.md) | Envia um evento personalizado para um centro de eventos para processamento por uma aplicação. |
| [Modelo de Gestor de Recursos: Crie um tópico personalizado da Grelha de Eventos e envie eventos para um hub de eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo de Gestor de Recursos que cria uma subscrição para um tópico personalizado. Envia eventos para um Azure Event Hubs. |

## <a name="message-properties"></a>Propriedades da mensagem
Se utilizar um hub de **eventos** como manipulador de eventos para eventos a partir da Grelha de Eventos, detete os seguintes cabeçalhos de mensagem: 

| Nome da propriedade | Descrição |
| ------------- | ----------- | 
| aeg-subscrição-nome | Nome da subscrição do evento. |
| aeg-entrega-contagem | <p>Número de tentativas feitas para o evento.</p> <p>Exemplo: "1"</p> |
| aeg-tipo evento | <p>Tipo de evento.</p><p> Exemplo: "Microsoft.Storage.blobCreated"</p> | 
| aeg-metadados-versão | <p>Versão metadados do evento.</p> <p>Exemplo: "1".</p><p> Para o **event grid schema**, esta propriedade representa a versão metadados e para o esquema de **eventos em nuvem,** representa a **versão de especificação**. </p>|
| aeg-data-versão | <p>Versão de dados do evento.</p><p>Exemplo: "1".</p><p>Para o **event grid schema**, esta propriedade representa a versão de dados e para o esquema de **eventos na nuvem**, não se aplica.</p> |
| aeg-output-evento-id | ID do evento Event Grid. |

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
Consulte o artigo de manipuladores de [eventos](event-handlers.md) para obter uma lista de manipuladores de eventos suportados. 
