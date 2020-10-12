---
title: Disponibilidade e consistência - Azure Event Hubs Microsoft Docs
description: Como fornecer a quantidade máxima de disponibilidade e consistência com os Azure Event Hubs usando divisórias.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 774332b8f2d5c336f1a22d717516ae35a62b341f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89000639"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilidade e consistência em Hubs de Eventos

## <a name="overview"></a>Descrição geral
O Azure Event Hubs usa um [modelo de partição](event-hubs-scalability.md#partitions) para melhorar a disponibilidade e a paralelização dentro de um único centro de eventos. Por exemplo, se um centro de eventos tiver quatro divisórias, e uma dessas divisórias for movida de um servidor para outro numa operação de equilíbrio de carga, ainda pode enviar e receber de três outras divisórias. Além disso, ter mais divisórias permite-lhe ter leitores mais simultâneos a processar os seus dados, melhorando a sua produção agregada. Compreender as implicações da partilha e da encomenda num sistema distribuído é um aspeto crítico do design de soluções.

Para ajudar a explicar a troca entre encomendas e disponibilidade, consulte [o teorema da PAC](https://en.wikipedia.org/wiki/CAP_theorem), também conhecido como teorema de Brewer. Este teorema discute a escolha entre consistência, disponibilidade e tolerância à partição. Diz que para os sistemas divididos por rede há sempre uma compensação entre a consistência e a disponibilidade.

O teorema de Brewer define consistência e disponibilidade da seguinte forma:
* Tolerância à partição: a capacidade de um sistema de processamento de dados continuar a processar dados mesmo que ocorra uma falha de partição.
* Disponibilidade: um nó não falha devolve uma resposta razoável dentro de um período de tempo razoável (sem erros ou intervalos).
* Consistência: uma leitura é garantida para devolver a escrita mais recente para um determinado cliente.

## <a name="partition-tolerance"></a>Tolerância à partição
Os Centros de Eventos são construídos em cima de um modelo de dados dividido. Pode configurar o número de divisórias no seu centro de eventos durante a configuração, mas não pode alterar este valor mais tarde. Uma vez que tem de usar divisórias com os Centros de Eventos, tem de tomar uma decisão sobre disponibilidade e consistência para a sua aplicação.

## <a name="availability"></a>Disponibilidade
A forma mais simples de começar com os Centros de Eventos é usar o comportamento padrão. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs (5.0.0 ou mais tarde)](#tab/latest)
Se criar um novo objeto **[EventHubProducerClient](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient?view=azure-dotnet)** e utilizar o método **[SendAsync,](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync?view=azure-dotnet)** os seus eventos são automaticamente distribuídos entre divisórias no seu centro de eventos. Este comportamento permite a maior quantidade de tempo de mente.

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs (4.1.0 ou mais cedo)](#tab/old)
Se criar um novo objeto **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** e utilizar o método **[Enviar,](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** os seus eventos são automaticamente distribuídos entre divisórias no seu centro de eventos. Este comportamento permite a maior quantidade de tempo de mente.

---

Para a utilização de casos que exijam o máximo de tempo de mente, este modelo é preferido.

## <a name="consistency"></a>Consistência
Em alguns cenários, a ordenação dos acontecimentos pode ser importante. Por exemplo, pode querer que o seu sistema back-end processe um comando de atualização antes de um comando de eliminação. Neste caso, pode definir a chave de partição num evento ou usar um `PartitionSender` objeto (se estiver a utilizar a antiga biblioteca Microsoft.Azure.Messaging) para enviar apenas eventos para uma determinada partição. Ao fazê-lo, garante que, quando estes eventos são lidos a partir da partição, são lidos em ordem. Se estiver a utilizar a biblioteca **Azure.Messaging.EventHubs** e para obter mais informações, consulte o [código migratório de PartitionSender a EventHubProducerClient para a publicação de eventos para uma partição](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition).

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

Com esta configuração, tenha em mente que se a partição específica para a qual está a enviar não estiver disponível, receberá uma resposta de erro. Como ponto de comparação, se não tiver uma afinidade com uma única partição, o serviço Event Hubs envia o seu evento para a próxima divisão disponível.

Uma solução possível para garantir a encomenda, ao mesmo tempo que maximiza o tempo, seria agregar eventos como parte da sua aplicação de processamento de eventos. A maneira mais fácil de conseguir isso é carimbar o seu evento com uma propriedade de número de sequência personalizada. O código seguinte mostra um exemplo:

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

Este exemplo envia o seu evento para uma das divisórias disponíveis no seu centro de eventos e define o número de sequência correspondente a partir da sua aplicação. Esta solução requer que o estado seja mantido pela sua aplicação de processamento, mas dá aos seus remetentes um ponto final mais provável de estar disponível.

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Visão geral do serviço do Event Hubs](./event-hubs-about.md)
* [Criar um hub de eventos](event-hubs-create.md)
