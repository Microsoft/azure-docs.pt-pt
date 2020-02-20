---
title: Comprar reservas do Azure com pagamentos adiantados ou mensais
description: Saiba como pode comprar reservas do Azure com pagamentos adiantados ou mensais.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 2689bdeb003cec0050141374165142a62f9ea337
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200406"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Comprar reservas com pagamentos mensais

Até agora, as reservas do Azure exigiam um pagamento adiantado. Agora, pode pagar as reservas através de pagamentos mensais. Ao contrário de uma compra antecipada em que paga o montante total, a opção de pagamento mensal divide o custo total da reserva em partes iguais para cada mês do período. O custo total de reservas pagas em adiantado ou mensalmente é o mesmo e não pagará qualquer taxa adicional ao optar por pagamentos mensais.

O montante de pagamento mensal pode variar consoante a taxa de câmbio da moeda local.

Os pagamentos mensais estão disponíveis para:

- Máquinas virtuais
- Storage do Azure
- Bases de Dados SQL
- SQL Data Warehouse
- BD do Cosmos
- Imposto de selo do Serviço de Aplicações
- Disco Gerido
- Azure Data Explorer
- Azure Database for MariaDB, MySQL e PostgreSQL
- Azure VMware Solution by CloudSimple


Compre reservas no [portal do Azure](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![Exemplo que mostra a compra de reservas](./media/monthly-payments-reservations/purchase-reservation.png)

Ao fazer uma compra de reservas, pode ver o plano de pagamento. Clique em **Ver todo o plano de pagamento**.

![Exemplo que mostra o plano de pagamento de reservas](./media/monthly-payments-reservations/prepurchase-schedule.png)

Para ver o plano de pagamentos após a compra, selecione a reserva, clique em **ID de encomenda de reserva** e, em seguida, clique no separador **Pagamentos**.

## <a name="view-payments-made"></a>Ver os pagamentos efetuados

Pode ver os pagamentos que foram efetuados através de APIs, dados de utilização e análise de custos. Para as reservas pagas mensalmente, o valor de frequência é apresentado como **periódico** nos dados de utilização e na API de Custos de Reservas. Para as reservas pagas antecipadamente, o valor é apresentado como **único**.

Análise de custos que mostra as compras mensais na vista predefinida. Aplique o filtro **compra** em **Tipo de custo** e **periódico** em **Frequência** para ver todas as compras. Para ver apenas as reservas, aplique um filtro para **Reserva**.

![Exemplo que mostra os custos da compra de reservas na análise de custos](./media/monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Mudar para pagamentos mensais na renovação

Quando renova uma reserva, pode alterar a frequência da faturação para mensal.

## <a name="exchange-and-refunds"></a>Troca e reembolsos

Tal como outras reservas, pode pedir o reembolso ou trocar reservas compradas com faturação mensal. Atualmente, pode submeter um pedido de suporte para iniciar uma troca ou reembolso para uma reserva comprada com faturação mensal.

Ao trocar uma reserva paga mensalmente, o custo de duração total da nova compra deve ser superior ao dos pagamentos restantes que são cancelados para a reserva devolvida. Não existem quaisquer outros limites ou taxas para trocas. Pode trocar uma reserva paga antecipadamente para comprar uma nova reserva que é faturada mensalmente. No entanto, o valor de duração da nova reserva deve ser superior ao valor proporcional da reserva a ser devolvida.

Caso cancele uma reserva paga mensalmente, a Microsoft poderá aplicar uma taxa de cancelamento aos futuros pagamentos consolidados que são cancelados. Os restantes pagamentos consolidados acumulam-se até o limite de reembolso de 50 000 USD.

Para obter mais informações sobre trocas e reembolsos, veja [Trocas e reembolsos personalizados das Reservas do Azure](exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre as reservas, veja [O que são as Reservas do Azure?](save-compute-costs-reservations.md)
- Para saber saber mais sobre as tarefas que deve realizar antes de comprar uma reserva, veja [Determinar o tamanho da VM adequado antes de comprar](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)
