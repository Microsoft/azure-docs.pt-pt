---
title: Reagindo aos eventos da Azure Media Services Microsoft Docs
description: Este artigo descreve como usar a Rede de Eventos Azure para subscrever eventos de Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/08/2019
ms.author: juliako
ms.openlocfilehash: e24bacb0ea7ab406442022915872fc77e9cc1a5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887889"
---
# <a name="handling-event-grid-events"></a>A processar eventos do Event Grid

Os eventos dos Media Services permitem que as aplicações reajam a diferentes eventos (por exemplo, o evento de mudança de estado de emprego) utilizando arquiteturas modernas sem servidores. Fá-lo sem a necessidade de código sumo ou serviços de sondagens dispendiosos e ineficientes. Em vez disso, os eventos são empurrados através da Rede de [Eventos Azure](https://azure.microsoft.com/services/event-grid/) para manipuladores de eventos como [Funções Azure,](https://azure.microsoft.com/services/functions/) [Aplicações Lógicas Azure,](https://azure.microsoft.com/services/logic-apps/)ou até mesmo para o seu próprio Webhook, e você só paga pelo que você usa. Para obter informações sobre preços, consulte [os preços da Grelha de Eventos](https://azure.microsoft.com/pricing/details/event-grid/).

A disponibilidade para eventos de Serviços de Media está ligada à [disponibilidade](../../event-grid/overview.md) da Rede de Eventos e ficará disponível noutras regiões, como faz a Event Grid.  

## <a name="media-services-events-and-schemas"></a>Eventos e schemas de Serviços de Media

A grelha do evento utiliza subscrições de [eventos](../../event-grid/concepts.md#event-subscriptions) para direcionar mensagens de eventos para assinantes. Os eventos dos Serviços de Media contêm todas as informações necessárias para responder a alterações nos seus dados. Pode identificar um evento de Media Services porque a propriedade eventType começa com "Microsoft.Media.".

Para mais informações, consulte o [evento de Media Services.](media-services-event-schemas.md)

## <a name="practices-for-consuming-events"></a>Práticas para consumir eventos

As aplicações que lidam com eventos de Serviços de Media devem seguir algumas práticas recomendadas:

* Como várias subscrições podem ser configuradas para direcionar eventos para o mesmo manipulador de eventos, é importante não assumir que os eventos são de uma determinada fonte, mas para verificar o tópico da mensagem para garantir que vem da conta de armazenamento que você está esperando.
* Da mesma forma, verifique se o eventoType é um que está preparado para processar, e não assuma que todos os eventos que receber serão os tipos que espera.
* Ignore campos que não entende.  Esta prática ajudará a mantê-lo resiliente a novas funcionalidades que poderão ser adicionadas no futuro.
* Utilize os fósforos de prefixo e sufixo "sujeito" para limitar os eventos a um determinado evento.

> [!NOTE]
> Os eventos estão sujeitos ao Acordo de Nível de Serviço da Grelha de Eventos [(SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/). Se quiser receber notificações de eventos utilizando APIs, consulte exemplos de como consumir eventos, com [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) ou [Java SDK](https://github.com/Azure-Samples/media-services-v3-java).

## <a name="next-steps"></a>Passos seguintes

* [Monitorizar eventos - portal](monitor-events-portal-how-to.md)
* [Monitorizar eventos - CLI](job-state-events-cli-how-to.md)
