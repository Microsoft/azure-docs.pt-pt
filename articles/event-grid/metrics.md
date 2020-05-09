---
title: Métricas suportadas pela Azure Event Grid
description: Este artigo fornece métricas do Azure Monitor suportadas pelo serviço Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: spelluru
ms.openlocfilehash: 643df2f4cc6347e0fd56f9124b68f1888ab85e26
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630138"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Métricas suportadas pela Azure Event Grid
Este artigo fornece listas de métricas da Grelha de Eventos que são categorizadas por espaços de nome. 

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domínios

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem de Sucessos|Eventos Publicados|Contagem|Total|Total de eventos publicados para este tópico|Tópico|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Eventos totais não publicaram para este tópico|Tópico,ErrorType,Error|
|PublicarSuccessLatencyInMs|Publicar Latência de Sucesso|Milissegundos|Total|Publicar latência de sucesso em milissegundos|Nenhum|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição do evento|Tópico,Nome de Subscrição de Eventos,DomínioEventSubscriptionName|
|Contagem de falhas de tentativa de entrega|Eventos falhados de entrega|Contagem|Total|Eventos totais não entregaram a esta subscrição do evento|Tópico,Nome de Subscrição de Eventos,DomínioEventSubscriptionName,Error,ErrorType|
|Contagem de Sucessos de Entrega|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Tópico,Nome de Subscrição de Eventos,DomínioEventSubscriptionName|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Tópico,Nome de Subscrição de Eventos,DomínioEventSubscriptionName|
|Contagem de eventos abandonados|Eventos abandonados|Contagem|Total|Total deixou cair eventos correspondentes a esta subscrição de evento|Tópico,Nome de Subscrição de Eventos,DomínioNome de Subscrição de Domínio,DropReason|
|DeadLetteredCount|Eventos com Letras Mortas|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|Tópico,Nome de Subscrição de Eventos,Nome de Subscrição de Domínio,DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/tópicos

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem de Sucessos|Eventos Publicados|Contagem|Total|Total de eventos publicados para este tópico|Nenhum|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Eventos totais não publicaram para este tópico|ErrorType,Error|
|Contagem de eventos incomparáveis|Eventos Incomparáveis|Contagem|Total|Eventos totais que não correspondem a nenhuma das subscrições do evento para este tópico|Nenhum|
|PublicarSuccessLatencyInMs|Publicar Latência de Sucesso|Milissegundos|Total|Publicar latência de sucesso em milissegundos|Nenhum|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição do evento|Nome de subscrição de eventos|
|Contagem de falhas de tentativa de entrega|Eventos falhados de entrega|Contagem|Total|Eventos totais não entregaram a esta subscrição do evento|Error,ErrorType,EventSubscriptionName|
|Contagem de Sucessos de Entrega|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|Contagem de eventos abandonados|Eventos abandonados|Contagem|Total|Total deixou cair eventos correspondentes a esta subscrição de evento|DropReason, Nome de Subscrição de Eventos|
|DeadLetteredCount|Eventos com Letras Mortas|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason, EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem de Sucessos|Eventos Publicados|Contagem|Total|Total de eventos publicados para este tópico|Nenhum|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Eventos totais não publicaram para este tópico|ErrorType, Error|
|Contagem de eventos incomparáveis|Eventos Incomparáveis|Contagem|Total|Eventos totais que não correspondem a nenhuma das subscrições do evento para este tópico|Nenhum|
|PublicarSuccessLatencyInMs|Publicar Latência de Sucesso|Milissegundos|Total|Publicar latência de sucesso em milissegundos|Nenhum|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição do evento|Nome de subscrição de eventos|
|Contagem de falhas de tentativa de entrega|Eventos falhados de entrega|Contagem|Total|Eventos totais não entregaram a esta subscrição do evento|Error,ErrorType,EventSubscriptionName|
|Contagem de Sucessos de Entrega|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|Contagem de eventos abandonados|Eventos abandonados|Contagem|Total|Total deixou cair eventos correspondentes a esta subscrição de evento|DropReason, Nome de Subscrição de Eventos|
|DeadLetteredCount|Eventos com Letras Mortas|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason, EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição do evento|Nenhum|
|Contagem de falhas de tentativa de entrega|Eventos falhados de entrega|Contagem|Total|Eventos totais não entregaram a esta subscrição do evento|Erro, ErrorType|
|Contagem de Sucessos de Entrega|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Nenhum|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nenhum|
|Contagem de eventos abandonados|Eventos abandonados|Contagem|Total|Total deixou cair eventos correspondentes a esta subscrição de evento|DropReason|
|DeadLetteredCount|Eventos com Letras Mortas|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem de Sucessos|Eventos Publicados|Contagem|Total|Total de eventos publicados para este tópico|Nenhum|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Eventos totais não publicaram para este tópico|ErrorType, Error|
|Contagem de eventos incomparáveis|Eventos Incomparáveis|Contagem|Total|Eventos totais que não correspondem a nenhuma das subscrições do evento para este tópico|Nenhum|
|PublicarSuccessLatencyInMs|Publicar Latência de Sucesso|Milissegundos|Total|Publicar latência de sucesso em milissegundos|Nenhum|

## <a name="next-steps"></a>Passos seguintes
Ver o seguinte artigo: [Registos de diagnóstico](diagnostic-logs.md)
