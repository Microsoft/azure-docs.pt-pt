---
title: Efetue o pré-pagamento do cobranças de armazém de dados SQL com capacidade de reserva do Azure | Documentos da Microsoft
description: Saiba como pode efetuar o pré-pagamento para cobranças de armazém de dados SQL com capacidade de reserva para poupar dinheiro.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 3d56ee99f8056139a234477b6eed93d9fcbe2bbb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371194"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Efetue o pré-pagamento do cobranças de armazém de dados SQL com capacidade de reserva

Pode poupar dinheiro com o Azure SQL Data Warehouse ao prepaying para a sua utilização cDWU durante um período de um ou três anos. Para comprar capacidade reservados do SQL Data Warehouse, tem de escolher o Azure região e o termo. Em seguida, adicione o SKU de armazém de dados do SQL ao seu carrinho e escolha a quantidade de unidades de cDWU que deseja comprar.

Ao comprar uma reserva, o SQL Data Warehouse a utilização que corresponda os atributos de reserva já não é cobrada com o pay as you go taxas.

Uma reserva não abrange a armazenamento ou tarifas das redes associadas com a utilização do SQL Data Warehouse.

Quando a capacidade de reserva expira, instâncias do SQL Data Warehouse continuam a ser executado, mas cobradas a tarifa pay as you aceda. As reservas não o renovar automaticamente.

Para obter informações sobre preços, consulte a [armazém de dados de SQL reservadas oferta de capacidade](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

Pode comprar capacidade do Azure SQL Data Warehouse reservado [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Comprar capacidade de reserva:

- Tem de ter a função de proprietário para, pelo menos, uma empresa ou uma subscrição pay as you go.
- Para subscrições Enterprise, o **adicionar as instâncias reservadas** opção tem de estar ativada no [portal EA](https://ea.azure.com/). Se a definição estiver desativada, tem de ser um administrador do EA.
- Para o programa de fornecedor de soluções Cloud (CSP), apenas o admin de agentes ou agentes de vendas podem comprar capacidade do SQL Data Warehouse reservados.

Para obter mais informações sobre como são cobrados os clientes empresariais e os clientes de pay as you go para compras de reserva, consulte [compreender a utilização de reserva do Azure da sua inscrição de Enterprise](billing-understand-reserved-instance-usage-ea.md) e [compreender do Azure utilização de reserva para a sua subscrição pay as you go](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Escolha o tamanho correto antes da compra

O tamanho da reserva deve basear-se no total do SQL Data Warehouse de computação unidades do data warehouse (cDWU) que consumir. São adquiridas em 100 cDWU incrementos.

Por exemplo, suponha que o consumo total do SQL Data Warehouse é DW3000c. Deseja comprar capacidade de reserva para todos. Por isso, deve adquirir 30 unidades de capacidade de cDWU reservado.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Capacidade de reserva de comprar o SQL Data Warehouse

1. Início de sessão para o [portal do Azure](https://portal.azure.com/).
2. Selecione **todos os serviços** > **reservas**.
3. Selecione uma subscrição. Utilize a lista de subscrições para escolher a subscrição que é utilizada para pagar a capacidade de reserva. O método de pagamento da subscrição é cobrado os custos iniciais para a capacidade de reserva. O tipo de subscrição tem de ser um contrato enterprise (números da oferta: MS-AZR-0017P ou MS-AZR - 0148 P) ou pay as you go (números da oferta: MS-AZR-0003P ou MS-AZR-0023P).
  - Para uma subscrição Enterprise, os custos são deduzidos do saldo de fidelização monetária da inscrição ou cobrados como utilização excedida.
  - Para a subscrição Pay As You Go, os custos são debitados no cartão de crédito ou cobrados de acordo com o método de pagamento indicado na subscrição.
4. Selecione um âmbito. Utilize a lista de âmbito para escolher um âmbito de subscrição.
  - Com o **único** opção, o desconto de reserva é aplicada ao SQL Data Warehouse implementadas na subscrição selecionada.
  - Com o **partilhado** opção, o desconto de reserva é aplicada às instâncias em execução no caso de subscrições no seu contexto de faturação.
    - Para os clientes empresariais, o contexto de faturação é a inscrição de EA.
    - Para clientes pay as you go, o âmbito partilhado é todas as subscrições pay as you go a criada pelo administrador de conta.
5. Selecione uma região para escolher uma região do Azure que é abrangida pela capacidade de reserva.
6. Escolha uma quantidade. Introduza a quantidade de 100 unidades de armazém de dados (cDWU) que pretende adquirir.    
  Por exemplo, uma quantidade de 30 daria cDWU 3.000 de capacidade de reserva a cada hora.
7. Reveja o SQL Data Warehouse reservado custo de reserva de capacidade na **custos** secção.
8. Selecione **Comprar**.
9. Selecione **ver esta reserva** para ver o estado de compra.

## <a name="cancellations-and-exchanges"></a>Cancelamentos e trocas

Se precisar de capacidade de reserva cancelar o SQL Data Warehouse, poderá haver uma taxa de rescisão antecipada de 12%. Os reembolsos baseiam-se no preço mais baixo do preço de compra ou do preço atual da reserva. Reembolsos estão limitados a US $50,000.00 por ano. O reembolso que receber é o restante saldo é calculado subtraindo a taxa de rescisão antecipada de 12%. Para pedir um cancelamento, vá para a reserva no portal do Azure e selecione **reembolsar** para criar um pedido de suporte.

Se precisar de alterar a capacidade do seu SQL Data Warehouse reservados para outra região ou o termo, podem trocá-lo para outro reserva do valor igual ou superior. A data de início do período da nova reserva não transita da reserva trocada. O um ou três anos termo começa quando cria a nova reserva. Para pedir uma troca, abra a reserva no portal do Azure e selecione **Exchange** para criar um pedido de suporte.

Para obter mais informações sobre como as reservas de exchange ou o reembolso, consulte [trocas de reserva e reembolsos](billing-azure-reservations-self-service-exchange-and-refund.md).

O desconto de reserva é aplicado automaticamente para o número de instâncias do SQL Data Warehouse que correspondem à âmbito de capacidade do SQL Data Warehouse reservados e região. Pode atualizar o âmbito da capacidade do SQL Data Warehouse reservado com o [portal do Azure](https://portal.azure.com/), PowerShell, CLI ou através da API.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://portal.azure.com/).

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre como os descontos de reserva se aplicam ao Azure SQL Data Warehouse, veja [como os descontos de reserva se aplicam ao Azure SQL Data Warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Para saber mais sobre as reservas do Azure, veja os artigos seguintes:
  - [Quais são as reservas do Azure?](billing-save-compute-costs-reservations.md)
  - [Gerir o Azure Reservations](billing-manage-reserved-vm-instance.md)
  - [Compreender o que Azure reservas de desconto](billing-understand-reservation-charges.md)
  - [Compreender a utilização de reserva para a sua subscrição pay as you go](billing-understand-reserved-instance-usage.md)
  - [Compreender a utilização de reserva para inscrição da sua empresa](billing-understand-reserved-instance-usage-ea.md)
