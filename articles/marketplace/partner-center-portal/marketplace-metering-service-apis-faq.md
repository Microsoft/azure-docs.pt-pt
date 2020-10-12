---
title: Serviço de medição APIs FAQ - Mercado comercial da Microsoft
description: Perguntas frequentes sobre o serviço de medição APIs para ofertas de SaaS no Microsoft AppSource e Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/01/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: ddec23f695396b8322d51da62158a97456096ae8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320005"
---
# <a name="marketplace-metered-billing-apis---faq"></a>APIs de faturação medido no mercado - FAQ

Uma vez que um cliente subscreva um serviço SaaS, ou Aplicação Azure com um plano de Aplicações Geridas, com faturação medido, você rastreará o consumo para cada dimensão de faturação que está sendo usada.  Se o consumo exceder as quantidades incluídas definidas para o termo selecionado pelo cliente, o seu serviço emitirá eventos de utilização para a Microsoft.

## <a name="for-both-saas-offers-and-managed-apps"></a>Tanto para ofertas saaS como aplicações geridas

### <a name="how-often-is-it-expected-to-emit-usage"></a>Com que frequência se espera que emita uso?

Idealmente, espera-se que emita uso a cada hora durante a última hora, apenas se houver uso na hora anterior.

### <a name="is-there-a-maximal-period-between-one-emission-and-the-next-one"></a>Existe um período máximo entre uma emissão e a seguinte?

Não existe tal limitação. Só emitem o uso à medida que ocorrem. Por exemplo, se precisar apenas de submeter uma unidade de utilização por tempo de vida útil da subscrição, pode fazê-lo.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Qual é o atraso máximo entre o momento em que um evento ocorre e o tempo de envio de um evento de utilização à Microsoft?

Idealmente, o evento de uso é emitido a cada hora para eventos que ocorreram na última hora. No entanto, esperam-se atrasos. O atraso máximo permitido é de 24 horas, após o qual os eventos de utilização não serão aceites. A melhor prática é recolher o uso de hora a hora e emitir é como um evento no final da hora.

Por exemplo, se um evento de uso ocorrer às 13:00 de um dia, tem até às 13:00 do dia seguinte para emitir um evento de utilização associado a este evento.  Caso o sistema emite a utilização, pode recuperar e enviar o evento de utilização para o intervalo de hora em que o uso aconteceu, sem perda de fidelidade.

Se 24 horas passaram após o uso real, ainda pode emitir as unidades consumidas com eventos de utilização posteriores.  No entanto, esta prática pode prejudicar a credibilidade dos relatórios do evento de faturação para o cliente final.  Recomendamos que evite enviar a emissão do contador uma vez por dia/semana/mês.  Será mais difícil compreender o uso real de um cliente, e resolver questões ou questões que possam ser levantadas em relação a eventos de uso.

Outra razão para enviar o uso a cada hora é evitar situações que o utilizador cancela a subscrição antes que a editora envie o evento de emissões diária/semanal/mensal.

### <a name="what-happens-when-you-send-more-than-one-usage-event-in-the-same-hour"></a>O que acontece quando envias mais do que um evento de uso na mesma hora?

Apenas um evento de utilização é aceite para o intervalo de uma hora. O intervalo de hora começa ao minuto 0 e termina ao minuto 59.  Se mais de um evento de utilização for emitido durante a mesma hora, quaisquer eventos de utilização subsequentes são deixados como duplicados.

### <a name="what-happens-when-the-customer-cancels-the-purchase-within-the-time-allowed-by-the-cancellation-policy"></a>O que acontece quando o cliente cancela a compra dentro do tempo permitido pela política de cancelamento?

O valor forfetado não será cobrado, mas o uso por excesso será.

### <a name="can-custom-meter-plans-be-used-for-one-time-payments"></a>Os planos de contadores personalizados podem ser usados para pagamentos únicos?

Sim, pode definir uma dimensão personalizada como uma unidade de pagamento único e efetuá-la apenas uma vez para cada cliente.

### <a name="can-custom-meter-plans-be-used-to-tiered-pricing-model"></a>Os planos de contadores personalizados podem ser usados para o modelo de preços hierarquizados?

Sim, pode ser implementado com cada dimensão personalizada representando um único nível de preço.

Por exemplo, a Contoso quer cobrar $0,5 por e-mail para os primeiros 1000 e-mails, $0,4 por e-mail entre 1000 e 5000 e-mails e $0,2 por e-mail para mais de 5000 e-mails. Podem definir três dimensões personalizadas, que correspondem aos três níveis de preços de e-mail. Emitam unidades da primeira dimensão enquanto o número de e-mails permanecer abaixo de 1000, em seguida, unidades da segunda dimensão quando o número de e-mails é entre 1000 e 5000, e, finalmente, unidades da terceira dimensão para acima de 5000 e-mails.

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>O que acontece se o serviço de medição do Marketplace tiver uma paragem?

Se o ISV enviar um medidor personalizado e receber um erro, que pode ter sido causado por um problema do lado da Microsoft (geralmente no caso de eventos semelhantes terem sido aceites antes sem um erro), então o ISV deve esperar e voltar a tentar a emissão.

Se o erro persistir, em seguida, reenviar o medidor personalizado na hora seguinte (acumular a quantidade). Continue este processo até que seja recebida uma resposta de não-erro.

## <a name="for-saas-offers-only"></a>Para ofertas saas apenas

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>O que acontece quando emite uso para uma subscrição saaS que já foi desubscrita?

Qualquer evento de utilização emitido para a plataforma do mercado não será aceite depois de uma subscrição do SaaS ter sido eliminada.

A utilização só pode ser emitida para subscrições no estado subscrito (e não para subscrições em `PendingFulfillmentStart` `Suspended` , ou `Unsubscribed` estado).

A única exceção é reportar o uso do tempo que foi antes da subscrição do SaaS ter sido cancelada.

Por exemplo, o cliente cancelou a subscrição do SaaS hoje às 15h. Agora são 17h00, a editora ainda pode emitir uso para o período entre as 18:00 de ontem e as 15:00 de hoje para esta subscrição do SaaS.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Pode obter uma lista de todas as subscrições do SaaS, incluindo subscrições ativas e não subscritas?

Sim, quando liga para a [Lista de Assinaturas GET API,](pc-saas-fulfillment-api-v2.md#subscription-apis) uma vez que inclui uma lista de todas as subscrições do SaaS. O campo de estado na resposta para cada subscrição do SaaS capta se a subscrição está ativa ou não subscrita.

### <a name="are-the-start-and-end-dates-of-saas-subscription-term-and-overage-usage-emission-connected"></a>As datas de início e fim do termo de subscrição do SaaS e as emissões de utilização excessiva estão ligadas?

Os eventos overage podem ser emitidos em qualquer momento para a subscrição saaS existente no estado *de Subscrição.* É da responsabilidade da editora emitir eventos de uso com base na política definida no plano de faturação. A sobreavalagem deve ser calculada com base nas datas definidas no prazo da subscrição do SAAS. 

Por exemplo, se a editora definir um plano SaaS que inclui 1000 e-mails por $100 em taxa fixa mensal, cada e-mail acima de 1000 é cobrado $1 por dimensão personalizada.

Quando o cliente comprar e ativar a subscrição no dia 6 de janeiro, o e-mail 1000 incluído na taxa fixa será contado a partir deste dia. Assim, se até 5 de fevereiro (final do primeiro mês da subscrição) apenas 900 e-mails forem enviados, o cliente pagará a taxa fixa apenas para o primeiro mês desta subscrição, e nenhum evento de utilização excessiva será emitido pela editora entre 6 de janeiro e 5 de fevereiro. No dia 6 de fevereiro, a subscrição será automaticamente renovada e a contagem recomeçará. Se em 15 de fevereiro o cliente tiver chegado aos 1000 e-mails enviados, os restantes e-mails enviados até 5 de março serão cobrados como overage ($1 por e-mail) com base nos eventos de utilização excessivo emitidos pela editora.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações, consulte [o serviço de medição de marketplace APIs](./marketplace-metering-service-apis.md).
