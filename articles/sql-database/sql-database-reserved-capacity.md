---
title: Poupar custos
description: Saiba como comprar a Base de Dados Azure SQL reservada para economizar nos custos da sua computação.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: 0713e1ed98cc87b95cad1f84672148cd062e0b58
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75979980"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Poupe custos para recursos de computação de base de dados SQL com capacidade reservada à Base de Dados Azure SQL

Poupe dinheiro com a Base de Dados Azure SQL comprometendo-se a uma reserva de recursos computacionais em comparação com os preços de pagamento. Com a capacidade reservada à Base de Dados Azure SQL, compromete-se a utilizar a Base de Dados SQL por um período de um ou três anos para obter um desconto significativo nos custos da computação. Para adquirir a capacidade reservada à Base de Dados SQL, é necessário especificar a região azure, o tipo de implantação, o nível de desempenho e o termo.


Não necessita de atribuir a reserva a instâncias específicas da Base de Dados SQL (bases de dados individuais, piscinas elásticas ou instâncias geridas). As instâncias de Base de Dados SQL correspondentes, que já estão em execução ou as que estão recentemente implantadas, receberão automaticamente o benefício. Ao adquirir uma reserva, compromete-se a utilizar os custos da computação por um período de um ou três anos. Assim que comprar uma reserva, as taxas de computação da Base de Dados SQL que correspondem aos atributos de reserva já não são cobradas às taxas de pagamento. Uma reserva não cobre software, networking ou custos de armazenamento associados à instância de Base de Dados SQL. No final do prazo de reserva, o benefício de faturação expira e as Bases de Dados SQL são faturadas ao preço de pagamento. As reservas não renovam automaticamente. Para obter informações sobre preços, consulte a oferta de capacidade reservada à [Base de Dados SQL.](https://azure.microsoft.com/pricing/details/sql-database/managed/)

Pode comprar a capacidade reservada à Azure SQL Database no [portal Azure](https://portal.azure.com). Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/monthly-payments-reservations.md). Para comprar a sql base de dados capacidade reservada:

- Você deve estar no papel de proprietário para pelo menos uma Empresa ou subscrição individual com taxas pay-as-you-go.
- Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com). Ou, se essa definição for desativada, deve ser um Administrador EA na subscrição.
- Para o programa Cloud Solution Provider (CSP), apenas os agentes de administração ou agentes de vendas podem adquirir a capacidade reservada à Base de Dados SQL.

Os detalhes sobre como os clientes empresariais e os clientes Pay-As-You-Go são cobrados para compras de reservas, consulte o uso da [reserva Azure para](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) a sua inscrição na Enterprise e compreenda o uso da reserva Azure para a sua [subscrição Pay-As-You-Go](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Determine o tamanho SQL certo antes da compra

O tamanho da reserva deve basear-se na quantidade total de cálculo utilizado pelas bases de dados únicas existentes ou em breve implantadas, piscinas elásticas ou instâncias geridas numa região específica e utilizando o mesmo nível de desempenho e geração de hardware.

Por exemplo, suponhamos que você está executando um propósito geral, Gen5 – 16 vCore eelástico e dois negócios críticos, Gen5 – 4 vCore bases de dados únicas. Além disso, supõe-se que planeia implementar dentro do próximo mês um propósito geral adicional, Gen5 – 16 vCore e elástico, e um negócio crítico, Gen5 – 32 vCore elástico. Além disso, suponhamos que saiba que vai precisar destes recursos por pelo menos 1 ano. Neste caso, deve adquirir uma reserva de 32 (2x16) vCores, 1 ano de reserva para uma única base de dados/elástico - Gen5 e uma reserva de 40 (2x4 + 32) vCore 1 ano para uma única base de dados/negócio de piscina elástica crítica - Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Comprar capacidade de reserva da Base de Dados SQL do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione todas as reservas **de serviços** > **Reservations**.
3. Selecione **Adicionar** e, em seguida, no painel de reservas de compra, selecione Base de **Dados SQL** para comprar uma nova reserva para base de dados SQL.
4. Preencha os campos necessários. Existindo ou novas bases de dados individuais, piscinas elásticas ou instâncias geridas que correspondam aos atributos que seleciona se qualificar para obter o desconto de capacidade reservado. O número real das instâncias da sua Base de Dados SQL que obtêm o desconto depende do âmbito e quantidade selecionados.
    ![Screenshot antes de submeter a Base de Dados SQL reservada compra de capacidade](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

A tabela seguinte descreve os campos necessários.

| Campo      | Descrição|
|------------|--------------|
|Subscrição|A subscrição utilizada para pagar a Reserva de Capacidade reservada à Base de Dados SQL. O método de pagamento da subscrição é cobrado os custos iniciais da reserva de capacidade reservada à Base de Dados SQL. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma subscrição Enterprise, os custos são deduzidos do saldo de fidelização monetária da inscrição ou cobrados como utilização excedida. Para uma subscrição individual com preços de pagamento, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição.|
|Âmbito       |O âmbito da reserva vCore pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar: <br/><br/>**Partilhado,** o desconto de reserva vCore é aplicado às instâncias da Base de Dados SQL que executam quaisquer subscrições dentro do seu contexto de faturação. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para clientes Pay As You Go, o âmbito partilhado é todas as subscrições Pay As You Go criadas pelo administrador de conta.<br/><br/>**Subscrição única,** o desconto de reserva vCore é aplicado às instâncias da Base de Dados SQL nesta subscrição. <br/><br/>**Grupo de recursos únicos**, o desconto de reserva é aplicado às instâncias da Base de Dados SQL na subscrição selecionada e no grupo de recursos selecionados dentro dessa subscrição.|
|Região      |A região azure que está coberta pela Base de Dados SQL reservou reserva de capacidade.|
|Tipo de implantação|O tipo de recurso SQL para o que pretende comprar a reserva.|
|Nível de Desempenho|O nível de serviço para as instâncias de Base de Dados SQL.
|Termo        |Um ano ou três anos.|
|Quantidade    |A quantidade de recursos computacionais que estão a ser adquiridos dentro da Base de Dados SQL reservada reserva de capacidade. A quantidade é uma série de vCores na região azure selecionada e nível de desempenho que estão sendo reservados e receberão o desconto de faturação. Por exemplo, se estiver a executar ou a planear executar casos de Base de Dados SQL com a capacidade total computacional de Gen5 16 vCores na região leste dos EUA, então especificaria a quantidade como 16 para maximizar o benefício para todos os casos. |

1. Reveja o custo da Base de Dados SQL reservada reserva de capacidade na secção **Custos.**
1. Selecione **Comprar**.
1. Selecione **Ver esta Reserva** para ver o estado da sua compra.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para mais informações, consulte [trocas de self-service e reembolsos para Reservas Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>flexibilidade do tamanho vCore

a flexibilidade do tamanho vCore ajuda-o a escalar para cima ou para baixo dentro de um nível de desempenho e região, sem perder o benefício de capacidade reservado. A capacidade reservada à Base de Dados SQL também lhe proporciona a flexibilidade para mover temporariamente as suas bases de dados quentes entre piscinas e bases de dados individuais como parte das suas operações normais (dentro da mesma região e nível de desempenho) sem perder o benefício de capacidade reservado. Ao manter um tampão não aplicado na sua reserva, pode gerir eficazmente os picos de desempenho sem exceder o seu orçamento.

## <a name="limitation"></a>Limitação

Não é possível reservar bases de dados SQL baseadas em DTU (básicas, standard ou premium).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva vCore é aplicado automaticamente ao número de instâncias de Base de Dados SQL que correspondem ao âmbito e atributos reservados à Reserva de Capacidade sQL. Pode atualizar o âmbito da Reserva de Capacidade reservada da Base de Dados SQL através do [portal Azure,](https://portal.azure.com)PowerShell, CLI ou através da API.

Para saber como gerir a reserva de capacidade reservada à Base de Dados SQL, consulte a capacidade reservada à Base de [Dados SQL](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

- [O que são Reservas Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Gerir o Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Compreender o desconto das Reservas do Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure Reservations no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros](https://docs.microsoft.com/partner-center/azure-reservations)
