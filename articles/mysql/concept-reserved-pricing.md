---
title: Pré-pagamento para computação com capacidade reservada - Azure Database for MySQL
description: Pré-pagamento da Base de Dados Azure para os recursos de computação MySQL com capacidade reservada
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 86933b324d7216a6097102cf237d0402d9f63234
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90882671"
---
# <a name="prepay-for-azure-database-for-mysql-compute-resources-with-reserved-capacity"></a>Pré-pagamento da Base de Dados Azure para os recursos de computação MySQL com capacidade reservada

A Azure Database for MySQL ajuda-o agora a economizar dinheiro, pré-pagando recursos de computação em comparação com os preços de pagamento. Com a Azure Database para a capacidade reservada do MySQL, você compromete-se antecipadamente no servidor MySQL por um período de um ou três anos para obter um desconto significativo sobre os custos de computação. Para adquirir a Base de Dados Azure para a capacidade reservada do MySQL, é necessário especificar a região Azure, tipo de implementação, nível de desempenho e termo. </br>

Não precisa de atribuir a reserva a bases de dados Azure específicas para servidores MySQL. Uma Base de Dados Azure já em execução para o MySQL ou as que são recentemente implantadas, obterá automaticamente o benefício de preços reservados. Ao comprar uma reserva, está a pré-pagar os custos do cálculo por um período de um ou três anos. Assim que comprar uma reserva, a base de dados Azure para os encargos computativos MySQL que correspondam aos atributos da reserva já não são cobradas nas tarifas pagas. Uma reserva não cobre software, networking ou custos de armazenamento associados ao servidor MySQL Database. No final do período de reserva, o benefício de faturação expira, e a Base de Dados Azure para o MySQL é faturada pelo preço de pagamento à sua saída. As reservas não renovam automaticamente. Para obter informações sobre preços, consulte a [Base de Dados Azure para a oferta de capacidade reservada do MySQL.](https://azure.microsoft.com/pricing/details/mysql/) </br>

Pode comprar Azure Database para a capacidade reservada do MySQL no [portal Azure.](https://portal.azure.com/) Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/monthly-payments-reservations.md). Para comprar a capacidade reservada:

* Você deve estar na função de proprietário para pelo menos uma Enterprise ou subscrição individual com taxas pay-as-you-go.
* Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com/). Ou, se essa definição estiver desativada, deve ser um Administrador EA na subscrição.
* Para o programa Cloud Solution Provider (CSP), apenas os agentes administrativos ou agentes de vendas podem adquirir a Base de Dados Azure para a capacidade reservada do MySQL. </br>

Os detalhes sobre como os clientes da empresa e os clientes Pay-As-You-Go são cobrados para compras de reservas, ver [compreender o uso da reserva Azure para a sua inscrição na Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) e compreender o uso da reserva [Azure para a sua subscrição Pay-As-You-Go.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)


## <a name="determine-the-right-database-size-before-purchase"></a>Determinar o tamanho certo da base de dados antes da compra

O tamanho da reserva deve basear-se na quantidade total de cálculo utilizada pelo servidor existente ou em breve implantado numa região específica e utilizando o mesmo nível de desempenho e geração de hardware.</br>

Por exemplo, suponhamos que está a executar um propósito geral, a base de dados Gen5 – 32 vCore MySQL e duas bases de dados de memória otimizadas, Gen5 – 16 vCore MySQL. Além disso, supõe-se que planeie implementar dentro do próximo mês um propósito geral adicional, o servidor de base de dados Gen5 – 32 vCore e uma memória otimizada, o servidor de base de dados Gen5 – 16 vCore. Suponhamos que saiba que vai precisar destes recursos por pelo menos 1 ano. Neste caso, você deve comprar uma reserva de 64 (2x32) vCores, 1 ano de reserva para uma única base de dados geral - Gen5 e um 48 (2x16 + 16) reserva 1 ano para memória única base de dados otimizada - Gen5


## <a name="buy-azure-database-for-mysql-reserved-capacity"></a>Comprar Base de Dados Azure para capacidade reservada do MySQL

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** e, em seguida, no painel de reservas de compra, selecione **Azure Database for MySQL** para comprar uma nova reserva para as suas bases de dados MySQL.
4. Preencha os campos necessários. Bases de dados existentes ou novas que correspondam aos atributos que seleciona qualificam-se para obter o desconto de capacidade reservado. O número real da sua Base de Dados Azure para servidores MySQL que obtêm o desconto depende do âmbito e quantidade selecionados.


:::image type="content" source="media/concepts-reserved-pricing/mysql-reserved-price.png" alt-text="Visão geral dos preços reservados":::


A tabela a seguir descreve os campos necessários.

| Campo | Descrição |
| :------------ | :------- |
| Subscrição   | A subscrição utilizada para pagar a Base de Dados Azure para a reserva de capacidade reservada do MySQL. O método de pagamento da subscrição é cobrado os custos iniciais da Base de Dados Azure para a reserva de capacidade reservada do MySQL. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma subscrição Enterprise, os custos são deduzidos do saldo de fidelização monetária da inscrição ou cobrados como utilização excedida. Para uma subscrição individual com preços pay-as-you-go, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição.
| Âmbito | O âmbito da reserva vCore pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar: </br></br> **Compartilhado**, o desconto de reserva vCore é aplicado à Base de Dados Azure para servidores MySQL que executam em quaisquer subscrições dentro do seu contexto de faturação. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para clientes Pay As You Go, o âmbito partilhado é todas as subscrições Pay As You Go criadas pelo administrador de conta.</br></br> **Subscrição única**, o desconto de reserva vCore é aplicado à Base de Dados Azure para servidores MySQL nesta subscrição. </br></br> **Grupo de recursos únicos**, o desconto de reserva é aplicado à Base de Dados Azure para servidores MySQL na subscrição selecionada e no grupo de recursos selecionado dentro dessa subscrição.
| Region | A região de Azure que está coberta pela Base de Dados Azure para a reserva de capacidade reservada do MySQL.
| Tipo de implantação | A Base de Dados Azure para o tipo de recurso MySQL para o quais pretende comprar a reserva.
| Nível de desempenho | O nível de serviço para a Base de Dados Azure para servidores MySQL.
| Termo | Um ano
| Quantidade | A quantidade de recursos computativos a ser adquiridos na Base de Dados Azure para a reserva de capacidade reservada do MySQL. A quantidade é uma série de vCores na região de Azure selecionada e nível de desempenho que estão sendo reservados e receberá o desconto de faturação. Por exemplo, se estiver a executar ou a planear executar uma Base de Dados Azure para servidores MySQL com a capacidade total de computação de Gen5 16 vCores na região leste dos EUA, então especificaria a quantidade como 16 para maximizar o benefício para todos os servidores.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>flexibilidade do tamanho vCore

vCore a flexibilidade do tamanho ajuda-o a escalar para cima ou para baixo dentro de um nível de desempenho e região, sem perder o benefício de capacidade reservada. 

## <a name="need-help--contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva vCore é aplicado automaticamente ao número de Bases de Dados Azure para servidores MySQL que correspondem à Base de Dados Azure para o âmbito de reserva de capacidade reservada do MySQL. Pode atualizar o âmbito da base de dados Azure para a reserva de capacidade reservada do MySQL através do portal Azure, PowerShell, CLI ou através da API. </br></br>
Para aprender a gerir a Base de Dados Azure para a capacidade reservada do MySQL, consulte a gestão da Base de Dados Azure para a capacidade reservada do MySQL.

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

* [O que são Reservas Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Gerir o Azure Reservations](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Compreender o desconto das Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Compreender a utilização de reservas na sua subscrição Pay As You Go](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
* [Compreender a utilização de reservas na inscrição Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure Reservations no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros](https://docs.microsoft.com/partner-center/azure-reservations)

