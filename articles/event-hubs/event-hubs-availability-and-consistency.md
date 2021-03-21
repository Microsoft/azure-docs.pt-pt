---
title: Disponibilidade e consistência - Azure Event Hubs | Microsoft Docs
description: Como fornecer a quantidade máxima de disponibilidade e consistência com os Azure Event Hubs usando divisórias.
ms.topic: article
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cd446cf86c22b851bae9cb9d8535a8e5234e08b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722536"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilidade e consistência em Hubs de Eventos
Este artigo fornece informações sobre disponibilidade e consistência apoiadas pelo Azure Event Hubs. 

## <a name="availability"></a>Disponibilidade
O Azure Event Hubs espalha o risco de falhas catastróficas de máquinas individuais ou mesmo de racks completos em clusters que abrangem vários domínios de falha dentro de um datacenter. Implementa mecanismos transparentes de deteção de falhas e falhas de modo a que o serviço continue a funcionar dentro dos níveis de serviço assegurados e normalmente sem interrupções visíveis quando tais falhas ocorrerem. 

Se um espaço de nomes do Event Hubs tiver sido criado com [zonas de disponibilidade](../availability-zones/az-overview.md) ativadas, o risco de paragem é ainda mais distribuído por três instalações fisicamente separadas, e o serviço tem reservas de capacidade suficientes para lidar instantaneamente com a perda completa e catastrófica de toda a instalação. Para mais informações, consulte [os Hubs de Eventos Azure - Recuperação de geo-desastres.](event-hubs-geo-dr.md)

Quando uma aplicação de cliente envia eventos para um centro de eventos sem especificar uma partição, os eventos são automaticamente distribuídos entre divisórias no seu centro de eventos. Se uma partição não estiver disponível por alguma razão, os eventos são distribuídos entre as restantes divisórias. Este comportamento permite a maior quantidade de tempo de mente. Para a utilização de casos que exijam o tempo máximo de up, este modelo é preferido em vez de enviar eventos para uma partição específica. 

## <a name="consistency"></a>Consistência
Em alguns cenários, a ordenação dos acontecimentos pode ser importante. Por exemplo, pode querer que o seu sistema back-end processe um comando de atualização antes de um comando de eliminação. Neste cenário, uma aplicação de cliente envia eventos para uma partição específica para que o pedido seja preservado. Quando uma aplicação de consumo consome estes eventos a partir da partição, são lidos em ordem. 

Com esta configuração, tenha em mente que se a partição específica para a qual está a enviar não estiver disponível, receberá uma resposta de erro. Como ponto de comparação, se não tiver uma afinidade com uma única partição, o serviço Event Hubs envia o seu evento para a próxima divisão disponível.

Portanto, se a alta disponibilidade for mais importante, não direcione uma partição específica (utilizando iD/chave de partição). A utilização de ID/chave de partição diminui a disponibilidade de um centro de eventos para o nível de partição. Neste cenário, está a fazer uma escolha explícita entre disponibilidade (sem ID/chave de partição) e consistência (fixando eventos a uma partição específica). Para obter informações detalhadas sobre divisórias em Centros de Eventos, consulte [As Partições.](event-hubs-features.md#partitions)

## <a name="appendix"></a>Apêndice

### <a name="send-events-without-specifying-a-partition"></a>Enviar eventos sem especificar uma partição
Recomendamos o envio de eventos para um centro de eventos sem definir informações de partição para permitir que o serviço DeComisão do Evento equilibre a carga através de divisórias. Veja o seguinte rápido começa a aprender a fazê-lo em diferentes linguagens de programação. 

- [Enviar eventos usando .NET](event-hubs-dotnet-standard-getstarted-send.md)
- [Enviar eventos usando Java](event-hubs-java-get-started-send.md)
- [Enviar eventos usando JavaScript](event-hubs-python-get-started-send.md)
- [Enviar eventos com o Python](event-hubs-python-get-started-send.md)


### <a name="send-events-to-a-specific-partition"></a>Enviar eventos para uma partição específica
Nesta secção, aprende-se a enviar eventos para uma partição específica utilizando diferentes linguagens de programação. 

### <a name="net"></a>[.NET](#tab/dotnet)
Para enviar eventos para uma partição específica, crie o lote utilizando o método [EventHubProducerClient.CreateBatchAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) especificando o `PartitionId` ou o in `PartitionKey` [CreateBatchOptions](/dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions?view=azure-dotnet). O código seguinte envia um lote de eventos para uma partição específica especificando uma chave de partição. O Event Hubs garante que todos os eventos que partilham um valor chave de partição são armazenados e entregues por ordem de chegada.

```csharp
var batchOptions = new CreateBatchOptions { PartitionKey = "cities" };
using var eventBatch = await producer.CreateBatchAsync(batchOptions);
```

Também pode utilizar o método [EventHubProducerClient.SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_SendAsync_System_Collections_Generic_IEnumerable_Azure_Messaging_EventHubs_EventData__Azure_Messaging_EventHubs_Producer_SendEventOptions_System_Threading_CancellationToken_) especificando **partitionId** ou **PartitionKey** em [SendEventOptions](/dotnet/api/azure.messaging.eventhubs.producer.sendeventoptions).

```csharp
var sendEventOptions  = new SendEventOptions { PartitionKey = "cities" };
// create the events array
producer.SendAsync(events, sendOptions)
```


### <a name="java"></a>[Java](#tab/java)
Para enviar eventos para uma partição específica, crie o lote utilizando o método [createBatch](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.createbatch) especificando o **ID de partição** ou **a chave de partição** na [criação DeBatchOptions](/java/api/com.azure.messaging.eventhubs.models.createbatchoptions). O código seguinte envia um lote de eventos para uma partição específica especificando uma chave de partição. 

```java
CreateBatchOptions batchOptions = new CreateBatchOptions();
batchOptions.setPartitionKey("cities");
```

Também pode utilizar o método [EventHubProducerClient.send](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.send#com_azure_messaging_eventhubs_EventHubProducerClient_send_java_lang_Iterable_com_azure_messaging_eventhubs_EventData__com_azure_messaging_eventhubs_models_SendOptions_) especificando o **ID de partição** ou **a tecla de partição** em [SendOptions](/java/api/com.azure.messaging.eventhubs.models.sendoptions).

```java
List<EventData> events = Arrays.asList(new EventData("Melbourne"), new EventData("London"), new EventData("New York"));
SendOptions sendOptions = new SendOptions();
sendOptions.setPartitionKey("cities");
producer.send(events, sendOptions);
```


### <a name="python"></a>[Python](#tab/python) 
Enviar eventos para uma partição específica, ao criar um lote utilizando o [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) método, especifique `partition_id` o `partition_key` . Em seguida, utilize o [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) método para enviar o lote para a partição do centro de eventos. 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

Também pode utilizar o método [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) especificando valores para `partition_id` ou `partition_key` parâmetros.

```python
producer.send_batch(event_data_batch, partition_key="cities")
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)
Para enviar eventos para uma partição específica, [Crie um lote](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) utilizando o objeto [EventHubProducerClient.CreateBatchOptions](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) especificando o `partitionId` ou o `partitionKey` . Em seguida, envie o lote para o centro de eventos utilizando o método [EventHubProducerClient.SendBatch.](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventDataBatch__OperationOptions_) 

Veja o seguinte exemplo.

```javascript
const batchOptions = { partitionKey = "cities"; };
const batch = await producer.createBatch(batchOptions);
```

Também pode utilizar o método [EventHubProducerClient.sendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventData____SendBatchOptions_) especificando o **ID de partição** ou **a tecla de partição** em [SendBatchOptions](/javascript/api/@azure/event-hubs/sendbatchoptions).

```javascript
const sendBatchOptions = { partitionKey = "cities"; };
// prepare events
producer.sendBatch(events, sendBatchOptions);
```

---



## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

- [Visão geral do serviço do Event Hubs](./event-hubs-about.md)
- [Terminologia dos Hubs de Eventos](event-hubs-features.md)
