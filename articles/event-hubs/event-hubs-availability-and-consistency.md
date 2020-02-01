---
title: Disponibilidade e consistência - Event Hubs do Azure | Documentos da Microsoft
description: Como fornecer a quantidade máxima de disponibilidade e consistência com os Hubs de eventos do Azure com partições.
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
ms.date: 01/29/2020
ms.author: shvija
ms.openlocfilehash: 808e813ad90626acec893a021634566f091c895f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904476"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilidade e consistência em Hubs de eventos

## <a name="overview"></a>Visão geral
Os Hubs de eventos do Azure utiliza um [modelo de criação de partições](event-hubs-scalability.md#partitions) para melhorar a disponibilidade e paralelização dentro de um hub de eventos único. Por exemplo, se um hub de eventos tem quatro partições, e um nessas partições é movido de um servidor para outro numa operação de balanceamento de carga, ainda pode enviar e receber de três outras partições. Além disso, a ter mais de partições permite-lhe ter os leitores mais simultâneos processar os seus dados, melhorar o débito agregado. Compreender as implicações de criação de partições e ordenação num sistema distribuído, é um aspeto fundamental do design da solução.

Para ajudar a explicar o equilíbrio entre a ordenação e a disponibilidade, consulte a [Teorema CAP](https://en.wikipedia.org/wiki/CAP_theorem), também conhecido como o Teorema do Brewer. Este Teorema aborda a escolha entre consistência, disponibilidade e tolerância de partição. Ele declara que para os sistemas particionados por rede existe sempre variação entre a consistência e disponibilidade.

O Teorema do Brewer define consistência e a disponibilidade da seguinte forma:
* Tolerância de partição: a capacidade de um sistema de processamento de dados para continuar o processamento de dados, mesmo se ocorrer uma falha de partição.
* Disponibilidade: um nó não falha devolve uma resposta razoável dentro de uma quantidade razoável de tempo (sem erros ou tempos limite).
* : Uma leitura é garantir a consistência para retornar a escrita mais recente para um determinado cliente.

## <a name="partition-tolerance"></a>Tolerância de partição
Os Hubs de eventos é criada sobre um modelo de dados particionados. Pode configurar o número de partições do seu hub de eventos durante a configuração, mas não pode alterar este valor mais tarde. Uma vez que tem de utilizar partições com os Hubs de eventos, terá de tomar uma decisão sobre a disponibilidade e consistência para a sua aplicação.

## <a name="availability"></a>Disponibilidade
A forma mais simples para começar a utilizar com os Hubs de eventos é usar o comportamento padrão. Se criar um novo **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** objeto e usar o **[enviar](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** método, os eventos são automaticamente distribuídos entre partições do seu hub de eventos. Este comportamento permite que a maior quantidade de tempo.

Para casos de utilização que exigem o máximo de tempo de atividade, esse modelo é preferencial.

## <a name="consistency"></a>Consistência
Em alguns cenários, a ordenação de eventos pode ser importante. Por exemplo, pode desejar seu sistema de back-end para processar um comando de atualização antes de um comando de eliminação. Neste caso, pode definir a chave de partição num evento ou utilizar um `PartitionSender` objeto apenas enviar eventos para uma determinada partição. Se o fizer, garante que, quando esses eventos são lidos a partir da partição, sejam lidos por ordem.

Com esta configuração, tenha em atenção que, se a partição específica para os quais estão a enviar não estiver disponível, receberá uma resposta de erro. Como um ponto de comparação, se não tiver uma afinidade com uma única partição, o serviço de Hubs de eventos envia seu evento para a partição disponível seguinte.

Uma solução possível para garantir a ordenação, ao maximizar o tempo, também seria agregar eventos como parte de seu aplicativo de processamento de eventos. A maneira mais fácil de realizar isso é carimbo seu evento com uma propriedade de número de sequência personalizada. O código seguinte mostra um exemplo:

#### <a name="azuremessagingeventhubs-500-or-latertablatest"></a>[Azure.Messaging.EventHubs (5.0.0 ou mais tarde)](#tab/latest)

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

#### <a name="microsoftazureeventhubs-410-or-earliertabold"></a>[Microsoft.Azure.EventHubs (4.1.0 ou mais cedo)](#tab/old)
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

Neste exemplo envia o evento para uma das partições disponíveis no seu hub de eventos e define o número de sequência correspondente a partir da sua aplicação. Esta solução requer o estado ser mantidos pelo seu aplicativo de processamento, mas dá seu remetentes um ponto final que é mais provável que esteja disponível.

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral de serviço de Hubs de eventos](event-hubs-what-is-event-hubs.md)
* [Criar um hub de eventos](event-hubs-create.md)
