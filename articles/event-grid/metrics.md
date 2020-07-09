---
title: Métricas suportadas por Azure Event Grid
description: Este artigo fornece métricas do Azure Monitor suportadas pelo serviço Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3b22beafc9f88d2d95b25fd7ad2f2308a4df9097
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116425"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Métricas suportadas por Azure Event Grid
Este artigo fornece listas de métricas de Grade de Eventos que são categorizadas por espaços de nome. 

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domínios

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublicarSuccessCount|Eventos Publicados|Contagem|Total|Total de eventos publicados neste tópico|Tópico|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Total de eventos não publicou para este tópico|Tópico,ErrorType,Error|
|Publicar Inimitrodências|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Nenhuma|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição de evento|Tema,EventSubscriptionName,DomainEventSubscriptionName|
|EntregaAttemptFailCount|Eventos falhados de entrega|Contagem|Total|Total de eventos não entregaram a esta subscrição do evento|Tópico,EventSubscriptionName,DomainEventSubscriptionName,Error,ErrorType|
|EntregaSSuccessCount|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Tema,EventSubscriptionName,DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Tema,EventSubscriptionName,DomainEventSubscriptionName|
|DropEventCount|Eventos abandonados|Contagem|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|Tópico,EventSubscriptionName,DomainEventSubscriptionName,DropReason|
|DeadLetteredCount|Eventos lettered mortos|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|Tópico,EventSubscriptionName,DomainEventSubscriptionName,DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/tópicos

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublicarSuccessCount|Eventos Publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhuma|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Total de eventos não publicou para este tópico|ErrorType,Error|
|InigualávelEventCount|Eventos incomparáveis|Contagem|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Nenhuma|
|Publicar Inimitrodências|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Nenhuma|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição de evento|Nome de subscrição de eventos|
|EntregaAttemptFailCount|Eventos falhados de entrega|Contagem|Total|Total de eventos não entregaram a esta subscrição do evento|Erro,ErrorType,EventSubscriptionName|
|EntregaSSuccessCount|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|DropEventCount|Eventos abandonados|Contagem|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|DropReason, Nome de EventosSubscription|
|DeadLetteredCount|Eventos lettered mortos|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason, Nome de EventosSubscrição|

## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublicarSuccessCount|Eventos Publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhuma|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Total de eventos não publicou para este tópico|ErrorType, Error|
|InigualávelEventCount|Eventos incomparáveis|Contagem|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Nenhuma|
|Publicar Inimitrodências|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Nenhuma|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição de evento|Nome de subscrição de eventos|
|EntregaAttemptFailCount|Eventos falhados de entrega|Contagem|Total|Total de eventos não entregaram a esta subscrição do evento|Erro,ErrorType,EventSubscriptionName|
|EntregaSSuccessCount|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|DropEventCount|Eventos abandonados|Contagem|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|DropReason, Nome de EventosSubscription|
|DeadLetteredCount|Eventos lettered mortos|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason, Nome de EventosSubscrição|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição de evento|Nenhuma|
|EntregaAttemptFailCount|Eventos falhados de entrega|Contagem|Total|Total de eventos não entregaram a esta subscrição do evento|Erro, ErrorType|
|EntregaSSuccessCount|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Nenhuma|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nenhuma|
|DropEventCount|Eventos abandonados|Contagem|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|GotaReason|
|DeadLetteredCount|Eventos lettered mortos|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublicarSuccessCount|Eventos Publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhuma|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Total de eventos não publicou para este tópico|ErrorType, Error|
|InigualávelEventCount|Eventos incomparáveis|Contagem|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Nenhuma|
|Publicar Inimitrodências|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Nenhuma|

## <a name="next-steps"></a>Próximos passos
Ver o seguinte artigo: [Registos de diagnóstico](diagnostic-logs.md)
