---
title: Reagir aos eventos da Azure Media Services
description: Este artigo descreve como usar a Azure Event Grid para subscrever eventos de Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 9bed493ca37d21d0c3e5c289bb8c26607975bdc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609723"
---
# <a name="handling-event-grid-events"></a>A processar eventos do Event Grid

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Os eventos de Media Services permitem que as aplicações reajam a diferentes eventos (por exemplo, o evento de mudança de estado de trabalho) usando arquiteturas modernas sem servidor. Fá-lo sem a necessidade de um código complicado ou de serviços de sondagens dispendiosos e ineficientes. Em vez disso, os eventos são empurrados através [da Azure Event Grid](https://azure.microsoft.com/services/event-grid/) para manipuladores de eventos como [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), ou mesmo para o seu próprio Webhook, e você só paga pelo que você usa. Para obter informações sobre preços, consulte [os preços da Grade de Eventos.](https://azure.microsoft.com/pricing/details/event-grid/)

A disponibilidade para eventos de Serviços de Mídia está ligada à [disponibilidade](../../../event-grid/overview.md) da Grade de Eventos e ficará disponível noutras regiões, como faz a Grade de Eventos.  

## <a name="media-services-events-and-schemas"></a>Eventos e esquemas de Serviços de Mídia

A grelha de eventos utiliza [subscrições de eventos](../../../event-grid/concepts.md#event-subscriptions) para encaminhar mensagens de evento para assinantes. Os eventos dos Serviços de Comunicação Social contêm toda a informação necessária para responder às alterações nos seus dados. Pode identificar um evento de Media Services porque a propriedade eventType começa com "Microsoft.Media.".

Para mais informações, consulte [os esquemas de eventos dos Media Services.](../media-services-event-schemas.md)

## <a name="practices-for-consuming-events"></a>Práticas para consumir eventos

As aplicações que lidam com eventos de Serviços de Mídia devem seguir algumas práticas recomendadas:

* Como várias subscrições podem ser configuradas para encaminhar eventos para o mesmo manipulador de eventos, é importante não assumir que os eventos são de uma determinada fonte, mas para verificar o tópico da mensagem para garantir que ele vem da conta de armazenamento que você está esperando.
* Da mesma forma, verifique se o eventType é aquele que está preparado para processar, e não assuma que todos os eventos que receber serão os tipos que espera.
* Ignore campos que não entende.  Esta prática irá ajudá-lo a manter-se resiliente a novas funcionalidades que poderão ser adicionadas no futuro.
* Utilize o prefixo "sujeito" e os jogos de sufixo para limitar os eventos a um determinado evento.

> [!NOTE]
> Os eventos estão sujeitos ao Acordo de Nível de Serviço de Grelha de [Eventos (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/). Se pretender obter notificações de eventos utilizando APIs, consulte exemplos sobre como consumir eventos, com [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) ou [Java SDK](https://github.com/Azure-Samples/media-services-v3-java).

## <a name="next-steps"></a>Passos seguintes

* [Monitorizar eventos - portal](../monitor-events-portal-how-to.md)
* [Monitorizar eventos - CLI](../job-state-events-cli-how-to.md)
