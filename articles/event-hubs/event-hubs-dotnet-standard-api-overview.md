---
title: Visão geral dos Hubs de Eventos Azure .NET Standard APIs [ Microsoft Docs
description: .NET Visão geral padrão da API
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/13/2018
ms.author: shvija
ms.openlocfilehash: b09f39f45936a7c43dbc1ef109780315d62c768f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60821914"
---
# <a name="event-hubs-net-standard-api-overview"></a>Event Hubs .NET Visão geral padrão API

Este artigo resume alguns dos principais Hubs de Eventos Azure [.NET Standard cliente APIs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/). Existem atualmente duas bibliotecas de clientes .NET Standard para Centros de Eventos:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs): Fornece todas as operações básicas de tempo de execução.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor): Adiciona funcionalidade adicional que permite acompanhar os eventos processados, e é a forma mais fácil de ler a partir de um hub de eventos.

## <a name="event-hubs-client"></a>Cliente do Event Hubs

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) é o principal objeto que usa para enviar eventos, criar recetores e obter informações sobre o tempo de execução. Este cliente está ligado a um determinado hub de eventos, e cria uma nova ligação ao ponto final do Event Hubs.

### <a name="create-an-event-hubs-client"></a>Criar um cliente dos Event Hubs

Um objeto [EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) é criado a partir de uma cadeia de ligação. A forma mais simples de instantaneamente um novo cliente é mostrada no seguinte exemplo:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("Event Hubs connection string");
```

Para editar programáticamente a cadeia de ligação, pode utilizar a classe [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) e passar a cadeia de ligação como parâmetro para [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("Event Hubs connection string")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Enviar eventos

Para enviar eventos para um centro de eventos, use a classe [EventData.](/dotnet/api/microsoft.azure.eventhubs.eventdata) O corpo deve `byte` ser uma `byte` matriz, ou um segmento de matriz.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Receber eventos

A forma recomendada de receber eventos do Event Hubs é a utilização do [Anfitrião](#event-processor-host-apis)do Processador de Eventos, que fornece funcionalidade para acompanhar automaticamente as informações de compensação e partilha do centro de eventos. No entanto, existem certas situações em que você pode querer usar a flexibilidade da biblioteca core Event Hubs para receber eventos.

#### <a name="create-a-receiver"></a>Criar um recetor

Os recetores estão ligados a divisórias específicas, por isso, para receber todos os eventos num centro de eventos, deve criar várias instâncias. É uma boa prática obter a informação da partilha programáticamente, em vez de codificar duramente os IDs de partição. Para tal, pode utilizar o método [GetRuntimeInformationAsync.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)

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

Como os eventos nunca são removidos de um centro de eventos (e só expiram), deve especificar o ponto de partida adequado. O exemplo que se segue mostra possíveis combinações:

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

## <a name="event-processor-host-apis"></a>Apis anfitrião do processador de eventos

Estas APIs proporcionam resiliência aos processos dos trabalhadores que podem ficar indisponíveis, distribuindo divisórias entre os trabalhadores disponíveis:

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

Segue-se uma implementação da amostra da interface [IEventProcessor:](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)

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

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os cenários dos Event Hubs, consulte estas ligações:

* [O que são os Hubs de Eventos do Azure?](event-hubs-what-is-event-hubs.md)
* [Apis do Event Hubs disponível](event-hubs-api-overview.md)

As referências .NET API estão aqui:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
