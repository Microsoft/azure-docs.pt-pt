---
title: Monitor Azure Event Grid entrega de mensagens
description: Este artigo descreve como usar o portal Azure para ver o estado da entrega de mensagens Azure Event Grid.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 7a01ab91fe84aaa1fe55018754eddbf8b8f89643
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890859"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorizar a entrega de mensagens da Grelha de Eventos 

Este artigo descreve como usar o portal para ver o estado das entregas de eventos.

A Rede de Eventos proporciona uma entrega duradoura. Entrega cada mensagem pelo menos uma vez para cada subscrição. Os eventos são enviados imediatamente para o webhook registado de cada subscrição. Se um webhook não reconhecer a receção de um evento no prazo de 60 segundos após a primeira tentativa de entrega, a Rede de Eventos retenta a entrega do evento.

Para obter informações sobre entrega e repetições de eventos, entrega e retentativa de mensagens da Grelha de [Eventos.](delivery-and-retry.md)

## <a name="delivery-metrics"></a>Métricas de entrega

O portal exibe métricas para o estado de entrega de mensagens de eventos.

Para tópicos, aqui estão algumas das métricas:

* **Publish Succeeded**: Evento enviado com sucesso para o tema, e processado com uma resposta 2xx.
* **Publicar Failed**: Evento enviado para o tópico mas rejeitado com um código de erro.
* **Unmatched**: Evento publicado com sucesso para o tema, mas não corresponde a uma subscrição de evento. O evento foi abandonado.

Para subscrições, aqui estão algumas das métricas:

* **Entrega Bem sucedida**: Evento entregue com sucesso no ponto final da subscrição, e recebeu uma resposta 2xx.
* **Entrega Falhada**: Sempre que o serviço tenta entregar e o manipulador de eventos não devolve um código 2xx de sucesso, o contador **Failed delivery** é incrementado. Se tentarmos entregar o mesmo evento várias vezes e falharmos, o contador **Failed delivery** é incrementado para cada falha.
* **Eventos Expirados**: O evento não foi entregue e todas as tentativas de retenção foram enviadas. O evento foi abandonado.
* **Eventos Combinados**: Evento no tema foi acompanhado pela subscrição do evento.

    > [!NOTE]
    > Para a lista completa de métricas, consulte [Métricas suportadas pela Azure Event Grid](metrics.md).

## <a name="event-subscription-status"></a>Estado de subscrição de eventos

Para ver métricas para uma subscrição de evento, pode pesquisar por tipo de subscrição ou por subscrições para um recurso específico.

Para pesquisar por tipo de subscrição de eventos, selecione **Todos os serviços**.

![Selecione todos os serviços](./media/monitor-event-delivery/all-services.png)

Procure a grelha de **eventos** e selecione Assinaturas de Grelha de **Eventos** das opções disponíveis.

![Pesquisar subscrições de eventos](./media/monitor-event-delivery/search-and-select.png)

Filtrar pelo tipo de evento, subscrição e localização. Selecione **Métricas** para a subscrição visualizar.

![Filtrar assinaturas de eventos](./media/monitor-event-delivery/filter-events.png)

Veja as métricas para o tópico do evento e subscrição.

![Ver métricas de eventos](./media/monitor-event-delivery/subscription-metrics.png)

Para encontrar as métricas para um recurso específico, selecione esse recurso. Em seguida, selecione **Eventos**.

![Selecione eventos para um recurso](./media/monitor-event-delivery/select-events.png)

Vê as métricas das assinaturas para esse recurso.

## <a name="custom-event-status"></a>Estado do evento personalizado

Se publicou um tópico personalizado, pode ver as métricas para ele. Selecione o grupo de recursos para o tópico e selecione o tópico.

![Selecionar tópico personalizado](./media/monitor-event-delivery/select-custom-topic.png)

Veja as métricas para o tópico de evento personalizado.

![Ver métricas de eventos](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="set-alerts"></a>Definir alertas

Pode definir alertas sobre as métricas de nível de tópico e domínio para tópicos personalizados e domínios de eventos. Na lâmina geral para, selecione **Alertas** da esquerda tinha menu de recursos para visualizar, gerir e criar regras de alerta. [Saiba mais sobre alertas do Monitor Azure](../azure-monitor/platform/alerts-overview.md)

![Ver métricas de eventos](./media/monitor-event-delivery/select-alerts.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre entrega e repetições de eventos, entrega e retentativa de mensagens da Grelha de [Eventos.](delivery-and-retry.md)
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente a usar a Grelha de Eventos, consulte [create e encaminhe eventos personalizados com a Grelha de Eventos Azure](custom-event-quickstart.md).
