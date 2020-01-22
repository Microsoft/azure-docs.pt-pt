---
title: Visão geral dos hubs de eventos do Azure .NET Framework APIs | Microsoft Docs
description: Este artigo fornece um resumo de alguns dos principais hubs de eventos .NET Framework APIs de cliente (gerenciamento e tempo de execução).
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: f67351fa38543504d63dbf8d86c9537feea24a4f
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76312622"
---
# <a name="event-hubs-net-framework-api-overview"></a>Visão geral da API dos hubs de eventos .NET Framework

Este artigo resume alguns dos principais hubs de eventos do Azure [.NET Framework APIs de cliente](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). Há duas categorias: APIs de gerenciamento e tempo de execução. As APIs de tempo de execução consistem em todas as operações necessárias para enviar e receber uma mensagem. As operações de gerenciamento permitem que você gerencie um estado de entidade de hubs de eventos criando, atualizando e excluindo entidades.

[Cenários de monitoramento](event-hubs-metrics-azure-monitor.md) abrangem gerenciamento e tempo de execução. Para obter a documentação de referência detalhada sobre as APIs do .NET, consulte as referências da API [.NET Framework](/dotnet/api/microsoft.servicebus.messaging.eventhubclient), [.net Standard](/dotnet/api/microsoft.azure.eventhubs)e [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) .

## <a name="management-apis"></a>APIs de gestão

Para executar as seguintes operações de gerenciamento, você deve ter permissões **gerenciar** no namespace de hubs de eventos:

### <a name="create"></a>Create

```csharp
// Create the event hub
var ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
await namespaceManager.CreateEventHubAsync(ehd);
```

### <a name="update"></a>Atualizar

```csharp
var ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
var ruleName = "myeventhubmanagerule";
var ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
await namespaceManager.UpdateEventHubAsync(ehd);
```

### <a name="delete"></a>Eliminar

```csharp
await namespaceManager.DeleteEventHubAsync("event hub name");
```

## <a name="run-time-apis"></a>APIs de tempo de execução
### <a name="create-publisher"></a>Criar Publicador

```csharp
// EventHubClient model (uses implicit factory instance, so all links on same connection)
var eventHubClient = EventHubClient.Create("event hub name");
```

### <a name="publish-message"></a>Publicar mensagem

```csharp
// Create the device/temperature metric
var info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
var data = new EventData(new byte[10]); // Byte array
var data = new EventData(Stream); // Stream 
var data = new EventData(info, serializer) //Object and serializer 
{
    PartitionKey = info.DeviceId.ToString()
};

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>Criar consumidor

```csharp
// Create the Event Hubs client
var eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
var defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index);

// From one day ago
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));

// From specific offset, -1 means oldest
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Consumir mensagem

```csharp
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)

// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>APIs de host do processador de eventos

Essas APIs fornecem resiliência a processos de trabalho que podem se tornar indisponíveis, distribuindo partições entre os trabalhos disponíveis.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

var eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
var blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

var eventHubDescription = new EventHubDescription(EventHubName);
var host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
await host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
await host.UnregisterEventProcessorAsync();
```

A interface [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) é definida da seguinte maneira:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            // Process messages here
        }

        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }

    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os cenários dos Event Hubs, consulte estas ligações:

* [O que é o Event Hubs do Azure?](event-hubs-what-is-event-hubs.md)
* [Guia de programação dos Hubs de Eventos](event-hubs-programming-guide.md)

As referências da API .NET estão aqui:

* [Microsoft.ServiceBus.Messaging](/dotnet/api/microsoft.servicebus.messaging)
* [Microsoft.Azure.EventHubs.EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)
