---
title: Poupe nos custos do Azure Synapse Analytics com a capacidade reservada do Azure
description: Saiba como economizar custos para encargos do Azure Synapse Analytics com a capacidade reservada para poupar dinheiro.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: bd43b668c318b825c5c5b6f36fc1da1055863bed
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599097"
---
# <a name="save-costs-for-azure-synapse-analytics-charges-with-reserved-capacity"></a>Poupe nos custos do Azure Synapse Analytics com a capacidade reservada

Pode poupar dinheiro com o Azure Synapse Analytics ao consolidar uma reserva de utilização de cDWUs para uma duração de um ou três anos. Para comprar capacidade reservada do Azure Synapse Analytics, tem de escolher a região do Azure e o termo. Depois, adicione o SKU do Azure Synapse Analytics ao carrinho e escolha a quantidade de unidades de cDWU que quer comprar.

Quando compra uma reserva, a utilização do Azure Synapse Analytics que corresponde aos atributos da reserva deixa de ser cobrada às tarifas de pay as you go.

Uma reserva não abrange os custos de armazenamento ou rede associados à utilização do Azure Synapse Analytics.

Quando a capacidade reservada expirar, as instâncias do Azure Synapse Analytics continuam a ser executadas, mas são cobradas à tarifa de pay as you go. As reservas não se renovam automaticamente.

Para obter informações sobre preços, veja a [oferta de capacidade reservada do Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

Pode comprar capacidade reservada do Azure Synapse Analytics no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Pague pela reserva [antecipadamente ou com pagamentos mensais](./prepare-buy-reservation.md). Para comprar capacidade de reserva:

- Deve ter a função de proprietário em pelo menos uma subscrição Enterprise ou Pay As You Go.
- Para subscrições Enterprise, a opção **Adicionar Instâncias Reservadas** deve ser ativada no [EA Portal](https://ea.azure.com/). Se a definição estiver desativada, tem ser um Administrador EA.
- Para o programa Fornecedor de Soluções Cloud (CSP), apenas os agentes de administração ou os agentes de vendas podem comprar capacidade reservada do Azure Synapse Analytics.

Para obter mais informações sobre como os clientes Enterprise e os clientes Pay As You Go são cobrados por compras de reserva, veja [understand Azure reservation usage for your Enterprise enrollment](understand-reserved-instance-usage-ea.md) (compreender a utilização da reserva do Azure para a sua inscrição Enterprise) e [understand Azure reservation usage for your Pay-As-You-Go subscription](understand-reserved-instance-usage.md) (compreender a utilização da reserva do Azure para a sua inscrição Pay As You Go).

## <a name="choose-the-right-size-before-purchase"></a>Escolha o tamanho certo antes da compra

O tamanho da reserva do Azure Synapse Analytics deve basear-se no total de unidades de computação do Data Warehouse (cDWU) que consumir. As compras são feitas em incrementos de 100 cDWU.

Por exemplo, suponha que o consumo total do Azure Synapse Analytics é DW3000c. Quer comprar capacidade de reserva para tudo. Portanto, deve comprar 30 unidades de capacidade de reserva de cDWU.

## <a name="buy-azure-synapse-analytics-reserved-capacity"></a>Comprar capacidade reservada do Azure Synapse Analytics

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione uma subscrição. Use a lista Subscrição para escolher a subscrição que serve para pagar a capacidade de reserva. Os custos são cobrados de acordo com o método de pagamento indicado na capacidade de reserva. O tipo de subscrição tem de ser um contrato Enterprise (número da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Pay As You Go (números da oferta: MS-AZR-0003P ou MS-AZR-0023P).
   - Para uma subscrição Enterprise, os custos são deduzidos do saldo do Pré-pagamento do Azure (que antes se chamava alocação monetária) da inscrição ou cobrados como utilização excedida.
   - Para a subscrição Pay As You Go, os custos são debitados no cartão de crédito ou cobrados de acordo com o método de pagamento indicado na subscrição.
4. Selecione um âmbito. Use a lista Âmbito para escolher um âmbito de subscrição.
   - **Âmbito de grupo de recursos único** – aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionado.
   - **Âmbito de subscrição individual** – aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.
   - **Âmbito partilhado** – aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que estejam no contexto de faturação. Para clientes do Contrato Enterprise, o contexto de faturação é a inscrição. Para subscrições individuais com taxas pay as you go, o âmbito da faturação reside em todas as subscrições elegíveis criadas pelo administrador de conta.
   - Para clientes do Enterprise, o contexto de faturação é a inscrição EA.
   - Para clientes Pay As You Go, o âmbito partilhado é todas as subscrições Pay As You Go criadas pelo administrador de conta.
5. Selecione uma região para escolher uma região do Azure coberta pela capacidade de reserva.
6. Escolha uma quantidade. Insira a quantidade de 100 unidades de Data Warehouse (cDWU) que quer comprar.    
   Por exemplo, uma quantidade de 30 dar-lhe-ia 3.000 cDWU de capacidade de reserva por hora.
7. Verifique o custo da reserva de capacidade reservada do Azure Synapse Analytics na secção **Custos**.
8. Selecione **Comprar**.
9. Selecione **Ver esta Reserva** para ver o estado da sua compra.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](exchange-and-refund-azure-reservations.md).

É aplicado automaticamente um desconto de reserva ao número de instâncias do Azure Synapse Analytics que correspondem à região e ao âmbito da capacidade reservada do Azure Synapse Analytics. Pode atualizar o âmbito da capacidade reservada do Azure Synapse Analytics com o [portal do Azure](https://portal.azure.com/), o PowerShell, a CLI ou através da API.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre como os descontos de reservas se aplicam ao Azure Synapse Analytics, veja [Como os descontos de reserva se aplicam ao Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md).

- Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:
  - [O que são as reservas do Azure?](save-compute-costs-reservations.md)
  - [Gerir o Azure Reservations](manage-reserved-vm-instance.md)
  - [Compreender o desconto das Reservas do Azure](understand-reservation-charges.md)
  - [Compreender a utilização de reservas na sua subscrição Pay As You Go](understand-reserved-instance-usage.md)
  - [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)