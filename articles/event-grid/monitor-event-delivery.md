---
title: Monitorizar a entrega de mensagens do Azure Event Grid
description: Descreve como monitorizar a entrega de mensagens do Azure Event Grid.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: spelluru
ms.openlocfilehash: fdd18b833794c25cb90188ba8bc418d4785492ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824161"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorizar a entrega de mensagens do Event Grid 

Este artigo descreve como utilizar o portal para ver o estado de entregas de evento.

Event Grid fornece entrega durável. Ele fornece a cada mensagem, pelo menos, uma vez para cada subscrição. Eventos são enviados para o webhook registado de cada subscrição imediatamente. Se um webhook não confirmar a receção de um evento em 60 segundos, da primeira tentativa de entrega, o Event Grid repete a entrega do evento.

Para obter informações sobre a entrega de eventos e repetições, [entrega de mensagens do Event Grid e volte a tentar](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Métricas de entrega

O portal apresenta métricas para o estado de entrega de mensagens de eventos.

Para tópicos, as métricas são:

* **Êxito ao publicar**: Evento com êxito enviadas para o tópico e processada com uma resposta de 2xx.
* **Falha ao publicar**: Evento enviadas para o tópico, mas rejeitada com um código de erro.
* **Sem correspondência**: Evento publicada com êxito para o tópico, mas não corresponde a uma subscrição de evento. O evento foi ignorado.

Para subscrições, as métricas são:

* **Entrega concluída com êxito**: Evento fornecidos ao ponto final da subscrição com êxito e recebeu uma resposta de 2xx.
* **A entrega falhou**: Eventos enviados para o ponto de extremidade da subscrição, mas receberam uma resposta 4xx ou 5xx.
* **Expirado eventos**: Evento não foi entregue e todas as tentativas de repetição foram enviadas. O evento foi ignorado.
* **Eventos de correspondência**: Eventos no tópico foi correspondido pela subscrição de evento.

## <a name="event-subscription-status"></a>Estado de subscrição de evento

Para ver as métricas para uma subscrição de evento, pode optar por procurar por tipo de subscrição ou subscrições para um recurso específico.

Para procurar por tipo de subscrição de evento, selecione **todos os serviços**.

![Selecione todos os serviços](./media/monitor-event-delivery/all-services.png)

Procure **grelha de eventos** e selecione **subscrições do Event Grid** nas opções disponíveis.

![Procurar subscrições de eventos](./media/monitor-event-delivery/search-and-select.png)

Filtrar pelo tipo de evento, a subscrição e localização. Selecione **métricas** para a subscrição ver.

![Filtrar subscrições de eventos](./media/monitor-event-delivery/filter-events.png)

Ver as métricas para o tópico do event e subscrição.

![Ver métricas de eventos](./media/monitor-event-delivery/subscription-metrics.png)

Para localizar as métricas de um recurso específico, selecione esse recurso. Em seguida, selecione **eventos**.

![Selecionar eventos para um recurso](./media/monitor-event-delivery/select-events.png)

Ver as métricas para subscrições para esse recurso.

## <a name="custom-event-status"></a>Estado do evento personalizado

Se publicar um tópico personalizado, pode ver as métricas para o mesmo. Selecione o grupo de recursos para o tópico e selecione o tópico.

![Selecionar tópico personalizado](./media/monitor-event-delivery/select-custom-topic.png)

Ver as métricas para o tópico de evento personalizado.

![Ver métricas de eventos](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>Passos Seguintes

* Para obter informações sobre a entrega de eventos e repetições, [entrega de mensagens do Event Grid e volte a tentar](delivery-and-retry.md).
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente com o Event Grid, veja [criar e encaminhar eventos personalizados com o Azure Event Grid](custom-event-quickstart.md).
