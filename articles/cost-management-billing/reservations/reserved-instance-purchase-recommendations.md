---
title: Recomendações de reservas do Azure
description: Saiba mais sobre as recomendações de reservas do Azure.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 869009d7dd26685842da3c948fbdc058a38feb21
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89398431"
---
# <a name="reservation-recommendations"></a>Recomendações da reserva

As recomendações de compra de instâncias reservadas (RI) do Azure são fornecidas através da [API de Recomendação de Reservas](/rest/api/consumption/reservationrecommendations) de Consumo do Azure, do [Assistente do Azure](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) e da experiência de compra de reservas no portal do Azure.

Os passos seguintes definem como são calculadas as recomendações:

1. O motor de recomendação avalia a utilização horária dos recursos no âmbito fornecido nos últimos 7, 30 e 60 dias.
2. Com base nos dados de utilização, o motor simula os custos com e sem reservas.
3. Os custos são simulados para diferentes quantidades, e é recomendada a quantidade que maximiza a poupança.
4. Se os recursos forem encerrados regularmente, a simulação não encontrará poupanças e não é fornecida nenhuma recomendação de compra.
5. O cálculo das recomendações inclui todos os descontos especiais que possa ter nas suas taxas de utilização a pedido.

## <a name="recommendations-in-the-azure-portal"></a>Recomendações no portal do Azure

As recomendações de compras de reservas também são apresentadas no portal do Azure na experiência de compra. As recomendações são apresentadas com a **Quantidade Recomendada**. Quando são compradas, a quantidade que o Azure recomenda dará a poupança máxima possível. Embora possa comprar a quantidade que pretenda, caso compre uma quantidade que não a recomendada, as poupanças não serão as ideais.

Vejamos alguns exemplos do porquê.

Na imagem de exemplo seguinte relativa à recomendação selecionada, o Azure recomenda comprar uma quantidade de 6.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Exemplo que mostra uma recomendação de compra de reserva" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Se selecionar a ligação **Ver detalhes**, são mostradas mais informações sobre a recomendação. A imagem seguinte mostra os detalhes da recomendação. A quantidade recomendada é calculada para a utilização máxima possível, com base no seu histórico de utilização. Se a utilização for inconsistente, a recomendação poderá não ser para 100% de utilização. No exemplo, repare que a utilização flutuou ao longo do tempo. É apresentada a reserva, as poupanças possíveis e a percentagem de utilização.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Exemplo que mostra os detalhes de uma recomendação de compra de reserva" :::

Quando aumenta ou diminui a quantidade da reserva para lá da recomendação, o gráfico e os valores estimados alteram-se. Ao aumentar a quantidade da reserva, as poupanças baixam porque acabará com uma utilização da reserva reduzida. Por outras palavras, pagará por reservas que não serão totalmente utilizadas.

Se diminuir a quantidade da reserva, as poupanças também serão reduzidas. Embora tenha aumentado a utilização, haverá provavelmente períodos em que as reservas não cobrirão completamente a utilização. A utilização que exceda a quantidade da reserva será utilizada por recursos pay as you go mais caros. A imagem do exemplo seguinte ilustra esta lógica. Reduzimos a quantidade da reserva para 4 manualmente. A utilização da reserva é aumentada, mas as poupanças gerais reduzidas, porque estão presentes custos pay as you go.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Exemplo que mostra os detalhes de uma recomendação de compra de reserva alterada" :::

Para maximizar as poupanças com as reservas, tente comprar as reservas o mais perto possível da recomendação.

## <a name="recommendations-in-azure-advisor"></a>Recomendações do Assistente do Azure

As recomendações de compra de reservas estão disponíveis no Assistente do Azure. Tenha em consideração os seguintes pontos:

- O Assistente tem apenas recomendações de âmbito de subscrição única. Se quiser ver as recomendações para todo o âmbito da faturação (conta de faturação ou perfil de faturação), aceda ao Portal do Azure > Reservas > Adicionar e selecione o tipo para o qual pretende as recomendações.
- Recomendações disponíveis na tendência de utilização do assistente de supervisor após 30 dias.
- As quantidades recomendadas e as poupanças são para reservas de 3 anos, sempre que disponíveis. Se não for vendida uma reserva de 3 anos para o serviço, a recomendação é calculada com base no preço da reserva de 1 ano.
- Os cálculos das recomendações incluem todos os descontos especiais que possa ter nas suas taxas de utilização a pedido.
- Se comprar uma reserva de âmbito partilhado, as recomendações de compra de reservas do Assistente podem demorar até 5 dias a desaparecer.

## <a name="other-expected-api-behavior"></a>Outro comportamento esperado da API

- Ao utilizar um período do histórico de sete dias, é possível que não receba recomendações quando as VMs estiverem desligadas durante mais de um dia.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais acerca da forma [como o desconto de reserva do Azure é aplicado a máquinas virtuais](../manage/understand-vm-reservation-charges.md).
