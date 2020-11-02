---
title: Resolver problemas das recomendações de reservas do Azure
description: Este artigo ajuda-o a compreender e a resolver problemas com as recomendações de reservas do Azure apresentadas no portal do Azure.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: a3137d779908bf2791ca396068a8c9edf5d56739
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92492253"
---
# <a name="troubleshoot-azure-reservation-recommendations"></a>Resolver problemas das recomendações de reservas do Azure

Este artigo ajuda-o a compreender e a resolver problemas com as recomendações de reservas do Azure apresentadas no portal do Azure. Talvez não veja recomendações ou recomendações que não correspondam às suas expetativas. Por exemplo, talvez já tenha uma instância reservada para uma configuração de VM específica. Talvez espere ver uma recomendação para uma configuração de VM semelhante.

## <a name="symptoms"></a>Sintomas

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) e navegue para **Reservas** .
2. Selecione **+ Adicionar** e, em seguida, selecione um produto.
3. Talvez não veja recomendações ou recomendações que não correspondam às suas expetativas no separador **Recomendado** .

## <a name="cause"></a>Causa

A lista de produtos recomendados é gerada pela quantidade de utilizações que tem para o seu âmbito (partilhado ou único), em comparação com o custo de uma reserva para o produto. Se o mecanismo de recomendação detetar economia, recomendará um produto para compra. No entanto, quando o mecanismo não identifica nenhuma economia, não recomenda produtos.

Quando estiver a executar um recurso com uma determinada configuração, não há garantia de que economizará dinheiro se comprar uma reserva para essa configuração. Pode ter utilização esporádica, por exemplo. Neste caso, o custo total da reserva pode não permitir economizar dinheiro durante a duração do período de reserva.

Também é importante compreender como a seleção de âmbito afeta as recomendações. Quando o âmbito é definido como **Partilhado** , as recomendações na lista mostram as instâncias reservadas em que o Azure encontra a economia para toda a inscrição associada à subscrição de faturação. Quando o âmbito é definido como **Único** , as recomendações na lista aplicam-se apenas a recursos que são executados na subscrição. É possível que alguns tamanhos de VM sejam recomendados para um âmbito, mas não para outro. Por exemplo, pode ter a utilização agregada de **Standard_B1ls** na sua inscrição, que seja elevada o suficiente para justificar o custo da compra de uma reserva no âmbito de inscrição. No entanto, uma única subscrição na inscrição pode não ter utilização suficiente para justificar o custo da compra de uma reserva no âmbito. Alterar o âmbito entre **Partilhado** e **Único** pode produzir recomendações diferentes.

O Azure pode recomendar a compra de uma reserva para determinados períodos e não para outros, com base nas economias de custo identificadas. Especificamente, os períodos de três anos têm descontos maiores do que os períodos de um ano. É mais provável que o Azure encontre economias para um período de três anos do que para um período de um ano.

Se quiser compreender por que o Azure recomenda um tamanho e uma quantidade específicos de recursos, selecione **&lt;Quantidade&gt; Consulte detalhes** para obter uma visualização detalhada, apresentando possíveis economias ao longo do tempo.

:::image type="content" source="./media/troubleshoot-reservation-recommendation/see-details-link.png" alt-text="Exemplo que mostra a recomendação de reserva Consulte a hiperligação detalhes" lightbox="./media/troubleshoot-reservation-recommendation/see-details-link.png" :::

## <a name="solution"></a>Solução

Pode comprar qualquer quantidade de reserva para um período que pretenda. Comprar uma reserva com uma quantidade e um período que difiram da recomendação provavelmente resultará numa economia e utilização mais baixas do que o ideal.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações acerca de recomendações de reserva, consulte [Recomendações de compra de reserva](determine-reservation-purchase.md).
