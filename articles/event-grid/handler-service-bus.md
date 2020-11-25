---
title: Filas de autocarros de serviço e tópicos como manipuladores de eventos para eventos Azure Event Grid
description: Descreve como pode usar filas e tópicos de Service Bus como manipuladores de eventos para eventos Azure Event Grid.
ms.topic: conceptual
ms.date: 09/03/2020
ms.openlocfilehash: 2b18009f8fb31f1a5f057c7395781f63f182847f
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "96024219"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Filas de autocarros de serviço e tópicos como manipuladores de eventos para eventos Azure Event Grid
Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma mais algumas medidas para processar o evento. Vários serviços da Azure são automaticamente configurados para lidar com eventos e **a Azure Service Bus** é um deles. 

Pode utilizar uma fila de serviço ou tópico como manipulador para eventos da Grade de Eventos. 

## <a name="service-bus-queues"></a>Filas do Service Bus
Você pode encaminhar eventos em Event Grid diretamente para as filas de Service Bus para uso em buffering ou comando & cenários de controlo em aplicações da empresa.

No portal Azure, ao criar uma subscrição de eventos, selecione **a Service Bus Queue** como tipo ponto final e, em seguida, clique em selecionar um ponto **final** para escolher uma fila de ônibus de serviço.

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Utilizar o CLI para adicionar um manipulador de fila de autocarros de serviço

Para o Azure CLI, o seguinte exemplo subscreve e liga um tópico de grelha de eventos a uma fila de autocarros de serviço:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Tópicos do Service Bus

Você pode encaminhar eventos em Event Grid diretamente para tópicos de Service Bus para lidar com eventos do sistema Azure com tópicos de Service Bus, ou para o comando & controlar cenários de mensagens.

No portal Azure, ao criar uma subscrição de eventos, selecione **Service Bus Topic** como tipo de ponto final e, em seguida, clique em selecionar e ponto **final** para escolher um tópico de Service Bus.

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Utilizar o CLI para adicionar um manipulador de tópicos de ônibus de serviço

Para o Azure CLI, o seguinte exemplo subscreve e liga um tópico de grelha de eventos a uma fila de autocarros de serviço:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

Ao enviar um evento para uma fila de Autocarro de Serviço ou tópico como uma mensagem intermediada, `messageid` a mensagem intermediada é um ID do sistema interno.

O ID do sistema interno para a mensagem será mantido em toda a reentrega do evento para que possa evitar entregas duplicadas, ligando a **deteção duplicada** na entidade de autocarros de serviço. Recomendamos que permita que a duração da deteção duplicada na entidade Service Bus seja o tempo de vida (TTL) do evento ou a duração máxima da relistência, o que for mais longo.

## <a name="rest-examples-for-put"></a>Exemplos de REPOUSO (para PUT)

### <a name="service-bus-queue"></a>Fila do Service Bus

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>Fila de ônibus de serviço - entrega com identidade gerida

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
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>Tópico do Service Bus

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>Tópico de ônibus de serviço - entrega com identidade gerida

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
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Consulte o artigo [dos manipuladores do Evento](event-handlers.md) para obter uma lista de manipuladores de eventos suportados. 
