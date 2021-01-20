---
title: Pré-pagamento para computação com capacidade reservada - Base de Dados Azure para MariaDB
description: Pré-pagamento da Base de Dados Azure para os recursos de computação MariaDB com capacidade reservada
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 462ba0ccbd5d7e7048c2c7fcb9c5bece04adaebe
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600895"
---
# <a name="prepay-for-azure-database-for-mariadb-compute-resources-with-reserved-capacity"></a>Pré-pagamento da Base de Dados Azure para os recursos de computação MariaDB com capacidade reservada

A Azure Database for MariaDB ajuda-o agora a economizar dinheiro, pré-pagando recursos de computação em comparação com os preços de pagamento. Com a Azure Database para a capacidade reservada da MariaDB, você compromete-se antecipadamente com os servidores MariaDB por um período de um ou três anos para obter um desconto significativo sobre os custos de computação. Para adquirir a Base de Dados Azure para a capacidade reservada mariaDB, é necessário especificar a região de Azure, tipo de implantação, nível de desempenho e termo. </br>

Não precisa de atribuir a reserva a bases de dados Azure específicas para servidores MariaDB. Uma Base de Dados Azure já em execução para a MariaDB ou as que são recentemente implantadas, terá automaticamente o benefício de preços reservados. Ao comprar uma reserva, está a pré-pagar os custos do cálculo por um período de um ou três anos. Assim que comprar uma reserva, a base de dados Azure para os encargos de computação MariaDB que correspondam aos atributos da reserva já não são cobradas nas tarifas pagas. Uma reserva não cobre software, networking ou custos de armazenamento associados ao servidor MariaDB Database. No final do período de reserva, o benefício de faturação expira, e a Base de Dados Azure para MariaDB é cobrada pelo preço de pagamento. As reservas não renovam automaticamente. Para obter informações sobre preços, consulte a [Base de Dados Azure para a oferta de capacidade reservada mariaDB.](https://azure.microsoft.com/pricing/details/mariadb/) </br>

Pode comprar Azure Database para a capacidade reservada da MariaDB no [portal Azure.](https://portal.azure.com/) Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/prepare-buy-reservation.md). Para comprar a capacidade reservada:

* Você deve estar na função de proprietário para pelo menos uma Enterprise ou subscrição individual com taxas pay-as-you-go.
* Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com/). Ou, se essa definição estiver desativada, deve ser um Administrador EA na subscrição.
* Para o programa Cloud Solution Provider (CSP), apenas os agentes administrativos ou agentes de vendas podem adquirir a Base de Dados Azure para a capacidade reservada mariaDB. </br>

Os detalhes sobre como os clientes da empresa e os clientes Pay-As-You-Go são cobrados para compras de reservas, ver [compreender o uso da reserva Azure para a sua inscrição na Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) e compreender o uso da reserva [Azure para a sua subscrição Pay-As-You-Go.](../cost-management-billing/reservations/understand-reserved-instance-usage.md)


## <a name="determine-the-right-server-size-before-purchase"></a>Determine o tamanho certo do servidor antes da compra

A dimensão da reserva deve basear-se na quantidade total de cálculo utilizada pela instância de bases de dados existente ou em breve implantada numa região específica e utilizando o mesmo nível de desempenho e geração de hardware.</br>

Por exemplo, suponhamos que está a executar um propósito geral, a Base de Dados Gen5 – 32 vCore MariaDB e duas bases de dados de memória otimizadas, Gen5 – 16 vCore MariaDB. Além disso, supõe-se que planeie implementar dentro do próximo mês um propósito geral adicional, o servidor de base de dados Gen5 – 32 vCore e uma memória otimizada, o servidor de base de dados Gen5 – 16 vCore. Suponhamos que saiba que vai precisar destes recursos por pelo menos 1 ano. Neste caso, você deve comprar uma reserva de 64 (2x32) vCores, 1 ano de reserva para uma única base de dados geral - Gen5 e um 48 (2x16 + 16) reserva 1 ano para memória única base de dados otimizada - Gen5


## <a name="buy-azure-database-for-mariadb-reserved-capacity"></a>Comprar Base de Dados Azure para capacidade reservada mariaDB

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3.  Selecione **Adicionar** e, em seguida, no painel de reservas de compra, selecione **Azure Database for MariaDB** para comprar uma nova reserva para as suas bases de dados MariaDB.
4.  Preencha os campos necessários. Bases de dados existentes ou novas que correspondam aos atributos que seleciona qualificam-se para obter o desconto de capacidade reservado. O número real da sua Base de Dados Azure para servidores MariaDB que obtêm o desconto dependem do âmbito e quantidade selecionados.


![Visão geral dos preços reservados](media/concepts-reserved-pricing/mariadb-reserved-price.png)


A tabela a seguir descreve os campos necessários.

| Campo | Descrição |
| :------------ | :------- |
| Subscrição   | A subscrição utilizada para pagar a Base de Dados Azure para reserva de capacidade reservada MariaDB. O método de pagamento da subscrição é cobrado os custos iniciais da Base de Dados Azure para reserva de capacidade reservada MariaDB. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma subscrição de empresa, os encargos são deduzidos do saldo do pré-pagamento Azure (anteriormente chamado de compromisso monetário) ou cobrados como excesso de idade. Para uma subscrição individual com preços pay-as-you-go, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição.
| Âmbito | O âmbito da reserva vCore pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar: </br></br> **Compartilhado**, o desconto de reserva vCore é aplicado na Base de Dados Azure para servidores MariaDB que executam em quaisquer subscrições dentro do seu contexto de faturação. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para clientes Pay As You Go, o âmbito partilhado é todas as subscrições Pay As You Go criadas pelo administrador de conta.</br></br> **Subscrição única**, o desconto de reserva vCore é aplicado à Base de Dados Azure para servidores MariaDB nesta subscrição. </br></br> **Grupo único de recursos**, o desconto de reserva é aplicado à Base de Dados Azure para servidores MariaDB na subscrição selecionada e no grupo de recursos selecionado dentro dessa subscrição.
| Região | A região de Azure que está coberta pela Base de Dados Azure para reserva de capacidade reservada mariaDB.
| Tipo de implantação | A Base de Dados Azure para o tipo de recurso MariaDB para o quais pretende comprar a reserva.
| Nível de desempenho | O nível de serviço para a Base de Dados Azure para servidores MariaDB.
| Termo | Um ano
| Quantidade | A quantidade de recursos computacional que estão a ser adquiridos no âmbito da Base de Dados Azure para reserva de capacidade reservada mariaDB. A quantidade é uma série de vCores na região de Azure selecionada e nível de desempenho que estão sendo reservados e receberá o desconto de faturação. Por exemplo, se estiver a executar ou a planear executar uma Base de Dados Azure para servidores MariaDB com a capacidade total de computação de Gen5 16 vCores na região leste dos EUA, então especificaria a quantidade como 16 para maximizar o benefício para todos os servidores.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>flexibilidade do tamanho vCore

vCore a flexibilidade do tamanho ajuda-o a escalar para cima ou para baixo dentro de um nível de desempenho e região, sem perder o benefício de capacidade reservada. 

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva vCore é aplicado automaticamente ao número de Base de Dados Azure para servidores MariaDB que correspondem à Base de Dados Azure para o âmbito de reserva de capacidade reservada MariaDB. Pode atualizar o âmbito da base de dados Azure para reserva de capacidade reservada mariaDB através do portal Azure, PowerShell, CLI ou através da API. </br></br>
Para aprender a gerir a Base de Dados Azure para a capacidade reservada da MariaDB, consulte a gestão da Base de Dados Azure para a capacidade reservada da MariaDB.

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

* [O que são Reservas Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Gerir o Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Compreender o desconto das Reservas do Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Compreender a utilização de reservas na sua subscrição Pay As You Go](../cost-management-billing/reservations/understand-reservation-charges-mariadb.md)
* [Compreender a utilização de reservas na inscrição Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](/partner-center/azure-reservations) (Reservas do Azure no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros)