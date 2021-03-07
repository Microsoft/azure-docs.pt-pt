---
title: Disponibilidade e consistência - Azure Event Hubs | Microsoft Docs
description: Como fornecer a quantidade máxima de disponibilidade e consistência com os Azure Event Hubs usando divisórias.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 325cc80daba2a44dedbd5e09ac4858ae2815c1cd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425928"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilidade e consistência em Hubs de Eventos
Este artigo fornece informações sobre disponibilidade e consistência apoiadas pelo Azure Event Hubs. 

## <a name="availability"></a>Disponibilidade
O Azure Event Hubs espalha o risco de falhas catastróficas de máquinas individuais ou mesmo de racks completos em clusters que abrangem vários domínios de falha dentro de um datacenter. Implementa mecanismos transparentes de deteção de falhas e falhas de modo a que o serviço continue a funcionar dentro dos níveis de serviço assegurados e normalmente sem interrupções visíveis quando tais falhas ocorrerem. 

Se um espaço de nomes do Event Hubs tiver sido criado com [zonas de disponibilidade](../availability-zones/az-overview.md) ativadas, o risco de paragem é ainda mais distribuído por três instalações fisicamente separadas, e o serviço tem reservas de capacidade suficientes para lidar instantaneamente com a perda completa e catastrófica de toda a instalação. Para mais informações, consulte [os Hubs de Eventos Azure - Recuperação de geo-desastres.](event-hubs-geo-dr.md)

Quando uma aplicação de cliente envia eventos para um centro de eventos sem especificar uma partição, os eventos são automaticamente distribuídos entre divisórias no seu centro de eventos. Se uma partição não estiver disponível por alguma razão, os eventos são distribuídos entre as restantes divisórias. Este comportamento permite a maior quantidade de tempo de mente. Para a utilização de casos que exijam o tempo máximo de up, este modelo é preferido em vez de enviar eventos para uma partição específica. 

### <a name="availability-considerations-when-using-a-partition-id-or-key"></a>Considerações de disponibilidade ao utilizar um ID ou chave de partição
A utilização de uma chave de identificação de partição ou de partição é opcional. Considere cuidadosamente se deve ou não usar um. Se não especificar um ID/chave de partição ao publicar um evento, o Event Hubs equilibra a carga entre divisórias. Quando utiliza um ID/chave de partição, estas divisórias requerem disponibilidade num único nó, e as interrupções podem ocorrer ao longo do tempo. Por exemplo, os nós de computação podem ter de ser reiniciados ou remendados. Assim, se definir um ID/chave de partição e essa partição ficar indisponível por alguma razão, uma tentativa de aceder aos dados nessa partição falhará. Se a alta disponibilidade for mais importante, não especifique um ID/chave de partição. Nesse caso, os eventos são enviados para divisórias usando um algoritmo interno de equilíbrio de carga. Neste cenário, está a fazer uma escolha explícita entre disponibilidade (sem ID/chave de partição) e consistência (fixando eventos a uma partição específica). A utilização de ID/chave de partição diminui a disponibilidade de um centro de eventos para o nível de partição. 

### <a name="availability-considerations-when-handling-delays-in-processing-events"></a>Considerações de disponibilidade no tratamento de atrasos nos eventos de processamento
Outra consideração é a de um pedido de consumidores que lida com atrasos no processamento de eventos. Em alguns casos, seria melhor que a aplicação do consumidor deixasse cair os dados e tentasse voltar a tentar, em vez de tentar acompanhar o tratamento, o que pode potencialmente causar mais atrasos no processamento a jusante. Por exemplo, com um ticker de stock é melhor esperar por dados atualizados completos, mas num cenário de chat ao vivo ou VOIP você prefere ter os dados rapidamente, mesmo que não esteja completo.

Tendo em conta estas considerações de disponibilidade, nestes cenários, a aplicação do consumidor pode escolher uma das seguintes estratégias de tratamento de erros:

- Parar (parar de ler no centro de eventos até que as questões sejam corrigidas)
- Drop (as mensagens não são importantes, largue-as)
- Reda o redando (relemisse as mensagens como entender)


## <a name="consistency"></a>Consistência
Em alguns cenários, a ordenação dos acontecimentos pode ser importante. Por exemplo, pode querer que o seu sistema back-end processe um comando de atualização antes de um comando de eliminação. Neste cenário, uma aplicação de cliente envia eventos para uma partição específica para que o pedido seja preservado. Quando uma aplicação de consumo consome estes eventos a partir da partição, são lidos em ordem. 

Com esta configuração, tenha em mente que se a partição específica para a qual está a enviar não estiver disponível, receberá uma resposta de erro. Como ponto de comparação, se não tiver uma afinidade com uma única partição, o serviço Event Hubs envia o seu evento para a próxima divisão disponível.


## <a name="appendix"></a>Apêndice

### <a name="send-events-to-a-specific-partition"></a>Enviar eventos para uma partição específica
Esta secção mostra-lhe como enviar eventos para uma partição específica usando C#, Java, Python e JavaScript. 

### <a name="net"></a>[.NET](#tab/dotnet)
Para obter o código de amostra completo que lhe mostra como enviar um lote de eventos para um centro de eventos (sem definir iD/chave de partição), consulte [Enviar eventos para e receber eventos de Azure Event Hubs - .NET (Azure.Messaging.EventHubs)](event-hubs-dotnet-standard-getstarted-send.md).

Para enviar eventos para uma partição específica, crie o lote utilizando o método [EventHubProducerClient.CreateBatchAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) especificando o `PartitionId` ou o in `PartitionKey` [CreateBatchOptions](//dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions). O código seguinte envia um lote de eventos para uma partição específica especificando uma chave de partição. 

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
Para o código de amostra completo que lhe mostra como enviar um lote de eventos para um centro de eventos (sem definir iD/chave de partição), consulte [Use Java para enviar eventos para ou receber eventos de Azure Event Hubs (azure-messaging-eventhubs)](event-hubs-java-get-started-send.md).

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
Para obter o código de amostra completo que lhe mostra como enviar um lote de eventos para um centro de eventos (sem definir iD/chave de partição), consulte [Enviar eventos para ou receber eventos de centros de eventos utilizando Python (azure-eventhub)](event-hubs-python-get-started-send.md).

Enviar eventos para uma partição específica, ao criar um lote utilizando o [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) método, especifique `partition_id` o `partition_key` . Em seguida, utilize o [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) método para enviar o lote para a partição do centro de eventos. 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

Também pode utilizar o método [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) especificando valores para `partition_id` ou `partition_key` parâmetros.

```python
producer.send_batch(event_data_batch, partition_key="cities")
```


### <a name="javascript"></a>[JavaScript](#tab/javascript)
Para o código de amostra completo que mostra como enviar um lote de eventos para um centro de eventos (sem definir ID/chave de partição), consulte [Enviar eventos para ou receber eventos de centros de eventos utilizando o JavaScript (azure/event-hubs)](event-hubs-node-get-started-send.md).

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

* [Visão geral do serviço do Event Hubs](./event-hubs-about.md)
* [Criar um centro de eventos](event-hubs-create.md)
