---
title: Métricas suportadas por Azure Event Grid
description: Este artigo fornece métricas do Azure Monitor suportadas pelo serviço Azure Event Grid.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 87c91077c8eeca2134da53774979c212a82e3b7d
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042145"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Métricas suportadas por Azure Event Grid
Este artigo fornece listas de métricas de Grade de Eventos que são categorizadas por espaços de nome. 

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domínios

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Eventos lettered mortos|de palavras|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|Tema, EventosUbscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|EntregaAttemptFailCount|No|Eventos falhados de entrega|de palavras|Total|Total de eventos não entregaram a esta subscrição do evento|Tema, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|EntregaSSuccessCount|Yes|Eventos Entregues|de palavras|Total|Total de eventos entregues a esta subscrição do evento|Tema, Nome de Subscrição de Eventos, Nome de Subsubscrição de Eventos|
|DestinationProcessingDurationInMs|No|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Tema, Nome de Subscrição de Eventos, Nome de Subsubscrição de Eventos|
|DropEventCount|Yes|Eventos abandonados|de palavras|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|Tema, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Eventos Combinados|de palavras|Total|Total de eventos combinados com esta subscrição de evento|Tema, Nome de Subscrição de Eventos, Nome de Subsubscrição de Eventos|
|PublicarFailCount|Yes|Publicar Eventos Falhados|de palavras|Total|Total de eventos não publicou para este tópico|Tópico, ErrorType, ErrorType|
|PublicarSuccessCount|Yes|Eventos Publicados|de palavras|Total|Total de eventos publicados neste tópico|Tópico|
|Publicar Inimitrodências|Yes|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Sem Dimensões|
| Extra-Endação DeFilter Avançado | Yes | Avaliações avançadas de filtros | de palavras | Total | Total de filtros avançados avaliados em todas as subscrições de eventos | Nome de subscrição de eventos |



## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Eventos lettered mortos|de palavras|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason|
|EntregaAttemptFailCount|No|Eventos falhados de entrega|de palavras|Total|Total de eventos não entregaram a esta subscrição do evento|Erro, ErrorType|
|EntregaSSuccessCount|Yes|Eventos Entregues|de palavras|Total|Total de eventos entregues a esta subscrição do evento|Sem Dimensões|
|DestinationProcessingDurationInMs|No|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Sem Dimensões|
|DropEventCount|Yes|Eventos abandonados|de palavras|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|GotaReason|
|MatchedEventCount|Yes|Eventos Combinados|de palavras|Total|Total de eventos combinados com esta subscrição de evento|Sem Dimensões|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|PublicarFailCount|Yes|Publicar Eventos Falhados|de palavras|Total|Total de eventos não publicou para este tópico|ErrorType, Error|
|PublicarSuccessCount|Yes|Eventos Publicados|de palavras|Total|Total de eventos publicados neste tópico|Sem Dimensões|
|Publicar Inimitrodências|Yes|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Sem Dimensões|
|InigualávelEventCount|Yes|Eventos incomparáveis|de palavras|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Sem Dimensões|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Eventos lettered mortos|de palavras|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason, Nome de EventosSubscrição|
|EntregaAttemptFailCount|No|Eventos falhados de entrega|de palavras|Total|Total de eventos não entregaram a esta subscrição do evento|Erro, ErrorType, EventSubscriptionName|
|EntregaSSuccessCount|Yes|Eventos Entregues|de palavras|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|No|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|DropEventCount|Yes|Eventos abandonados|de palavras|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|DropReason, Nome de EventosSubscription|
|MatchedEventCount|Yes|Eventos Combinados|de palavras|Total|Total de eventos combinados com esta subscrição de evento|Nome de subscrição de eventos|
|PublicarFailCount|Yes|Publicar Eventos Falhados|de palavras|Total|Total de eventos não publicou para este tópico|ErrorType, Error|
|PublicarSuccessCount|Yes|Eventos Publicados|de palavras|Total|Total de eventos publicados neste tópico|Sem Dimensões|
|Publicar Inimitrodências|Yes|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Sem Dimensões|
|InigualávelEventCount|Yes|Eventos incomparáveis|de palavras|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Sem Dimensões|
| Extra-Endação DeFilter Avançado | Yes | Avaliações avançadas de filtros | de palavras | Total | Total de filtros avançados avaliados em todas as subscrições de eventos | Nome de subscrição de eventos |



## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/tópicos

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Eventos lettered mortos|de palavras|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason, Nome de EventosSubscrição|
|EntregaAttemptFailCount|No|Eventos falhados de entrega|de palavras|Total|Total de eventos não entregaram a esta subscrição do evento|Erro, ErrorType, EventSubscriptionName|
|EntregaSSuccessCount|Yes|Eventos Entregues|de palavras|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|No|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|DropEventCount|Yes|Eventos abandonados|de palavras|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|DropReason, Nome de EventosSubscription|
|MatchedEventCount|Yes|Eventos Combinados|de palavras|Total|Total de eventos combinados com esta subscrição de evento|Nome de subscrição de eventos|
|PublicarFailCount|Yes|Publicar Eventos Falhados|de palavras|Total|Total de eventos não publicou para este tópico|ErrorType, Error|
|PublicarSuccessCount|Yes|Eventos Publicados|de palavras|Total|Total de eventos publicados neste tópico|Sem Dimensões|
|Publicar Inimitrodências|Yes|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Sem Dimensões|
|InigualávelEventCount|Yes|Eventos incomparáveis|de palavras|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Sem Dimensões|
| Extra-Endação DeFilter Avançado | Yes | Avaliações avançadas de filtros | de palavras | Total | Total de filtros avançados avaliados em todas as subscrições de eventos | Tema,EventSubscriptionName, DomainEventSubscriptionName |

## <a name="next-steps"></a>Passos seguintes
Ver o seguinte artigo: [Registos de diagnóstico](diagnostic-logs.md)
