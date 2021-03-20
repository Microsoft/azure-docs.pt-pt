---
title: Grelha de Eventos Azure - Registos de diagnóstico para tópicos ou domínios
description: Este artigo fornece informações conceptuais sobre registos de diagnóstico para um tópico de grelha de eventos Azure ou um domínio.
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: 36ade14932b5d25c7a1fe05632da671de68ba3bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96574991"
---
#  <a name="diagnostic-logs-for-azure-event-grid-topicsdomains"></a>Registos de diagnóstico para tópicos/domínios da Grelha de Eventos Azure
As definições de diagnóstico permitem aos utilizadores da Grade de Eventos capturar e visualizar registos de **falha de publicação e entrega** numa conta de Armazenamento, num centro de eventos ou num espaço de trabalho do Log Analytics. Este artigo fornece esquema para os registos e uma entrada de registo de exemplo.


## <a name="schema-for-publishdelivery-failure-logs"></a>Esquema para registos de falha de publicação/entrega

| Nome da propriedade | Tipo de dados | Descrição |
| ------------- | --------- | ----------- | 
| Hora | DateTime | O tempo em que a entrada de registo foi gerada <p>**Valor do exemplo:**  01-29-2020 09:52:02.700</p> |
| Nome de subscrição de eventos | String | O nome da subscrição do evento <p>**Valor de exemplo:** "EVENTSUB1"</p> <p>Esta propriedade existe apenas para registos de falha de entrega.</p>  |
| Categoria | String | O nome da categoria de registo. <p>**Valores de exemplo:** "DeliveryFailures" ou "PublishFailures" | 
| OperationName | String | O nome da operação causou a falha.<p>**Valores de exemplo:** "Entregar" para falhas de entrega. |
| Mensagem | String | A mensagem de registo para o utilizador explicando o motivo da falha e outros detalhes adicionais. |
| ResourceId | String | O ID de recursos para o tópico/recurso de domínio<p>**Valores de exemplo:**`/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example"></a>Exemplo

```json
{
    "time": "2019-11-01T00:17:13.4389048Z",
    "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
    "eventSubscriptionName": "SAMPLEDESTINATION",
    "category": "DeliveryFailures",
    "operationName": "Deliver",
    "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
}
```

## <a name="next-steps"></a>Passos seguintes
Para aprender a ativar registos de diagnóstico para tópicos ou domínios, consulte [Ativar registos de diagnóstico](enable-diagnostic-logs-topic.md).
