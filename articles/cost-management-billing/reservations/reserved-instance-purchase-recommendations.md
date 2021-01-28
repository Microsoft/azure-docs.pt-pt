---
title: Recomendações de reservas do Azure
description: Saiba mais sobre as recomendações de reservas do Azure.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 4f6187ccb143f065fed236495128add7a2ab1ee4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928527"
---
# <a name="reservation-recommendations"></a>Recomendações da reserva

As recomendações de compra de instâncias reservadas (RI) do Azure são fornecidas através da [API de Recomendação de Reservas](/rest/api/consumption/reservationrecommendations) de Consumo do Azure, do [Assistente do Azure](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) e da experiência de compra de reservas no portal do Azure.

Os passos seguintes definem como são calculadas as recomendações:

1. O motor de recomendação avalia a utilização horária dos recursos no âmbito fornecido nos últimos 7, 30 e 60 dias.
2. Com base nos dados de utilização, o motor simula os custos com e sem reservas.
3. Os custos são simulados para diferentes quantidades, e é recomendada a quantidade que maximiza a poupança.
4. Se os recursos forem encerrados regularmente, a simulação não encontrará poupanças e não é fornecida nenhuma recomendação de compra.
5. Os cálculos da recomendação incluem quaisquer descontos especiais que possa ter nas suas tarifas de utilização a pedido.

## <a name="recommendations-in-the-azure-portal"></a>Recomendações no portal do Azure

As recomendações de compras de reservas também são apresentadas no portal do Azure na experiência de compra. As recomendações são apresentadas com a **Quantidade Recomendada**. Quando são compradas, a quantidade que o Azure recomenda dará a poupança máxima possível. Embora possa comprar qualquer quantidade que goste, se comprar uma quantidade diferente, as suas poupanças não serão ótimas.

Vejamos alguns exemplos do porquê.

Na imagem de exemplo seguinte relativa à recomendação selecionada, o Azure recomenda comprar uma quantidade de 6.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Exemplo que mostra uma recomendação de compra de reserva" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Mais informações sobre a recomendação aparecem quando seleciona **Ver detalhes**. A imagem seguinte mostra os detalhes da recomendação. A quantidade recomendada é calculada para o maior uso possível e baseia-se no seu uso histórico. Se a utilização for inconsistente, a recomendação poderá não ser para 100% de utilização. No exemplo, note-se que a utilização oscilou ao longo do tempo. É apresentada a reserva, as poupanças possíveis e a percentagem de utilização.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Exemplo mostrando detalhes para uma recomendação de compra de reserva " :::

O gráfico e os valores estimados mudam quando aumenta a quantidade recomendada. Ao aumentar a quantidade de reserva, as suas poupanças serão reduzidas porque acabará com uma redução do uso da reserva. Por outras palavras, pagará por reservas que não serão totalmente utilizadas.

Se baixar a quantidade de reserva, as suas poupanças também serão reduzidas. Embora tenha aumentado a utilização, haverá provavelmente períodos em que as reservas não cobrirão completamente a utilização. A utilização que exceda a quantidade da reserva será utilizada por recursos pay as you go mais caros. A imagem do exemplo seguinte ilustra esta lógica. Reduzimos a quantidade da reserva para 4 manualmente. A utilização da reserva é aumentada, mas as poupanças globais são reduzidas porque os custos de pagamento esmuido estão presentes.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Exemplo que mostra os detalhes de uma recomendação de compra de reserva alterada" :::

Para maximizar as poupanças com as reservas, tente comprar as reservas o mais perto possível da recomendação.

## <a name="recommendations-in-azure-advisor"></a>Recomendações do Assistente do Azure

As recomendações de compra de reservas estão disponíveis no Assistente do Azure. Tenha em consideração os seguintes pontos:

- O Assistente tem apenas recomendações de âmbito de subscrição única. Se quiser ver recomendações para todo o âmbito de faturação (conta de faturação ou perfil de faturação), então:
  -  No portal Azure, navegue para **Reservas**  >  **Adicionar** e, em seguida, selecione o tipo para o qual deseja ver as recomendações.
- Recomendações disponíveis no Advisor considerem a sua tendência de utilização de 30 dias.
- A quantidade e a poupança das recomendações destinam-se a uma reserva de três anos, sempre que disponível. Se uma reserva de três anos não for vendida para o serviço, a recomendação é calculada usando o preço de reserva de um ano.
- Os cálculos da recomendação incluem quaisquer descontos especiais que possa ter nas suas tarifas de utilização a pedido.
- Se comprar uma reserva de âmbito partilhado, as recomendações de compra de reservas do Advisor podem demorar até cinco dias a desaparecer.

## <a name="other-expected-api-behavior"></a>Outro comportamento esperado da API

- Ao utilizar um período do histórico de sete dias, é possível que não receba recomendações quando as VMs estiverem desligadas durante mais de um dia.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais acerca da forma [como o desconto de reserva do Azure é aplicado a máquinas virtuais](../manage/understand-vm-reservation-charges.md).
