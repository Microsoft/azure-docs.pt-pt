---
title: Economize custos para encargos SQL Data Warehouse com a capacidade reservada do Azure
description: Saiba como economizar custos para SQL Data Warehouse encargos com capacidade reservada para economizar dinheiro.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 381a709c74f5fcf6bb1f89f07ad84d5e3af0c5e0
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806271"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Economize custos para encargos SQL Data Warehouse com capacidade reservada

Você pode economizar dinheiro com o Azure SQL Data Warehouse confirmando uma reserva para seu uso de cDWU por uma duração de um ou três anos. Para comprar SQL Data Warehouse capacidade reservada, você precisa escolher a região do Azure e o termo. Depois, adicione o SKU do SQL Data Warehouse ao carrinho e escolha a quantidade de unidades de cDWU que quer comprar.

Quando compra uma reserva, a utilização do SQL Data Warehouse que corresponde aos atributos da reserva deixa de ser cobrada às tarifas de pay as you go.

A reserva não cobre os custos de armazenamento ou rede associados à utilização do SQL Data Warehouse.

Quando a capacidade reservada expirar, as instâncias do SQL Data Warehouse continuam a ser executadas, mas são cobradas à tarifa de pay as you go. As reservas não se renovam automaticamente.

Para obter informações sobre preços, consulte a [SQL data warehouse oferta de capacidade reservada](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

Você pode comprar o Azure SQL Data Warehouse capacidade reservada no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Pague pela reserva [antecipada ou com pagamentos mensais](billing-monthly-payments-reservations.md). Para comprar a capacidade reservada:

- Você deve ter a função de proprietário para pelo menos uma assinatura corporativa ou paga conforme o uso.
- Para assinaturas Enterprise, a opção **adicionar instâncias reservadas** deve ser habilitada no [portal de ea](https://ea.azure.com/). Se a configuração estiver desabilitada, você deverá ser um administrador do EA.
- Para o programa CSP (provedor de soluções na nuvem), somente os agentes de administração ou os agentes de vendas podem comprar SQL Data Warehouse capacidade reservada.

Para obter mais informações sobre como clientes corporativos e clientes pagos conforme o uso são cobrados por compras de reserva, consulte [entender o uso de reserva do Azure para seu registro corporativo](billing-understand-reserved-instance-usage-ea.md) e [entender o uso de reserva do Azure para seu Assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Escolha o tamanho certo antes da compra

O tamanho da reserva de SQL Data Warehouse deve ser baseado no total de cDWU (unidades de data warehouse de computação) que você consumir. As compras são feitas em incrementos de 100 cDWU.

Por exemplo, suponha que o consumo total de SQL Data Warehouse seja DW3000c. Você deseja comprar a capacidade reservada para todas elas. Portanto, você deve comprar 30 unidades de capacidade reservada cDWU.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Comprar SQL Data Warehouse capacidade reservada

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **todas as** > **reservas**de serviços.
3. Selecione uma subscrição. Use a lista assinatura para escolher a assinatura que é usada para pagar pela capacidade reservada. O método de pagamento da assinatura é cobrado pelos custos da capacidade reservada. O tipo de assinatura deve ser um Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou pago conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P).
   - Para uma subscrição Enterprise, os custos são deduzidos do saldo de fidelização monetária da inscrição ou cobrados como utilização excedida.
   - Para a subscrição Pay As You Go, os custos são debitados no cartão de crédito ou cobrados de acordo com o método de pagamento indicado na subscrição.
4. Selecione um escopo. Use a lista escopo para escolher um escopo de assinatura.
   - **Escopo do grupo de recursos único** — aplica o desconto de reserva aos recursos correspondentes somente no grupo de recursos selecionado.
   - **Escopo de assinatura única** — aplica o desconto de reserva aos recursos de correspondência na assinatura selecionada.
   - **Escopo compartilhado** — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes Enterprise Agreement, o contexto de cobrança é o registro. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo de cobrança é todas as assinaturas qualificadas criadas pelo administrador da conta.
   - Para clientes corporativos, o contexto de cobrança é o registro de EA.
   - Para clientes pagos conforme o uso, o escopo compartilhado é todas as assinaturas pagas conforme o uso criadas pelo administrador da conta.
5. Selecione uma região para escolher uma região do Azure coberta pela capacidade reservada.
6. Escolha uma quantidade. Insira a quantidade de 100 cDWU (unidades de data warehouse) que você deseja comprar.    
   Por exemplo, uma quantidade de 30 forneceria 3.000 cDWU de capacidade reservada a cada hora.
7. Examine o custo de reserva de capacidade reservada SQL Data Warehouse na seção de **custos** .
8. Selecione **Comprar**.
9. Selecione **exibir esta reserva** para ver seu status de compra.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Você pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, consulte [trocas e reembolsos de autoatendimento para reservas do Azure](billing-azure-reservations-self-service-exchange-and-refund.md).

Um desconto de reserva é aplicado automaticamente ao número de instâncias de SQL Data Warehouse que correspondem à região e ao escopo da capacidade reservada SQL Data Warehouse. Você pode atualizar o escopo do SQL Data Warehouse capacidade reservada com o [portal do Azure](https://portal.azure.com/), o PowerShell, a CLI ou por meio da API.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://portal.azure.com/).

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre como os descontos de reserva se aplicam ao SQL Data Warehouse do Azure, confira [como os descontos de reserva se aplicam ao azure SQL data warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Para saber mais sobre as reservas do Azure, consulte os seguintes artigos:
  - [O que são as reservas do Azure?](billing-save-compute-costs-reservations.md)
  - [Gerir o Azure Reservations](billing-manage-reserved-vm-instance.md)
  - [Entender o desconto de reservas do Azure](billing-understand-reservation-charges.md)
  - [Compreender a utilização de reservas na sua subscrição Pay As You Go](billing-understand-reserved-instance-usage.md)
  - [Compreender a utilização de reservas na inscrição Enterprise](billing-understand-reserved-instance-usage-ea.md)
