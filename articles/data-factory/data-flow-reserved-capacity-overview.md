---
title: Economize custos de cálculo com capacidade reservada
description: Saiba como comprar o fluxo de dados da Azure Data Factory reservado capacidade para economizar nos seus custos de cálculo.
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.date: 01/25/2021
ms.openlocfilehash: 8bea1ff0dcb945e969553a4f643e289a5157f966
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811723"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>Poupar custos para recursos com capacidade reservada - Fluxos de dados da Azure Data Factory

Poupe dinheiro com os custos de fluxo de dados da Azure Data Factory comprometendo-se a uma reserva de recursos computativos em comparação com os preços de pay-as-you-go. Com capacidade reservada, compromete-se a utilizar o fluxo de dados da ADF por um período de um ou três anos para obter um desconto significativo nos custos de computação. Para adquirir capacidade reservada, precisa especificar a região de Azure, tipo de cálculo, contagem de núcleos e termo.

Não precisa de atribuir a reserva a uma determinada fábrica ou tempo de execução de integração. As fábricas existentes ou as fábricas recém-implantadas obtêm automaticamente o benefício. Ao comprar uma reserva, compromete-se a usar os custos de cálculo do fluxo de dados por um período de um ou três anos. Assim que comprar uma reserva, os encargos computacional que correspondam aos atributos da reserva já não são cobrados nas tarifas pagas. 

Pode comprar capacidade reservada no [portal Azure.](https://portal.azure.com) Pague pela reserva [antecipadamente ou com pagamentos mensais](https://docs.microsoft.com/azure/cost-management-billing/reservations/prepare-buy-reservation.md). Para comprar capacidade de reserva:

- Você deve estar na função de proprietário para pelo menos uma Enterprise ou subscrição individual com taxas pay-as-you-go.
- Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com). Ou, se essa definição estiver desativada, deve ser um Administrador EA na subscrição. Capacidade reservada.

Para obter mais informações sobre como os clientes da empresa e os clientes Pay-As-You-Go são cobrados para compras de reservas, consulte [o uso da reserva Understand Azure para a sua inscrição na Enterprise](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea) e [compreenda o uso da reserva para a sua subscrição Pay-As-You-Go.](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage.md)

> [!NOTE]
> A aquisição de capacidade reservada não pré-aloca ou reserva recursos específicos de infraestrutura (máquinas virtuais ou clusters) para a sua utilização.

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>Determinar os tamanhos adequados do IR Azure necessários antes da compra

O tamanho da reserva deve basear-se na quantidade total de cálculo utilizada pelos fluxos de dados existentes ou em breve implantados utilizando o mesmo nível de cálculo.

Por exemplo, suponhamos que está a executar um oleoduto de hora a hora usando memória otimizada com 32 núcleos. Além disso, supõe-se que planeie implantar no próximo mês um oleoduto adicional que usa 64 núcleos para fins gerais. Além disso, suponhamos que saiba que vai precisar destes recursos por pelo menos 1 ano. Neste caso, deverá adquirir uma reserva de 32 núcleos de 1 ano para fluxos de dados otimizados de memória e uma reserva de 1 ano de 64 core core de propósito geral.

## <a name="buy-reserved-capacity"></a>Comprar capacidade reservada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** e, em seguida, no painel **de Reservas de Compra,** selecione **Fluxos de Dados ADF** para comprar uma nova reserva para fluxos de dados ADF.
4. Preencha os campos e atributos necessários que selecione qualifique para obter o desconto de capacidade reservado. O número real de fluxos de dados que obtêm o desconto depende do âmbito e quantidade selecionados.
5. Reveja o custo da reserva de capacidade na secção **Custos.**
6. Selecione **Comprar**.
7. Selecione **Ver esta Reserva** para ver o estado da sua compra.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

- [Compreender o desconto das Reservas do Azure](data-flow-understand-reservation-charges.md)
