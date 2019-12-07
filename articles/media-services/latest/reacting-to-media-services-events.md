---
title: Reagindo aos eventos dos serviços de mídia do Azure | Microsoft Docs
description: Este artigo descreve como usar a grade de eventos do Azure para assinar eventos de serviços de mídia.
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
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74887889"
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

> [!NOTE]
> Os eventos estão sujeitos à Contrato de Nível de Serviço de grade de eventos [(SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/). Se você quiser obter notificações de eventos usando APIs, consulte exemplos de como consumir eventos, com o SDK do [.net](https://github.com/Azure-Samples/media-services-v3-dotnet) ou o [SDK do Java](https://github.com/Azure-Samples/media-services-v3-java).

## <a name="next-steps"></a>Passos seguintes

* [Monitorar eventos-Portal](monitor-events-portal-how-to.md)
* [Eventos de monitor-CLI](job-state-events-cli-how-to.md)
