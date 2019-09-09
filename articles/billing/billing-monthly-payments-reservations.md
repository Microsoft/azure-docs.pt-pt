---
title: Comprar reservas do Azure com pagamentos antecipados ou mensais
description: Saiba como você pode comprar as reservas do Azure com pagamentos iniciais ou mensais.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: banders
ms.openlocfilehash: d211334ad2aa760cd63b98c6827fb2512811a1d3
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806951"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Comprar reservas com pagamentos mensais

Até agora, as reservas do Azure exigiram o pagamento antecipado. Agora você pode pagar por reservas com pagamentos mensais. Ao contrário de uma compra antecipada em que você paga o valor total, a opção pagamento mensal divide o custo total da reserva uniformemente em cada mês do termo. O custo total de reservas antecipadas e mensais é o mesmo e você não paga nenhuma taxa adicional quando opta por pagar mensalmente.

Seu valor de pagamento mensal pode variar, dependendo da taxa de câmbio de mercado do mês atual para sua moeda local.

Os pagamentos mensais estão disponíveis para:

- Máquinas virtuais
- SQL Database
- SQL Data Warehouse
- BD do Cosmos
- Taxa de selo do serviço de aplicativo

Compre reservas no [portal do Azure](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![Exemplo mostrando a compra de reserva](./media/billing-monthly-payments-reservations/purchase-reservation.png)

Ao fazer uma compra de reserva, você pode exibir o plano de pagamento. Clique em **exibir agendamento de pagamento completo**.

![Exemplo mostrando agendamento de pagamento de reserva](./media/billing-monthly-payments-reservations/prepurchase-schedule.png)

Para exibir o agendamento de pagamentos após a compra, selecione uma reserva, clique na **ID do pedido de reserva**e clique na guia **pagamentos** .

## <a name="view-payments-made"></a>Exibir pagamentos feitos

Você pode exibir os pagamentos que foram feitos usando APIs, dados de uso e análise de custo. Para as reservas pagas mensalmente, o valor de Frequency é mostrado como **recorrente** em dados de uso e na API de encargos de reserva. Para reservas pagas antecipadamente, o valor é mostrado como **OneTime**.

A análise de custo mostra as compras mensais na exibição padrão. Aplique o filtro de **compra** para o **tipo de encargo** e **recorrência** da **frequência** para ver todas as compras. Para exibir somente as reservas, aplique um filtro para **reserva**.

![Exemplo mostrando custos de compra de reserva na análise de custo](./media/billing-monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Alternar para pagamentos mensais na renovação

Ao renovar uma reserva, você pode alterar a frequência de cobrança para mensal.

## <a name="exchange-and-refunds"></a>Exchange e reembolsos

Assim como outras reservas, você pode reembolsar ou trocar reservas adquiridas com cobrança mensal. No momento, você pode enviar uma solicitação de suporte para iniciar uma troca ou reembolso para uma reserva adquirida com cobrança mensal.

Quando você troca uma reserva paga mensalmente, o custo total de vida útil da nova compra deve ser maior do que os pagamentos restantes cancelados para a reserva retornada. Não há outros limites ou taxas para trocas. Você pode trocar uma reserva paga por antecipação para comprar uma nova reserva que é cobrada mensalmente. No entanto, o valor de tempo de vida da nova reserva deve ser maior que o valor rateado da reserva que está sendo retornada.

Se cancelar uma reserva paga mensalmente, a Microsoft poderá aplicar uma taxa de cancelamento aos futuros pagamentos confirmados que foram cancelados. Os pagamentos confirmados restantes se acumulam até o limite de reembolso de US $ $50000.

Para obter mais informações sobre o Exchange e reembolsos, consulte trocas [e reembolsos de autoatendimento para reservas do Azure](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="faq"></a>FAQ

P. O Azure oferece "reservas de front-end parciais?"<br>
R. Não. Como os custos das reservas pagas antecipadamente e mensalmente são os mesmos, a Microsoft não dá suporte a pagamentos de início parcial.

P. Os pagamentos mensais estão disponíveis para o programa CSP (provedor de soluções Microsoft Cloud)?<br>
R. Sim, os parceiros podem comprar reservas para seus clientes do CSP na portal do Azure. A capacidade de comprar reservas com cobrança mensal não está disponível no Partner Center.

P. Sou cliente do Azure governamental dos EUA, posso pagar mensalmente por compras de reserva?<br>
R. Atualmente não.

P. Quando posso trocar ou reembolsar na portal do Azure, em vez de criar um tíquete de suporte?<br>
R. Atualmente não. As solicitações para troca e reservas de reembolso com pagamentos mensais são manipuladas pelo suporte do Azure.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre reservas, confira [o que são as reservas do Azure?](billing-save-compute-costs-reservations.md)
- Para saber mais sobre as tarefas que você deve realizar antes de comprar uma reserva, consulte [determinar o tamanho correto da VM antes de comprar](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)
