---
title: Monitorize tópicos e assinaturas de eventos - Azure Event Grid IoT Edge [ Microsoft Docs
description: Monitorize tópicos e assinaturas de eventos
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086669"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Monitorize tópicos e assinaturas de eventos

A Grelha de Eventos no Edge expõe uma série de métricas para tópicos e subscrições de eventos no formato de [exposição Prometheus](https://prometheus.io/docs/instrumenting/exposition_formats/). Este artigo descreve as métricas disponíveis e como as permitir.

## <a name="enable-metrics"></a>Ativar métricas

Configure o módulo para emitir métricas, definindo a `metrics__reporterType` variável ambiental para `prometheus` no recipiente criar opções:

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

As métricas estarão disponíveis no `5888/metrics` `4438/metrics` módulo para http e para https. Por exemplo, `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` para http. Neste ponto, um módulo de métricas pode sondar o ponto final para recolher métricas como neste [exemplo arquitetura](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Métricas disponíveis

Tanto os tópicos como as subscrições de eventos emitem métricas para lhe dar informações sobre a entrega de eventos e o desempenho do módulo.

### <a name="topic-metrics"></a>Métricas de tópicos

| Métrica | Descrição |
| ------ | ----------- |
| Eventos Recebidos | Número de eventos publicados para o tema
| Eventos Incomparáveis | Número de eventos publicados para o tópico que não correspondem a uma Subscrição de Eventos e são abandonados
| Pedidos de Sucesso | Número de pedidos de publicação recebidas pelo tópico
| Pedidos de erro do sistema | Número de pedidos de publicação de entrada falhou devido a um erro do sistema interno
| Pedidos de erro de utilizador | Número nos pedidos de publicação de entrada falhou devido a erro do utilizador, como jSON mal formado
| SuccessRequestLatencyMs | Publique latência de resposta de pedido em milissegundos


### <a name="event-subscription-metrics"></a>Métricas de subscrição de eventos

| Métrica | Descrição |
| ------ | ----------- |
| EntregaSSuccessCounts | Número de eventos entregues com sucesso no ponto final configurado
| Contagem de falhas de entrega | Número de eventos que não foram entregues ao ponto final configurado
| DeliverySuccessLatencyMs | Latência de eventos entregues com sucesso em milissegundos
| Falha de entregaAtrasom | Latência de falhas na entrega de eventos em milissegundos
| Atraso do sistemaForFirstAttemptMs | Atraso do sistema de eventos antes da primeira tentativa de entrega em milissegundos
| EntregaTentativasCount | Número de tentativas de entrega de eventos - sucesso e fracasso
| Contagem expirada | Número de eventos que expiraram e não foram entregues no ponto final configurado