---
title: Reagindo aos eventos dos serviços de mídia do Azure | Microsoft Docs
description: Use a grade de eventos do Azure para assinar eventos de serviços de mídia.
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
ms.openlocfilehash: 64bf8f5c8de5f56ee1140e91d0472a33b35570cf
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68878804"
---
# <a name="handling-event-grid-events"></a>A processar eventos do Event Grid

Os eventos dos serviços de mídia permitem que os aplicativos reajam a eventos diferentes (por exemplo, o evento de alteração de estado do trabalho) usando arquiteturas modernas sem servidor. Ele faz isso sem a necessidade de código complicado ou serviços de sondagem caros e ineficientes. Em vez disso, os eventos são enviados por Push por meio da [grade de eventos do Azure](https://azure.microsoft.com/services/event-grid/) para manipuladores de eventos, como [Azure Functions](https://azure.microsoft.com/services/functions/), [aplicativos lógicos do Azure](https://azure.microsoft.com/services/logic-apps/)ou até mesmo para seu próprio webhook, e você paga apenas pelo que usar. Para obter informações sobre preços, consulte [preços da grade de eventos](https://azure.microsoft.com/pricing/details/event-grid/).

A disponibilidade dos eventos dos serviços de mídia está vinculada à [disponibilidade](../../event-grid/overview.md) da grade de eventos e será disponibilizada em outras regiões como a grade de eventos.  

## <a name="media-services-events-and-schemas"></a>Esquemas e eventos de serviços de mídia

A grade de eventos usa [assinaturas de evento](../../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para assinantes. Os eventos dos serviços de mídia contêm todas as informações necessárias para responder às alterações em seus dados. Você pode identificar um evento dos serviços de mídia porque a propriedade eventType começa com "Microsoft. Media.".

Para obter mais informações, consulte [esquemas de eventos dos serviços de mídia](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Práticas para consumo de eventos

Os aplicativos que lidam com os eventos dos serviços de mídia devem seguir algumas práticas recomendadas:

* Como várias assinaturas podem ser configuradas para rotear eventos para o mesmo manipulador de eventos, é importante não pressupor que os eventos sejam de uma fonte específica, mas para verificar o tópico da mensagem a fim de garantir que ela venha da conta de armazenamento que você espera.
* Da mesma forma, verifique se o eventType é um que você está preparado para processar e não presuma que todos os eventos recebidos serão os tipos esperados.
* Ignore os campos que você não entende.  Essa prática ajudará a mantê-lo resiliente a novos recursos que podem ser adicionados no futuro.
* Use o prefixo "Subject" e as correspondências de sufixo para limitar os eventos a um evento específico.

## <a name="next-steps"></a>Passos Seguintes

* [Monitorar eventos-Portal](monitor-events-portal-how-to.md)
* [Eventos de monitor-CLI](job-state-events-cli-how-to.md)
