---
title: Disponibilidade e consistência - Azure Event Hubs | Microsoft Docs
description: Como fornecer a quantidade máxima de disponibilidade e consistência com os Azure Event Hubs usando divisórias.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2fdb62e953230a38a26d22e136789fea52c8ee8c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882200"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilidade e consistência em Hubs de Eventos
Este artigo fornece informações sobre disponibilidade e consistência apoiadas pelo Azure Event Hubs. 

## <a name="availability"></a>Disponibilidade
O Azure Event Hubs espalha o risco de falhas catastróficas de máquinas individuais ou mesmo de racks completos em clusters que abrangem vários domínios de falha dentro de um datacenter. Implementa mecanismos transparentes de deteção de falhas e falhas de modo a que o serviço continue a funcionar dentro dos níveis de serviço assegurados e normalmente sem interrupções visíveis quando tais falhas ocorrerem. Se um espaço de nomes do Event Hubs tiver sido criado com a opção ativa para [zonas de disponibilidade,](../availability-zones/az-overview.md)o risco de paragem é ainda mais distribuído por três instalações fisicamente separadas, e o serviço tem reservas de capacidade suficientes para lidar instantaneamente com a perda completa e catastrófica de toda a instalação. Para mais informações, consulte [os Hubs de Eventos Azure - Recuperação de geo-desastres.](event-hubs-geo-dr.md)

Quando uma aplicação de cliente envia eventos para um centro de eventos, os eventos são automaticamente distribuídos entre divisórias no seu centro de eventos. Se uma partição não estiver disponível por alguma razão, os eventos são distribuídos entre as restantes divisórias. Este comportamento permite a maior quantidade de tempo de mente. Para a utilização de casos que exijam o tempo máximo de up, este modelo é preferido em vez de enviar eventos para uma partição específica. Para mais informações, consulte [As Divisórias.](event-hubs-scalability.md#partitions)

## <a name="consistency"></a>Consistência
Em alguns cenários, a ordenação dos acontecimentos pode ser importante. Por exemplo, pode querer que o seu sistema back-end processe um comando de atualização antes de um comando de eliminação. Neste cenário, uma aplicação de cliente envia eventos para uma partição específica para que o pedido seja preservado. Quando uma aplicação de consumo consome estes eventos a partir da partição, são lidos em ordem. 

Com esta configuração, tenha em mente que se a partição específica para a qual está a enviar não estiver disponível, receberá uma resposta de erro. Como ponto de comparação, se não tiver uma afinidade com uma única partição, o serviço Event Hubs envia o seu evento para a próxima divisão disponível.

Uma solução possível para garantir a encomenda, ao mesmo tempo que maximiza o tempo, seria agregar eventos como parte da sua aplicação de processamento de eventos. A maneira mais fácil de o conseguir é carimbar o seu evento com uma propriedade de número de sequência personalizada.

Neste cenário, o cliente produtor envia eventos para uma das divisórias disponíveis no seu centro de eventos, e define o número de sequência correspondente da sua aplicação. Esta solução requer que o estado seja mantido pela sua aplicação de processamento, mas dá aos seus remetentes um ponto final mais provável de estar disponível.

## <a name="appendix"></a>Apêndice

### <a name="net-examples"></a>.EXEMPLOS NET

#### <a name="send-events-to-a-specific-partition"></a>Enviar eventos para uma partição específica
Ou define a chave de partição num evento ou utiliza um `PartitionSender` objeto (se estiver a usar a antiga biblioteca Microsoft.Azure.Messaging) para enviar apenas eventos para uma determinada partição. Ao fazê-lo, garante que, quando estes eventos são lidos a partir da partição, são lidos em ordem. 

Se estiver a utilizar a mais recente biblioteca **Azure.Messaging.EventHubs,** consulte o [código migratório de PartitionSender a EventHubProducerClient para a publicação de eventos para uma partição](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition).

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

### <a name="set-a-sequence-number"></a>Definir um número de sequência
O exemplo a seguir marca o seu evento com uma propriedade de número de sequência personalizada. 

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

## <a name="next-steps"></a>Próximos passos
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Visão geral do serviço do Event Hubs](./event-hubs-about.md)
* [Criar um centro de eventos](event-hubs-create.md)
