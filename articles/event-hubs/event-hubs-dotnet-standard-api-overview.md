---
title: Descrição geral dos Hubs de eventos do Azure APIs padrão do .NET | Documentos da Microsoft
description: Descrição geral da API padrão do .NET
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/13/2018
ms.author: shvija
ms.openlocfilehash: b09f39f45936a7c43dbc1ef109780315d62c768f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821914"
---
# <a name="event-hubs-net-standard-api-overview"></a>Descrição geral da API padrão do .NET dos Hubs de eventos

Este artigo resume alguns dos principais dos Hubs de eventos do Azure [APIs de cliente .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/). Existem atualmente duas bibliotecas de cliente .NET Standard dos Hubs de eventos:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs): Fornece todas as operações de tempo de execução básica.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor): Adiciona funcionalidade adicional que permite manter o controle de eventos processados e é a forma mais fácil de ler a partir de um hub de eventos.

## <a name="event-hubs-client"></a>Cliente de Hubs de eventos

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) é o principal objeto de usar para enviar eventos, criar receptores de e para obter informações de tempo de execução. Este cliente está ligado a um hub de eventos específico e cria uma nova ligação para o ponto final de Hubs de eventos.

### <a name="create-an-event-hubs-client"></a>Criar um cliente dos Event Hubs

Uma [EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) objeto é criado a partir de uma cadeia de ligação. A forma mais simples para instanciar um novo cliente é mostrada no exemplo a seguir:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("Event Hubs connection string");
```

Para editar programaticamente a cadeia de ligação, pode utilizar o [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) de classe e passar a cadeia de ligação como um parâmetro para [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("Event Hubs connection string")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Enviar eventos

Para enviar eventos para um hub de eventos, utilize o [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) classe. O corpo tem de ser um `byte` matriz, ou um `byte` segmento de matriz.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Receber eventos

A forma recomendada para receber eventos dos Hubs de eventos está a utilizar o [anfitrião do processador de eventos](#event-processor-host-apis), que fornece funcionalidade para automaticamente controlar as informações de deslocamento e a partição da hub de eventos. No entanto, há determinadas situações em que talvez queira usar a flexibilidade da biblioteca principal do Hubs de eventos para receber eventos.

#### <a name="create-a-receiver"></a>Criar um recetor

Recetores estão associados com partições específicas, por isso, para receber todos os eventos num hub de eventos, tem de criar várias instâncias. É uma boa prática para obter as informações de partição programaticamente, em vez de embutir a IDs de partição. Para tal, pode utilizar o [GetRuntimeInformationAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) método.

```csharp
// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition IDs
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

Como eventos nunca são removidos de um hub de eventos (e apenas expirarem), tem de especificar o ponto de partida adequado. O exemplo seguinte mostra as combinações possíveis:

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Consumir um evento

```csharp
// Receive a maximum of 100 messages in this call to ReceiveAsync
var ehEvents = await receiver.ReceiveAsync(100);
// ReceiveAsync can return null if there are no messages
if (ehEvents != null)
{
    // Since ReceiveAsync can return more than a single event you will need a loop to process
    foreach (var ehEvent in ehEvents)
    {
        // Decode the byte array segment
        var message = UnicodeEncoding.UTF8.GetString(ehEvent.Body.Array);
        // Load the custom property that we set in the send example
        var customType = ehEvent.Properties["Type"];
        // Implement processing logic here
    }
}       
```

## <a name="event-processor-host-apis"></a>APIs de anfitrião do processador de eventos

Essas APIs proporcionam resiliência a processos de trabalho que podem ficar indisponíveis, distribuindo partições em trabalhadores disponíveis:

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.

// Read these connection strings from a secure location
var ehConnectionString = "{Event Hubs connection string}";
var ehEntityPath = "{event hub path/name}";
var storageConnectionString = "{Storage connection string}";
var storageContainerName = "{Storage account container name}";

var eventProcessorHost = new EventProcessorHost(
    ehEntityPath,
    PartitionReceiver.DefaultConsumerGroupName,
    ehConnectionString,
    storageConnectionString,
    storageContainerName);

// Start/register an EventProcessorHost
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

// Disposes the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

Segue-se uma implementação de exemplo do [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) interface:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
        return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
        Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
        return Task.CompletedTask;
    }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
        Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
        return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (var eventData in messages)
        {
            var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
            Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
        }

        return context.CheckpointAsync();
    }
}
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre os cenários dos Event Hubs, consulte estas ligações:

* [O que é o Event Hubs do Azure?](event-hubs-what-is-event-hubs.md)
* [Apis de Hubs de eventos disponíveis](event-hubs-api-overview.md)

As referências de .NET API são aqui:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
