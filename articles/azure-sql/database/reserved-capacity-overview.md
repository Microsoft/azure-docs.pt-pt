---
title: Poupar custos com cálculo com capacidade reservada
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Saiba como comprar a Base de Dados Azure SQL e a SQL Managed Instance reservada à capacidade de poupar nos custos da sua computação.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: c02368b5713c0743cdca764275f76dda0e0926d2
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84119100"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>Custos de poupança para recursos com capacidade reservada - Base de Dados Azure SQL & Instância Gerida SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

Poupe dinheiro com a Base de Dados Azure SQL e a SQL Managed Instance comprometendo-se a uma reserva de recursos computacionais em comparação com os preços de pagamento. Com capacidade reservada, compromete-se com a Base de Dados SQL e/ou a SQL Managed Instance por um período de um ou três anos para obter um desconto significativo nos custos da computação. Para adquirir a capacidade reservada, é necessário especificar a região azure, o tipo de implantação, o nível de desempenho e o termo.

Não necessita de atribuir a reserva a uma base de dados SQL específica ou a Instância Gerida SQL. A correspondência das implementações existentes que já estão em execução ou as que são recentemente implantadas recebem automaticamente o benefício. Ao comprar uma reserva, compromete-se a utilizar os custos da computação por um período de um ou três anos. Assim que comprar uma reserva, os encargos computação que correspondem aos atributos de reserva já não são cobrados às taxas de pagamento. Uma reserva não cobre software, networking ou custos de armazenamento associados ao serviço. No final do prazo de reserva, o benefício de faturação expira e a Base de Dados SQL ou SQL Managed Instance são faturadas ao preço de pagamento. As reservas não renovam automaticamente. Para obter informações sobre preços, consulte a [oferta de capacidade reservada.](https://azure.microsoft.com/pricing/details/sql-database/managed/)

Pode comprar capacidade reservada no [portal Azure.](https://portal.azure.com) Pague pela reserva [antecipadamente ou com pagamentos mensais](../../cost-management-billing/reservations/prepare-buy-reservation.md). Para comprar capacidade de reserva:

- Você deve estar no papel de proprietário para pelo menos uma Empresa ou subscrição individual com taxas pay-as-you-go.
- Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com). Ou, se essa definição for desativada, deve ser um Administrador EA na subscrição. capacidade reservada.

Os detalhes sobre como os clientes empresariais e os clientes Pay-As-You-Go são cobrados para compras de reservas, consulte o uso da [reserva Azure para](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) a sua inscrição na Enterprise e compreenda o uso da reserva Azure para a sua [subscrição Pay-As-You-Go](../../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="determine-correct-size-before-purchase"></a>Determine o tamanho correto antes da compra

O tamanho da reserva deve basear-se na quantidade total de cálculo utilizado pela base de dados SQL existente ou em breve implantada ou por Instância Gerida SQL numa região específica e utilizando o mesmo nível de desempenho e geração de hardware.

Por exemplo, suponhamos que você está executando um propósito geral, Gen5 – 16 vCore eelástico e dois negócios críticos, Gen5 – 4 vCore bases de dados únicas. Além disso, supõe-se que planeia implementar dentro do próximo mês um propósito geral adicional, Gen5 – 16 vCore e elástico, e um negócio crítico, Gen5 – 32 vCore elástico. Além disso, suponhamos que saiba que vai precisar destes recursos por pelo menos 1 ano. Neste caso, deve adquirir uma reserva de 32 (2x16) vCores, 1 ano de reserva para uma única base de dados/elástico - Gen5 e uma reserva de 40 (2x4 + 32) vCore 1 ano para uma única base de dados/negócio de piscina elástica crítica - Gen5.

## <a name="buy-reserved-capacity"></a>Comprar capacidade reservada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** e, em seguida, no painel **reservas de compra,** selecione Base de **Dados SQL** para comprar uma nova reserva para base de dados SQL.
4. Preencha os campos necessários. Bases de dados SQL existentes (instâncias únicas ou reunidas) ou SQL Geridas que correspondem aos atributos que seleciona se qualificam para obter o desconto de capacidade reservado. O número real das suas Bases de Dados SQL ou Instâncias Geridas SQL que obtêm o desconto depende do âmbito e quantidade selecionados.
    ![Screenshot antes de submeter a compra de capacidade reservada](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

A tabela seguinte descreve os campos necessários.

| Campo      | Descrição|
|------------|--------------|
|Subscrição|A subscrição usada para pagar a reserva de capacidade. O método de pagamento da subscrição é cobrado os custos iniciais da reserva. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma subscrição Enterprise, os custos são deduzidos do saldo de fidelização monetária da inscrição ou cobrados como utilização excedida. Para uma subscrição individual com preços de pagamento, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição.|
|Âmbito       |O âmbito da reserva vCore pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar: <br/><br/>**Partilhado,** o desconto de reserva vCore é aplicado à Base de Dados SQL ou à Instância Gerida SQL que executa em quaisquer subscrições dentro do seu contexto de faturação. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para clientes Pay As You Go, o âmbito partilhado é todas as subscrições Pay As You Go criadas pelo administrador de conta.<br/><br/>**Subscrição única,** o desconto de reserva vCore é aplicado nas Bases de Dados SQL ou Instâncias Geridas SQL nesta subscrição. <br/><br/>**Grupo de recursos únicos,** o desconto de reserva é aplicado às Bases de Dados SQL ou Instâncias Geridas SQL na subscrição selecionada e no grupo de recursos selecionados dentro dessa subscrição.|
|Região      |A região de Azure que está coberta pela reserva de capacidade.|
|Tipo de implantação|O tipo de recurso SQL para o que pretende comprar a reserva.|
|Nível de Desempenho|O nível de serviço para as Bases de Dados SQL ou Instâncias Geridas SQL. |
|Termo        |Um ano ou três anos.|
|Quantidade    |A quantidade de recursos computacionais que estão a ser adquiridos dentro da reserva de capacidade. A quantidade é uma série de vCores na região azure selecionada e nível de desempenho que estão sendo reservados e receberão o desconto de faturação. Por exemplo, se estiver a executar ou a planear executar várias bases de dados SQL com a capacidade total computacional de Gen5 16 vCores na região leste dos EUA, então especificaria a quantidade como 16 para maximizar o benefício para todas as bases de dados. |

1. Reveja o custo da reserva de capacidade na secção **Custos.**
1. Selecione **Comprar**.
1. Selecione **Ver esta Reserva** para ver o estado da sua compra.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>flexibilidade do tamanho vCore

a flexibilidade do tamanho vCore ajuda-o a escalar para cima ou para baixo dentro de um nível de desempenho e região, sem perder o benefício de capacidade reservado. A capacidade reservada também lhe proporciona a flexibilidade para mover temporariamente as suas bases de dados SQL quentes para dentro e para fora de piscinas elásticas (dentro da mesma região e nível de desempenho) como parte das suas operações normais sem perder o benefício de capacidade reservado. Ao manter um tampão não aplicado na sua reserva, pode gerir eficazmente os picos de desempenho sem exceder o seu orçamento.

## <a name="limitation"></a>Limitação

Não é possível reservar bases de dados baseadas em DTU (básicas, standard ou premium) na Base de Dados SQL.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva vCore é aplicado automaticamente ao número de Bases de Dados SQL ou Instâncias Geridas SQL que correspondem ao âmbito e atributos da reserva de capacidade. Pode atualizar o âmbito da reserva de capacidade através do [portal Azure,](https://portal.azure.com)PowerShell, CLI ou através da API.

Para aprender a gerir a reserva de capacidade, consulte [gerir a capacidade reservada.](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

- [O que são as reservas do Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Gerir o Azure Reservations](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Compreender o desconto das Reservas do Azure](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure Reservations no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros](https://docs.microsoft.com/partner-center/azure-reservations)
