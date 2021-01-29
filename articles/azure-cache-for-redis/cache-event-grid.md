---
title: Cache Azure para Redis Event Grid Overview
description: Use a grelha de eventos Azure para publicar Azure Cache para eventos Redis.
author: curib
ms.author: cauribeg
ms.date: 12/21/2020
ms.topic: conceptual
ms.service: cache
ms.openlocfilehash: 0a0809076367356739dfeadcf8dd63f88866a987
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056310"
---
# <a name="azure-cache-for-redis-event-grid-overview"></a>Cache Azure para Redis Event Grid Overview 

Azure Cache para eventos Redis, tais como patching, escala, eventos de importação/exportação (RDB) são empurrados usando [a Azure Event Grid](https://azure.microsoft.com/services/event-grid/) para assinantes como Azure Functions, Azure Logic Apps, ou mesmo para o seu próprio ouvinte http. A Event Grid fornece uma entrega fiável de eventos às suas aplicações através de políticas de relagem ricas e letras mortas.

Consulte o artigo de [esquema de eventos da Azure Cache para ver](../event-grid/event-schema-azure-cache.md) a lista completa dos eventos que a Azure Cache para Redis suporta.

Se quiser experimentar Azure Cache para eventos redis, consulte qualquer um destes quickstarts:

|Se quiser utilizar esta ferramenta:    |Veja este arranque rápido: |
|--|-|
|Portal do Azure    |[Quickstart: Route Azure Cache para eventos Redis para o ponto final da web com o portal Azure](cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Quickstart: Route Azure Cache para eventos redis para o ponto final da web com PowerShell](cache-event-grid-quickstart-powershell.md)|
|CLI do Azure    |[Quickstart: Route Azure Cache para eventos Redis para o ponto final da web com Azure CLI](cache-event-grid-quickstart-cli.md)|

## <a name="the-event-model"></a>O modelo do evento

O Event Grid utiliza [subscrições de eventos](../event-grid/concepts.md#event-subscriptions) para encaminhar mensagens de eventos para assinantes. Esta imagem ilustra a relação entre editores de eventos, subscrições de eventos e manipuladores de eventos.

:::image type="content" source="media/cache-event-grid/event-grid-model.png" alt-text="Modelo de grelha de evento.":::

Primeiro, subscreva um ponto final para um evento. Em seguida, quando um evento é desencadeado, o serviço de Grade de Eventos enviará dados sobre esse evento para o ponto final.

Consulte o artigo de esquema de [eventos da Azure Cache for Redis](../event-grid/event-schema-azure-cache.md) para ver:

> [!div class="checklist"]
> * Uma lista completa de Azure Cache para eventos Redis e como cada evento é desencadeado.
> * Um exemplo dos dados que a Grade de Eventos enviaria para cada um destes eventos.
> * O propósito de cada par de valor chave que aparece nos dados.


## <a name="best-practices-for-consuming-events"></a>Melhores práticas para consumir eventos

As aplicações que lidam com a Azure Cache para eventos Redis devem seguir algumas práticas recomendadas:
> [!div class="checklist"]
> * Como várias subscrições podem ser configuradas para encaminhar eventos para o mesmo manipulador de eventos, é importante não assumir que os eventos são de uma determinada fonte, mas para verificar o tópico da mensagem para garantir que ele vem da Cache Azure para o caso Redis que você está esperando.
> * Da mesma forma, verifique se o eventType é aquele que está preparado para processar, e não assuma que todos os eventos que receber serão os tipos que espera.
> * A Azure Cache para eventos Redis garante pelo menos uma vez a entrega aos assinantes, o que garante que todas as mensagens são outputadas. No entanto, devido a retrações ou disponibilidade de subscrições, podem ocorrer mensagens duplicadas ocasionalmente. Para saber mais sobre a entrega de mensagens e retentou, consulte [a entrega de mensagens de Event Grid e retentou.](../event-grid/delivery-and-retry.md)


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Grade de Eventos e dê a Azure Cache para eventos Redis uma tentativa:

- [Sobre o Event Grid](../event-grid/overview.md)
- [Azure Cache para esquema de eventos redis](../event-grid/event-schema-azure-cache.md)
- [Rota Azure Cache para eventos Redis para web endpoint com Azure CLI](cache-event-grid-quickstart-cli.md)
- [Rota Azure Cache para eventos Redis para o ponto final da web com o portal Azure](cache-event-grid-quickstart-portal.md)
- [Route Azure Cache para eventos redis para o ponto final da web com PowerShell](cache-event-grid-quickstart-powershell.md)
