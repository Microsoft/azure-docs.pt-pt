---
title: Reagir a eventos de serviços de multimédia do Azure | Documentos da Microsoft
description: Utilize o Azure Event Grid para subscrever a eventos de serviços de multimédia.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: cb5d6474a0c830933c712e1008015b5220617c96
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57850909"
---
# <a name="handling-event-grid-events"></a>A processar eventos do Event Grid

Os eventos de serviços de multimédia permitem que os aplicativos reagir a eventos diferentes (por exemplo, o tarefa evento de alteração Estado), usando as modernas arquiteturas sem servidor. Ele faz isso sem a necessidade de código complicado ou serviços de consulta dispendiosa e ineficiente. Em vez disso, os eventos são enviados por meio [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) a manipuladores de eventos, tal como [as funções do Azure](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), ou até mesmo para o seu próprio Webhook e pagar apenas o que usa. Para obter informações sobre preços, consulte [preços do Event Grid](https://azure.microsoft.com/pricing/details/event-grid/).

Disponibilidade para eventos de serviços de multimédia é associada ao Event Grid [disponibilidade](../../event-grid/overview.md) e ficará disponível noutras regiões como o Event Grid.  

## <a name="media-services-events-and-schemas"></a>Eventos de serviços de multimédia e esquemas

Grelha de eventos usa [subscrições de eventos](../../event-grid/concepts.md#event-subscriptions) encaminhar mensagens de eventos para os assinantes. Eventos de serviços de multimédia contêm todas as informações que necessárias para responder a alterações nos seus dados. É possível identificar um evento de serviços de multimédia, porque a propriedade eventType começa com "Microsoft.Media.".

Para obter mais informações, consulte [esquemas de eventos dos serviços de multimédia](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Práticas recomendadas para o consumo de eventos

Aplicações que processam os eventos de serviços de multimédia devem seguir algumas práticas recomendadas:

* Como várias subscrições podem ser configuradas para encaminhar eventos para o mesmo manipulador de eventos, é importante não partem do princípio de eventos são de uma origem específica, mas para verificar o tópico da mensagem para se certificar de que trata da conta de armazenamento que está esperando.
* Da mesma forma, verifique se o eventType é um estão preparados para o processo e não partem do princípio de que todos os eventos recebidos serão os tipos esperados.
* Ignore campos que não compreende.  Essa prática ajuda a manter-se resiliente aos novos recursos que podem ser adicionados no futuro.
* Utilize as correspondências de prefixo e o sufixo "assunto" para limitar os eventos para um determinado evento.

## <a name="next-steps"></a>Passos Seguintes

[Obter eventos de estado de tarefa](job-state-events-cli-how-to.md)
