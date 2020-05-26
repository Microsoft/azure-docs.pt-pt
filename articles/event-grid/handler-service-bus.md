---
title: Filas de ônibus de serviço e tópicos como manipuladores de eventos para eventos Azure Event Grid
description: Descreve como pode usar as filas e tópicos do Service Bus como manipuladores de eventos para eventos da Rede de Eventos Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: c9e1c9aa664065371595ed34a3af28330bd7e0db
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798868"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Filas de ônibus de serviço e tópicos como manipuladores de eventos para eventos Azure Event Grid
Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma mais medidas para processar o evento. Vários serviços Azure são configurados automaticamente para lidar com eventos e **o Azure Service Bus** é um deles. 

Você pode usar uma fila de serviço ou tópico como manipulador para eventos a partir da Grelha de Eventos. 

## <a name="service-bus-queues"></a>Filas do Service Bus
Pode encaminhar eventos em Event Grid diretamente para as filas de ônibus de serviço para utilização em cenários de tampão ou comando & de controlo em aplicações empresariais.

No portal Azure, ao criar uma subscrição de evento, selecione **Service Bus Queue** como tipo de ponto final e, em seguida, clique em selecionar um ponto **final** para escolher uma fila de ônibus de serviço.

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Usando o CLI para adicionar um manipulador de fila de ônibus de serviço

Para o Azure CLI, o seguinte exemplo subscreve e liga um tópico da grelha de eventos a uma fila de ônibus de serviço:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Tópicos de ônibus de serviço

Você pode direcionar eventos em Event Grid diretamente para tópicos de ônibus de serviço para lidar com eventos do sistema Azure com tópicos de ônibus de serviço, ou para comandar & controlar cenários de mensagens.

No portal Azure, ao criar uma subscrição de evento, selecione **Service Bus Topic** como tipo de ponto final e, em seguida, clique em selecionar e ponto **final** para escolher um tópico de Ônibus de serviço.

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Usando o CLI para adicionar um manipulador de tópicos de ônibus de serviço

Para o Azure CLI, o seguinte exemplo subscreve e liga um tópico da grelha de eventos a uma fila de ônibus de serviço:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="message-properties"></a>Propriedades da mensagem
Se utilizar um **tópico de ônibus de serviço ou uma fila** como manipulador de eventos para eventos a partir da Grelha de Eventos, detete os seguintes cabeçalhos de mensagem: 

| Nome da propriedade | Descrição |
| ------------- | ----------- | 
| aeg-subscrição-nome | Nome da subscrição do evento. |
| aeg-entrega-contagem | <p>Número de tentativas feitas para o evento.</p> <p>Exemplo: "1"</p> |
| aeg-tipo evento | <p>Tipo de evento.</p><p> Exemplo: "Microsoft.Storage.blobCreated"</p> | 
| aeg-metadados-versão | <p>Versão metadados do evento.</p> <p>Exemplo: "1".</p><p> Para o **event grid schema**, esta propriedade representa a versão metadados e para o esquema de **eventos em nuvem,** representa a **versão de especificação**. </p>|
| aeg-data-versão | <p>Versão de dados do evento.</p><p>Exemplo: "1".</p><p>Para o **event grid schema**, esta propriedade representa a versão de dados e para o esquema de **eventos na nuvem**, não se aplica.</p> |

## <a name="message-headers"></a>Cabeçalhos de mensagem
Ao enviar um evento para uma fila de ônibus de serviço ou tópico como uma mensagem intermediada, a `messageid` mensagem intermediada é o **ID**do evento .

O ID do evento será mantido em toda a reentrega do evento para que possa evitar entregas duplicadas, ligando a **deteção duplicada** na entidade de ônibus de serviço. Recomendamos que permita que a duração da deteção duplicada na entidade do Ônibus de serviço seja o tempo de vida (TTL) do evento ou a duração máxima de retry, o que for mais longo.

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
Consulte o artigo de manipuladores de [eventos](event-handlers.md) para obter uma lista de manipuladores de eventos suportados. 
