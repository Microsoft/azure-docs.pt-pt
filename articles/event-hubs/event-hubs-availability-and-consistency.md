---
title: Disponibilidade e consistência - Hubs de Eventos Azure / Microsoft Docs
description: Como fornecer a máxima disponibilidade e consistência com os Hubs de Eventos Azure utilizando divisórias.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2020
ms.author: shvija
ms.openlocfilehash: 0546adb6131479a8f5d2e7e31819483200586839
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397323"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilidade e consistência em Hubs de Eventos

## <a name="overview"></a>Descrição geral
O Azure Event Hubs usa um [modelo de partição](event-hubs-scalability.md#partitions) para melhorar a disponibilidade e a paralelização dentro de um único centro de eventos. Por exemplo, se um centro de eventos tiver quatro divisórias, e uma dessas divisórias for movida de um servidor para outro numa operação de equilíbrio de carga, ainda pode enviar e receber de três outras divisórias. Além disso, ter mais divisórias permite-lhe ter leitores mais simultâneos a processar os seus dados, melhorando a sua produção agregada. Compreender as implicações da partilha e da encomenda num sistema distribuído é um aspeto crítico do design da solução.

Para ajudar a explicar a compensação entre a encomenda e a disponibilidade, consulte o [teorema](https://en.wikipedia.org/wiki/CAP_theorem)da CAP , também conhecido como teorema de Brewer. Este teorema discute a escolha entre consistência, disponibilidade e tolerância à partilha. Afirma que para os sistemas divididos por rede há sempre trocas entre consistência e disponibilidade.

O teorema de Brewer define a consistência e a disponibilidade da seguinte forma:
* Tolerância à partilha: a capacidade de um sistema de processamento de dados continuar a processar dados mesmo que ocorra uma falha de partição.
* Disponibilidade: um nó infalível devolve uma resposta razoável num prazo razoável (sem erros ou intervalos).
* Consistência: uma leitura é garantida para devolver a escrita mais recente para um determinado cliente.

## <a name="partition-tolerance"></a>Tolerância à partilha
O Event Hubs é construído em cima de um modelo de dados dividido. Pode configurar o número de divisórias no seu centro de eventos durante a configuração, mas não pode alterar este valor mais tarde. Uma vez que deve utilizar divisórias com Hubs de Eventos, tem de tomar uma decisão sobre disponibilidade e consistência para a sua aplicação.

## <a name="availability"></a>Disponibilidade
A maneira mais simples de começar com os Centros de Eventos é usar o comportamento padrão. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs (5.0.0 ou mais tarde)](#tab/latest)
Se criar um novo objeto **[EventHubProducerClient](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient?view=azure-dotnet)** e utilizar o método **[SendAsync,](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync?view=azure-dotnet)** os seus eventos são distribuídos automaticamente entre divisórias no seu centro de eventos. Este comportamento permite a maior quantidade de tempo de up.

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs (4.1.0 ou mais cedo)](#tab/old)
Se criar um novo objeto **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** e utilizar o método **[Enviar,](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** os seus eventos são distribuídos automaticamente entre divisórias no seu centro de eventos. Este comportamento permite a maior quantidade de tempo de up.

---

Para os casos de utilização que exijam o máximo de tempo de up, este modelo é preferido.

## <a name="consistency"></a>Consistência
Em alguns cenários, a ordenação dos eventos pode ser importante. Por exemplo, pode querer que o seu sistema de back-end processe um comando de atualização antes de um comando de exclusão. Neste caso, pode definir a chave de partição `PartitionSender` num evento ou utilizar um objeto (se estiver a usar a antiga biblioteca Microsoft.Azure.Messaging) para enviar apenas eventos para uma determinada partição. Ao fazê-lo, assegura-se que, quando estes eventos são lidos a partir da partição, são lidos em ordem. Se estiver a utilizar a biblioteca **Azure.Messaging.EventHubs** e para obter mais informações, consulte o [código de migração do PartitionSender para o EventHubProducerClient para publicar eventos para uma partição](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition).

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs (5.0.0 ou mais tarde)](#tab/latest)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    var batchOptions = new CreateBatchOptions() { PartitionId = "my-partition-id" };
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync(batchOptions);
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));
    
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs (4.1.0 ou mais cedo)](#tab/old)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(connectionString){ EntityPath = eventHubName }; 
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
PartitionSender partitionSender = eventHubClient.CreatePartitionSender("my-partition-id");
try
{
    EventDataBatch eventBatch = partitionSender.CreateBatch();
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));

    await partitionSender.SendAsync(eventBatch);
}
finally
{
    await partitionSender.CloseAsync();
    await eventHubClient.CloseAsync();
}
```

---

Com esta configuração, lembre-se que se a partição específica para a qual está a enviar não estiver disponível, receberá uma resposta de erro. Como ponto de comparação, se não tiver uma afinidade com uma única partição, o serviço Event Hubs envia o seu evento para a próxima partição disponível.

Uma solução possível para garantir a encomenda, ao mesmo tempo que maximiza o tempo, seria agregar eventos como parte da sua aplicação de processamento de eventos. A maneira mais fácil de o conseguir é carimbar o seu evento com uma propriedade personalizada de número de sequência. O código seguinte mostra um exemplo:

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs (5.0.0 ou mais tarde)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs (4.1.0 ou mais cedo)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

Este exemplo envia o seu evento para uma das divisórias disponíveis no seu centro de eventos, e define o número de sequência correspondente da sua aplicação. Esta solução requer que o Estado seja mantido pela sua aplicação de processamento, mas dá aos seus remetentes um ponto final que é mais provável de estar disponível.

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Visão geral do serviço De Evento Hubs](event-hubs-what-is-event-hubs.md)
* [Criar um hub de eventos](event-hubs-create.md)
