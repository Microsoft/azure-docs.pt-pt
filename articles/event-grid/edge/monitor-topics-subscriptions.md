---
title: Monitore tópicos e subscrições de eventos - Azure Event Grid IoT Edge | Microsoft Docs
description: Monitorizar tópicos e subscrições de eventos
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: d230be4f74abd61ad7b7f0fdb3facb32ee63da73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171538"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Monitorizar tópicos e subscrições de eventos

A Grade de Eventos no Edge expõe uma série de métricas para tópicos e subscrições de eventos no [formato de exposição Prometheus](https://prometheus.io/docs/instrumenting/exposition_formats/). Este artigo descreve as métricas disponíveis e como habilitá-las.

## <a name="enable-metrics"></a>Ativar métricas

Configure o módulo para emitir métricas, definindo a `metrics__reporterType` variável ambiente para `prometheus` no recipiente criar opções:

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

As métricas estarão disponíveis `5888/metrics` no módulo para http e para `4438/metrics` https. Por exemplo, `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` para http. Neste ponto, um módulo de métricas pode sondar o ponto final para recolher métricas como neste [exemplo arquitetura](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Métricas disponíveis

Tanto os tópicos como as subscrições de eventos emitem métricas para lhe dar informações sobre a entrega de eventos e o desempenho do módulo.

### <a name="topic-metrics"></a>Métricas de tópicos

| Metric | Descrição |
| ------ | ----------- |
| EventosVivido | Número de eventos publicados para o tema
| Eventos Incomparáveis | Número de eventos publicados no tópico que não correspondem a uma Subscrição de Eventos e são abandonados
| SucessoReques | Número de pedidos de entrada de entrada recebidos pelo tema
| SystemErrorRequests | Número de pedidos de publicação de entrada falhou devido a um erro do sistema interno
| UserErrorRequests | O número de pedidos de publicação de entrada falhou devido a um erro do utilizador, como o JSON mal formado
| SucessoRequestLatencyMs | Publicar latência de resposta de pedido em milissegundos


### <a name="event-subscription-metrics"></a>Métricas de subscrição de eventos

| Metric | Descrição |
| ------ | ----------- |
| EntregaSSuccessCounts | Número de eventos entregues com sucesso no ponto final configurado
| DeliveryFailureCounts | Número de eventos que não foram entregues ao ponto final configurado
| DeliverySuccessLatencyMs | Latência de eventos entregues com sucesso em milissegundos
| DeliveryFailureLatencyMs | Insuficiências de entrega de eventos em milissegundos
| SystemDelayForFirstAttemptMs | Atraso do sistema dos acontecimentos antes da primeira tentativa de entrega em milissegundos
| DeliveryAttemptsCount | Número de tentativas de entrega de eventos - sucesso e fracasso
| Contas Caducadas | Número de eventos que expiraram e não foram entregues ao ponto final configurado