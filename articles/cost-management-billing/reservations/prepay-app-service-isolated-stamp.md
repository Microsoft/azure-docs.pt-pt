---
title: Economizar no Serviço de Aplicações do Azure com a capacidade reservada
description: Saiba como pode economizar custos para o Imposto de Selo do Plano Isolado do Serviço de Aplicações com a capacidade de reserva.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: feb7be6ad5dbd0dfbd04b05836842d0476780d40
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151545"
---
# <a name="save-costs-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>Economize custos para o Imposto de Selo do Plano Isolado do Serviço de Aplicações com a capacidade de reserva

Pode poupar dinheiro no Imposto de Selo do Plano Isolado do Serviço de Aplicações ao consolidar uma reserva para a sua utilização de selos durante três anos. Para comprar capacidade de reserva do Imposto de Selo do Plano Isolado, tem de escolher a região do Azure onde o carimbo será implementado e o número de selos a comprar.

Quando compra uma reserva, a utilização do Imposto de Selo do Plano Isolado que corresponde aos atributos da reserva deixa de ser cobrada às tarifas de pay as you go. A reserva é aplicada automaticamente ao número de selos de Plano Isolado que correspondem à região e ao âmbito da capacidade de reserva. Não precisa de atribuir uma reserva a um selo de plano isolado. A reserva não se aplica às funções de trabalho, pelo que todos os outros recursos associados ao selo serão cobrados separadamente.

Quando a capacidade reservada expirar, os Selos de Plano Isolado continuam a ser executados, mas são cobrados à tarifa de pay as you go. As reservas não se renovam automaticamente.

## <a name="determine-the-right-reservation-to-purchase"></a>Determinar a reserva correta a comprar

Ao comprar uma reserva, está a comprometer-se com a utilização de quantidades reservadas nos próximos três anos. Verifique os seus dados de utilização para determinar quantos Selos do Plano Isolado do Serviço de Aplicações está a usar consistentemente e pode usar no futuro.

Além disso, confirme que compreender como o Selo de Plano Isolado emite o medidor do Linux ou do Windows.

- Por predefinição, um Selo de Plano Isolado vazio emite o medidor de selos do Windows. Por exemplo, sem funções de trabalho implementadas. Continua a emitir este medidor se as funções de trabalho do Windows forem implementadas no selo.
- O medidor muda para o medidor de selos do Linux se implementar uma função de trabalho do Linux.
- Nos casos em que se implementem funções de trabalho do Linux e do Windows, o selo emite o medidor do Windows.

Portanto, o medidor de selos pode mudar entre o Windows e o Linux durante a vida útil do selo.

Compre reservas de selos do Windows se tiver um ou mais funções de trabalho do Windows no selo. A única vez que deve comprar uma reserva de selo do Linux é se planear ter _apenas_ funções de trabalho do Linux no selo.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Comprar capacidade de reserva de Selos de Plano Isolado

Pode comprar capacidade de reserva de Plano Isolado no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Pague pela reserva [antecipadamente ou com pagamentos mensais](./prepare-buy-reservation.md). Para comprar a capacidade de reserva, deve ter a função de proprietário em pelo menos uma subscrição Enterprise ou uma subscrição individual com tarifas pay as you go.

- Para subscrições Enterprise, a opção **Adicionar Instâncias Reservadas** deve ser ativada no [EA Portal](https://ea.azure.com/). Em alternativa, se a definição estiver desativada, tem ser um Administrador EA.
- Para o programa Fornecedor de Soluções Cloud (CSP), apenas os agentes de administração ou os agentes de vendas podem comprar capacidade reservada do Azure Synapse Analytics.

**Para Comprar:**

1. Aceda ao [Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Selecione uma subscrição. Use a lista **Subscrição** para escolher a subscrição que serve para pagar a capacidade de reserva. Os custos são cobrados de acordo com o método de pagamento indicado na capacidade de reserva. O tipo de subscrição tem de ser um contrato Enterprise (número da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Pay As You Go (números da oferta: MS-AZR-0003P ou MS-AZR-0023P) ou uma subscrição CSP.
    - Para uma subscrição Enterprise, os custos são deduzidos do saldo de fidelização monetária da inscrição ou cobrados como utilização excedida.
    - Para a subscrição Pay As You Go, os custos são debitados no cartão de crédito ou cobrados de acordo com o método de pagamento indicado na subscrição.
1. Selecione um **Âmbito** para escolher um âmbito de subscrição.
    - **Âmbito de grupo de recursos único** – aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionado.
    - **Âmbito de subscrição individual** – aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.
    - **Âmbito partilhado** – aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que estejam no contexto de faturação. Para clientes do Contrato Enterprise, o contexto de faturação é a inscrição. Para subscrições individuais com taxas pay as you go, o âmbito da faturação reside em todas as subscrições elegíveis criadas pelo administrador de conta.
1. Selecione uma **Região** para escolher uma região do Azure coberta pela capacidade de reserva e adicione a reserva ao carrinho.
1. Selecione um tipo de Plano Isolado e clique em **Selecionar**.  
    ![Exemplo ](./media/prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. Insira a quantidade de selos de Plano Isolado do Serviço de Aplicações a reservar. Por exemplo, uma quantidade de três dar-lhe-ia três selos reservados por região. Clique em **Seguinte: Rever + Comprar**.
1. Reveja e clique em **Comprar agora**.

Após a compra, aceda a [Reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) para ver o estado da compra e monitorize-as em qualquer altura.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](exchange-and-refund-azure-reservations.md).

## <a name="discount-application-shown-in-usage-data"></a>Aplicação de desconto mostrada nos dados de utilização

Os dados de utilização têm um preço efetivo de zero pela utilização que obtém um desconto de reserva. Os dados de utilização mostram o desconto de reserva para cada instância de selo em cada reserva.

Para obter mais informações sobre como o desconto de reserva é mostrado nos dados de utilização, veja [Get Enterprise Agreement reservation costs and usage](understand-reserved-instance-usage-ea.md) (Compreender custos de reserva e utilização do Contrato Enterprise) se for um cliente de Contrato Enterprise (EA). Caso contrário, veja [Compreender a utilização de reservas do Azure para a sua subscrição individual com tarifas pay as you go](understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:
  - [O que são as reservas do Azure?](save-compute-costs-reservations.md)
  - [Compreender a forma como é aplicado um desconto de reserva de Selo de Plano Isolado do Serviço de Aplicações do Azure](reservation-discount-app-service-isolated-stamp.md)
  - [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)