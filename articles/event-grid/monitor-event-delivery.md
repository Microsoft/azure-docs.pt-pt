---
title: Monitorar a entrega de mensagens da grade de eventos do Azure
description: Descreve como monitorar a entrega de mensagens da grade de eventos do Azure.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: spelluru
ms.openlocfilehash: b1035046cc3c3b6cd7bde895e2e779d1c966abe0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170022"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorar entrega de mensagem da grade de eventos 

Este artigo descreve como usar o portal para ver o status de entregas de eventos.

A grade de eventos fornece entrega durável. Ele entrega cada mensagem pelo menos uma vez para cada assinatura. Os eventos são enviados para o webhook registrado de cada assinatura imediatamente. Se um webhook não confirmar o recebimento de um evento dentro de 60 segundos da primeira tentativa de entrega, a grade de eventos tentará entregar o evento novamente.

Para obter informações sobre a entrega de eventos e repetições, [entrega de mensagens do Event Grid e volte a tentar](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Métricas de entrega

O portal exibe as métricas para o status de entrega de mensagens de evento.

Para tópicos, as métricas são:

* **Publicação bem-sucedida**: evento enviado com êxito para o tópico e processado com uma resposta 2xx.
* **Falha na publicação**: evento enviado ao tópico, mas rejeitado com um código de erro.
* Sem **correspondência**: evento publicado com êxito no tópico, mas não corresponde a uma assinatura de evento. O evento foi Descartado.

Para assinaturas, as métricas são:

* **Entrega bem-sucedida**: o evento foi entregue com êxito ao ponto de extremidade da assinatura e recebeu uma resposta 2xx.
* **Falha na entrega**: evento enviado ao ponto de extremidade da assinatura, mas recebeu uma resposta 4xx ou 5xx.
* **Eventos expirados**: o evento não foi entregue e todas as tentativas de repetição foram enviadas. O evento foi Descartado.
* **Eventos correspondentes**: o evento no tópico foi correspondido pela assinatura do evento.

## <a name="event-subscription-status"></a>Status da assinatura do evento

Para ver as métricas de uma assinatura de evento, você pode pesquisar por tipo de assinatura ou por assinaturas para um recurso específico.

Para pesquisar por tipo de assinatura de evento, selecione **todos os serviços**.

![Selecione todos os serviços](./media/monitor-event-delivery/all-services.png)

Pesquise a **grade de eventos** e selecione **assinaturas de grade de eventos** nas opções disponíveis.

![Pesquisar por assinaturas de evento](./media/monitor-event-delivery/search-and-select.png)

Filtre pelo tipo de evento, a assinatura e o local. Selecione as **métricas** para exibição da assinatura.

![Filtrar assinaturas de evento](./media/monitor-event-delivery/filter-events.png)

Exiba as métricas do tópico e da assinatura do evento.

![Exibir métricas de evento](./media/monitor-event-delivery/subscription-metrics.png)

Para localizar as métricas de um recurso específico, selecione esse recurso. Em seguida, selecione **eventos**.

![Selecionar eventos para um recurso](./media/monitor-event-delivery/select-events.png)

Você vê as métricas de assinaturas para esse recurso.

## <a name="custom-event-status"></a>Status do evento personalizado

Se você publicou um tópico personalizado, poderá exibir as métricas para ele. Selecione o grupo de recursos para o tópico e selecione o tópico.

![Selecionar tópico personalizado](./media/monitor-event-delivery/select-custom-topic.png)

Exiba as métricas para o tópico de evento personalizado.

![Exibir métricas de evento](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="set-alerts"></a>Definir alertas

Você pode definir alertas sobre as métricas de nível de domínio e de tópico para tópicos personalizados e domínios de evento. Na folha visão geral do, selecione **alertas** no menu de recursos da esquerda para exibir, gerenciar e criar regras de alerta. [Saiba mais sobre alertas de Azure Monitor](../azure-monitor/platform/alerts-overview.md)

![Exibir métricas de evento](./media/monitor-event-delivery/select-alerts.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a entrega de eventos e repetições, [entrega de mensagens do Event Grid e volte a tentar](delivery-and-retry.md).
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente com o Event Grid, veja [criar e encaminhar eventos personalizados com o Azure Event Grid](custom-event-quickstart.md).
