---
title: Preços de cálculo reservados - Base de Dados Azure para PostgreSQL - Servidor Único
description: Pré-pagamento para Base de Dados Azure para recursos computacionais PostgreSQL com capacidade reservada
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/02/2020
ms.openlocfilehash: 7f671e2a77a0a00fd1cc4338e29c14f7b8fca4f2
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734727"
---
# <a name="prepay-for-azure-database-for-postgresql-compute-resources-with-reserved-capacity"></a>Pré-pagamento para Base de Dados Azure para recursos computacionais PostgreSQL com capacidade reservada

A Base de Dados Azure para PostgreSQL ajuda-o agora a economizar dinheiro pagando os recursos da computação em comparação com os preços de pagamento. Com a Base de Dados Azure para a capacidade reservada postgreSQL, você assume um compromisso inicial no servidor PostgreSQL por um período de um ou três anos para obter um desconto significativo nos custos da computação. Para adquirir a Base de Dados Azure para a capacidade reservada postgreSQL, é necessário especificar a região azure, o tipo de implantação, o nível de desempenho e o termo. </br>

Não necessita de atribuir a reserva a uma base de dados específica do Azure para servidores PostgreSQL. Uma base de dados Azure já em execução para postgreSQL ou aquelas que são recentemente implantadas, receberá automaticamente o benefício de preços reservados. Ao comprar uma reserva, está a pré-pagar os custos do cálculo por um período de um ou três anos. Assim que comprar uma reserva, a base de dados Azure para taxas de computação PostgreSQL que correspondam aos atributos de reserva já não são cobradas pelas tarifas de pagamento. Uma reserva não cobre software, networking ou custos de armazenamento associados aos servidores de base de dados PostgreSQL. No final do prazo de reserva, o benefício de faturação expira, e a Base de Dados Azure para PostgreSQL é faturada ao preço de pagamento. As reservas não renovam automaticamente. Para obter informações sobre preços, consulte a Base de [Dados Azure para a oferta de capacidade reservada postgresQL](https://azure.microsoft.com/pricing/details/postgresql/). </br>

> [!IMPORTANT]
> Os preços de capacidade reservados só estão disponíveis para a Base de Dados Azure para a implementação [do servidor Single](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server) PostgreSQL e não para a implementação do [Hyperscale Citus.](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---hyperscale-citus)

Pode comprar base de dados Azure para capacidade reservada postgresQL no [portal Azure](https://portal.azure.com/). Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/monthly-payments-reservations.md). Para comprar a capacidade reservada:

* Você deve estar no papel de proprietário para pelo menos uma Empresa ou subscrição individual com taxas pay-as-you-go.
* Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com/). Ou, se essa definição for desativada, deve ser um Administrador EA na subscrição.
* Para o programa Cloud Solution Provider (CSP), apenas os agentes de administração ou agentes de vendas podem adquirir a Base de Dados Azure para a capacidade reservada postgreSQL. </br>

Os detalhes sobre como os clientes empresariais e os clientes Pay-As-You-Go são cobrados para compras de reservas, consulte o uso da [reserva Azure para](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) a sua inscrição na Enterprise e compreenda o uso da reserva Azure para a sua [subscrição Pay-As-You-Go](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## <a name="determine-the-right-server-size-before-purchase"></a>Determine o tamanho certo do servidor antes da compra

O tamanho da reserva deve basear-se na quantidade total de computação utilizada pelos servidores existentes ou em breve implantados numa região específica e utilizando o mesmo nível de desempenho e geração de hardware.</br>

Por exemplo, suponhamos que está a executar uma base de dados gen5 – 32 vCore PostgreSQL e duas bases de dados de memória otimizadas, Gen5 – 16 vCore PostgreSQL. Além disso, supõe-se que planeia implementar dentro do próximo mês um propósito geral adicional, o servidor de base de dados Gen5 – 32 vCore, e uma memória otimizada, gen5 – 16 vCore servidor de base de dados. Suponhamos que saiba que vai precisar destes recursos por pelo menos 1 ano. Neste caso, deve adquirir uma reserva de 64 (2x32) vCores, 1 ano de reserva para fins gerais de base de dados únicas - Gen5 e um 48 (2x16 + 16) vCore 1 ano reserva para memória única de base de dados otimizada - Gen5


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Comprar Base de Dados Azure para capacidade reservada postgresQL

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** e, em seguida, no painel de reservas de compra, selecione Base de **Dados Azure para PostgreSQL** para comprar uma nova reserva para as suas bases de dados PostgreSQL.
4. Preencha os campos necessários. Bases de dados existentes ou novas que correspondam aos atributos que seleciona qualificam-se para obter o desconto de capacidade reservado. O número real da sua Base de Dados Azure para servidores PostgreSQL que obtêm o desconto depende do âmbito e quantidade selecionados.


![Visão geral dos preços reservados](media/concepts-reserved-pricing/postgresql-reserved-price.png)


A tabela seguinte descreve os campos necessários.

| Campo | Descrição |
| :------------ | :------- |
| Subscrição   | A subscrição utilizada para pagar a Base de Dados Azure para reserva de capacidade reservada postgreSQL. O método de pagamento da subscrição é cobrado os custos iniciais da Base de Dados Azure para reserva de capacidade reservada postgreSQL. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma subscrição Enterprise, os custos são deduzidos do saldo de fidelização monetária da inscrição ou cobrados como utilização excedida. Para uma subscrição individual com preços de pagamento, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição.
| Âmbito | O âmbito da reserva vCore pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar: </br></br> **Partilhado,** o desconto de reserva vCore é aplicado na Base de Dados Azure para servidores PostgreSQL em qualquer subscrição dentro do seu contexto de faturação. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para clientes Pay As You Go, o âmbito partilhado é todas as subscrições Pay As You Go criadas pelo administrador de conta.</br></br> **Subscrição única,** o desconto de reserva vCore é aplicado na Base de Dados Azure para servidores PostgreSQL nesta subscrição. </br></br> **Grupo de recursos únicos**, o desconto de reserva é aplicado à Base de Dados Azure para servidores PostgreSQL na subscrição selecionada e no grupo de recursos selecionados dentro dessa subscrição.
| Região | A região azure que está coberta pela Base de Dados Azure para reserva de capacidade reservada postgresQL.
| Tipo de implantação | A Base de Dados Azure para o tipo de recurso PostgreSQL para o que pretende comprar a reserva.
| Nível de Desempenho | O nível de serviço da Base de Dados Azure para servidores PostgreSQL.
| Termo | Um ano
| Quantidade | A quantidade de recursos computacionais que estão a ser adquiridos na Base de Dados Azure para reserva de capacidade reservada postgreSQL. A quantidade é uma série de vCores na região azure selecionada e nível de desempenho que estão sendo reservados e receberão o desconto de faturação. Por exemplo, se estiver a executar ou a planear executar uma Base de Dados Azure para servidores PostgreSQL com a capacidade total de computação de Gen5 16 vCores na região leste dos EUA, então especificaria a quantidade como 16 para maximizar o benefício para todos os servidores.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>flexibilidade do tamanho vCore

a flexibilidade do tamanho vCore ajuda-o a escalar para cima ou para baixo dentro de um nível de desempenho e região, sem perder o benefício de capacidade reservado. 

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva vCore é aplicado automaticamente ao número de Bases de Dados Azure para servidores PostgreSQL que correspondem à Base de Dados Azure para o âmbito e atributos de reserva de capacidade reservados postgreSQL. Pode atualizar o âmbito da base de dados Azure para reserva de capacidade reservada postgreSQL através do portal Azure, PowerShell, CLI ou através da API. </br></br>
Para saber gerir a Base de Dados Azure para a capacidade reservada postgresQL, consulte a gestão da Base de Dados Azure para a capacidade reservada postgreSQL.

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

* [O que são Reservas Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Gerir o Azure Reservations](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Compreender o desconto das Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Compreender a utilização de reservas na sua subscrição Pay As You Go](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [Compreender a utilização de reservas na inscrição Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure Reservations no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros](https://docs.microsoft.com/partner-center/azure-reservations)
