---
title: Serviço de medição do mercado APIs - FAQ / Mercado Azure
description: Emita o uso de uma oferta SaaS no Mercado Azure.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6e5b691a41ef283449f9eeeb90e9d01a91616146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275786"
---
# <a name="marketplace-metering-service-apis---faq"></a>FAQ - APIs do serviço de medição do Marketplace

Uma vez que um utilizador Azure subscreva um serviço SaaS que inclui faturação medição, irá acompanhar o consumo de cada dimensão de faturação que está a ser utilizada pelo cliente. Se o consumo exceder as quantidades incluídas definidas para o termo selecionado pelo cliente, o seu serviço emitirá eventos de utilização para a Microsoft.

## <a name="emit-usage-events"></a>Emitir eventos de uso

>[!Note]
>Esta secção é aplicável apenas às ofertas da SaaS, onde pelo menos um dos planos tem dimensões de serviço de medição definidas no momento da publicação da oferta.

![Emitir eventos de uso](media/isv-emits-usage-event.png)

Consulte o evento de utilização do [lote SaaS API](./marketplace-metering-service-apis.md#batch-usage-event) para obter informações sobre o contrato da API para a emissão de eventos de utilização.

### <a name="how-often-is-it-expected-to-emit-usage"></a>Quantas vezes se espera que emita o uso?

Idealmente, espera-se que emita uso a cada hora durante a última hora, apenas se houver uso na hora anterior.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Qual é o atraso máximo entre o momento em que ocorre um evento, e o momento em que um evento de uso é emitido para a Microsoft?

Idealmente, o evento de uso é emitido a cada hora para eventos que ocorreram na última hora. No entanto, esperam-se atrasos. O atraso máximo permitido é de 24 horas, após o que os eventos de utilização não serão aceites.

Por exemplo, se um evento de uso ocorrer às 13:00 de um dia, você tem até às 13:00 do dia seguinte para emitir um evento de uso associado a este evento. Isto significa que no caso do sistema que emite o uso tem um tempo de paragem, pode recuperar e, em seguida, enviar o evento de utilização para o intervalo de hora em que o uso ocorreu, sem perda de fidelidade.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>O que acontece quando envias mais do que um evento de uso na mesma hora?

Apenas um evento de utilização é aceite para o intervalo de hora. O intervalo de hora começa no minuto 0 e termina ao minuto 59.  Se for emitida mais de um evento de utilização durante o intervalo da mesma hora, quaisquer eventos de utilização subsequentes são eliminados como duplicados.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>O que acontece quando emite uso para uma subscrição SaaS que já foi dessubscrita?

Qualquer evento de utilização emitido para a plataforma de marketplace não será aceite depois de uma subscrição SaaS ter sido eliminada.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Pode obter uma lista de todas as subscrições do SaaS, incluindo subscrições ativas e sem subscrição?

Sim, quando liga `GET /saas/subscriptions` para a API, inclui uma lista de todas as subscrições do SaaS. O campo de estado na resposta para cada subscrição do SaaS capta se a subscrição está ativa ou não subscrita. A chamada para listar Subscrições devolve um máximo de 100 subscrições no momento.

## <a name="next-steps"></a>Passos seguintes

- Consulte o serviço de [medição do Marketplace APIs](./marketplace-metering-service-apis.md) para obter mais informações.
