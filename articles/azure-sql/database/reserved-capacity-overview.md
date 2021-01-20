---
title: Economize custos de cálculo com capacidade reservada
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Saiba como comprar a Azure SQL Database e a SQL Managed Instance reservada capacidade para economizar nos seus custos de computação.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 10/13/2020
ms.openlocfilehash: cacd43502a01352c24f8fcfd85b12aac781dccbd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602510"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>Economize custos para recursos com capacidade reservada - Azure SQL Database & SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

Poupe dinheiro com a Azure SQL Database e a SQL Managed Instance comprometendo-se a uma reserva de recursos computativos em comparação com os preços de pay-as-you-go. Com capacidade reservada, compromete-se a utilizar a SQL Database e/ou SQL Managed Instance por um período de um ou três anos para obter um desconto significativo nos custos de computação. Para adquirir capacidade reservada, é necessário especificar a região de Azure, tipo de implantação, nível de desempenho e termo.

Não precisa de atribuir a reserva a uma base de dados específica ou a uma instância gerida. A correspondência das implementações existentes que já estão em execução ou as que são recentemente implantadas obtém automaticamente o benefício. Ao comprar uma reserva, compromete-se a usar os custos de cálculo por um período de um ou três anos. Assim que comprar uma reserva, os encargos computacional que correspondam aos atributos da reserva já não são cobrados nas tarifas pagas. 

Uma reserva aplica-se tanto a réplicas de computação secundária primárias como faturadas, mas não abrange os custos de software, networking ou armazenamento associados ao serviço. No final do prazo de reserva, o benefício de faturação expira e a base de dados ou instância gerida é cobrada pelo preço de pagamento à sua saída. As reservas não renovam automaticamente. Para obter informações sobre preços, consulte a [oferta de capacidade reservada.](https://azure.microsoft.com/pricing/details/sql-database/managed/)

Pode comprar capacidade reservada no [portal Azure.](https://portal.azure.com) Pague pela reserva [antecipadamente ou com pagamentos mensais](../../cost-management-billing/reservations/prepare-buy-reservation.md). Para comprar capacidade de reserva:

- Você deve estar na função de proprietário para pelo menos uma Enterprise ou subscrição individual com taxas pay-as-you-go.
- Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com). Ou, se essa definição estiver desativada, deve ser um Administrador EA na subscrição. Capacidade reservada.

Para obter mais informações sobre como os clientes da empresa e os clientes Pay-As-You-Go são cobrados para compras de reservas, consulte [o uso da reserva Understand Azure para a sua inscrição na Enterprise](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) e [compreenda o uso da reserva para a sua subscrição Pay-As-You-Go.](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)

> [!NOTE]
> A aquisição de capacidade reservada não pré-aloca ou reserva recursos específicos de infraestrutura (máquinas virtuais ou nós) para a sua utilização.

## <a name="determine-correct-size-before-purchase"></a>Determinar o tamanho correto antes da compra

A dimensão da reserva deve basear-se na quantidade total de cálculo utilizada pela base de dados existente ou em breve implantada ou por exemplo gerido numa região específica e utilizando o mesmo nível de desempenho e geração de hardware.

Por exemplo, suponhamos que você está executando um propósito geral, Gen5 – piscina elástica 16 vCore e duas bases de dados críticas de negócios Gen5 – 4 vCore. Além disso, supõe-se que planeie implementar dentro do próximo mês um propósito geral adicional Gen5 – piscina elástica de 16 vCore e uma piscina elástica gen5 – 32 vCore. Além disso, suponhamos que saiba que vai precisar destes recursos por pelo menos 1 ano. Neste caso, você deve comprar uma reserva de 32 (2x16) vCores 1 ano para uma única base de dados/piscina elástica para fins gerais - Gen5 e uma reserva de 40 (2x4 + 32) vCore 1 ano para uma única base de dados/negócio de piscina elástica crítico - Gen5.

## <a name="buy-reserved-capacity"></a>Comprar capacidade reservada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** e, em seguida, no painel **de Reservas de Compra,** selecione **SQL Database** para adquirir uma nova reserva para a Base de Dados SQL.
4. Preencha os campos necessários. Bases de dados existentes na Base de Dados SQL e SQL Managed Instance que correspondem aos atributos que seleciona qualificam para obter o desconto de capacidade reservado. O número real de bases de dados ou casos geridos que obtenham o desconto depende do âmbito e quantidade selecionados.

    ![Screenshot antes de submeter a compra de capacidade reservada](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    A tabela a seguir descreve os campos necessários.
    
    | Campo      | Descrição|
    |------------|--------------|
    |Subscrição|A subscrição utilizada para pagar a reserva de capacidade. O método de pagamento da subscrição é cobrado os custos iniciais da reserva. O tipo de subscrição deve ser um acordo de empresa (número de oferta MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços pay-as-you-go (número de oferta MS-AZR-0003P ou MS-AZR-0023P). Para uma subscrição de empresa, os encargos são deduzidos do saldo do pré-pagamento Azure (anteriormente chamado de compromisso monetário) ou cobrados como excesso de idade. Para uma subscrição individual com preços pay-as-you-go, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição.|
    |Âmbito       |O âmbito da reserva vCore pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar  <br/><br/>**Partilhado**, o desconto de reserva vCore é aplicado na base de dados ou caso gerido em quaisquer subscrições dentro do seu contexto de faturação. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para clientes Pay As You Go, o âmbito partilhado é todas as subscrições Pay As You Go criadas pelo administrador de conta.<br/><br/>**Subscrição única**, o desconto de reserva vCore é aplicado nas bases de dados ou casos geridos nesta subscrição. <br/><br/>**Grupo de recursos únicos**, o desconto de reserva é aplicado às instâncias de bases de dados ou instâncias geridas na subscrição selecionada e no grupo de recursos selecionado dentro dessa subscrição.|
    |Região      |A região de Azure que está coberta pela reserva de capacidade.|
    |Tipo de implantação|O tipo de recurso SQL para o quais pretende comprar a reserva.|
    |Nível de desempenho|O nível de serviço das bases de dados ou de instâncias geridas. |
    |Termo        |Um ano ou três anos.|
    |Quantidade    |A quantidade de recursos computacional que estão a ser adquiridos dentro da reserva de capacidade. A quantidade é uma série de vCores na região de Azure selecionada e nível de desempenho que estão sendo reservados e receberá o desconto de faturação. Por exemplo, se executar ou planear executar várias bases de dados com a capacidade total de computação da Gen5 16 vCores na região leste dos EUA, então especificaria a quantidade como 16 para maximizar o benefício para todas as bases de dados. |

1. Reveja o custo da reserva de capacidade na secção **Custos.**
1. Selecione **Comprar**.
1. Selecione **Ver esta Reserva** para ver o estado da sua compra.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>flexibilidade do tamanho vCore

vCore a flexibilidade do tamanho ajuda-o a escalar para cima ou para baixo dentro de um nível de desempenho e região, sem perder o benefício de capacidade reservada. A capacidade reservada também lhe proporciona a flexibilidade para mover temporariamente as suas bases de dados quentes para dentro e para fora de piscinas elásticas (dentro da mesma região e nível de desempenho) como parte das suas operações normais sem perder o benefício de capacidade reservada. Ao manter um tampão não apetecível na sua reserva, pode gerir eficazmente os picos de desempenho sem exceder o seu orçamento.

## <a name="limitation"></a>Limitação

Não é possível reservar bases de dados baseadas em DTU (básicas, padrão ou premium) na Base de Dados SQL.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva vCore é aplicado automaticamente ao número de bases de dados ou casos geridos que correspondam ao âmbito e atributos de reserva de capacidade. Pode atualizar o âmbito da reserva de capacidade através do [portal Azure,](https://portal.azure.com)PowerShell, Azure CLI ou da API.

Para aprender a gerir a reserva de capacidade, consulte [gerir a capacidade reservada.](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

- [O que são as reservas do Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Gerir o Azure Reservations](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Compreender o desconto das Reservas do Azure](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](/partner-center/azure-reservations) (Reservas do Azure no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros)