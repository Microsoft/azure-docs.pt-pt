---
title: Preços reservados de computação - Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
description: Pré-pagamento para Base de Dados de Azure para PostgreSQL - Hiperescala (Citus) com capacidade reservada
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: dcf38a3bcd41ba41bb7cf3b16d022a7a45734c4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85218070"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>Pré-pagamento para Base de Dados de Azure para PostgreSQL - Hiperescala (Citus) com capacidade reservada

A Azure Database for PostgreSQL – Hyperscale (Citus) ajuda-o agora a economizar dinheiro, pré-pagando recursos de computação em comparação com os preços de pay-as-you-go. Com a capacidade reservada da Hyperscale (Citus), você compromete-se antecipadamente com o grupo de servidores Hyperscale (Citus) por um período de um ou três anos para obter um desconto significativo nos custos de computação. Para adquirir a capacidade reservada da Hyperscale (Citus), tem de especificar a região de Azure, o termo de reserva e a frequência de faturação.

> [!IMPORTANT]
> Esta página é sobre a capacidade reservada para a Base de Dados Azure para PostgreSQL – Hyperscale (Citus). Consulte [esta página](/azure/postgresql/concept-reserved-pricing) para obter informações sobre a capacidade reservada para a Base de Dados Azure para PostgreSQL – Servidor Único.

Não precisa de atribuir a reserva a grupos específicos de servidores Hyperscale (Citus). Um grupo de servidores hyperscale (Citus) já em execução ou aqueles que são recentemente implantados obterão automaticamente o benefício de preços reservados. Ao comprar uma reserva, está a pré-pagar os custos do cálculo por um período de um ou três anos. Assim que comprar uma reserva, os encargos de cálculo da Hyperscale (Citus) que correspondam aos atributos da reserva já não são cobrados nas taxas de pagamento à medida que vão. Uma reserva não cobre software, networking ou taxas de armazenamento associadas aos grupos de servidores Hyperscale (Citus). No final do período de reserva, o benefício de faturação expira e os grupos de servidores Hyperscale (Citus) são cobrados pelo preço de pagamento à medida que vão. As reservas não são novas. Para obter informações sobre preços, consulte a [Base de Dados Azure para a oferta de capacidade reservada postgreSQL – Hyperscale (Citus).](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)

Pode comprar capacidade reservada para a Hyperscale (Citus) no [portal Azure.](https://portal.azure.com/) Pague pela reserva [antecipadamente ou com pagamentos mensais](https://docs.microsoft.com/azure/cost-management-billing/reservations/monthly-payments-reservations). Para comprar a capacidade reservada:

* Você deve estar na função de proprietário para pelo menos uma Enterprise ou subscrição individual com taxas pay-as-you-go.
* Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com/). Ou, se essa definição estiver desativada, deve ser um Administrador EA na subscrição.
* Para o programa Cloud Solution Provider (CSP), apenas os agentes administrativos ou agentes de vendas podem adquirir capacidade reservada hyperscale (Citus).

Para mais detalhes sobre como os clientes da empresa e os clientes Pay-As-You-Go são cobrados para compras de reservas, consulte [a compreensão do uso da reserva Azure para a sua inscrição na Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) e [compreenda o uso da reserva Azure para a sua subscrição Pay-As-You-Go.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

## <a name="determine-the-right-server-group-size-before-purchase"></a>Determine o tamanho certo do grupo do servidor antes da compra

O tamanho da reserva deve basear-se na quantidade total de cálculo utilizada pelo coordenador existente ou em breve implantado e nos nós de trabalhadores em grupos de servidores Hyperscale (Citus) dentro de uma região específica.

Por exemplo, suponhamos que está a executar um grupo de servidores Hyperscale (Citus) com 16 coordenadores vCore e três nós de trabalhadores 8 vCore. Além disso, vamos supor que planeia implementar dentro do próximo mês um grupo adicional de servidores Hyperscale (Citus) com um coordenador 32 vCore e dois nós de trabalhadores 4 vCore. Suponhamos que vai precisar destes recursos por pelo menos um ano.

Neste caso, você deve comprar uma reserva de um ano para

* Total de 16 vCores + 32 vCores= 48 vCores para os nosdes coordenadores
* Total de três nóns x 8 vCores + 2 nosdes x 4 vCores = 24 + 8 = 32 vCores para os nóns dos trabalhadores

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Comprar Base de Dados Azure para capacidade reservada pósgresqL

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** e, em seguida, no painel de reservas de compra, selecione **Azure Database for PostgreSQL** para comprar uma nova reserva para as suas bases de dados PostgreSQL.
4. Selecione Hyperscale (Citus) Tipo de cálculo para comprar e clique no botão **Select.**
5. Reveja a quantidade para o tipo de cálculo selecionado no **separador Produtos.**
4. Dirija-se ao **separador Comprar + Revisão** para concluir a sua compra.

A tabela a seguir descreve os campos necessários.

| Campo        | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscrição | A subscrição utilizada para pagar a Base de Dados Azure para reserva de capacidade reservada postgreSQL. O método de pagamento da subscrição é cobrado os custos iniciais da Base de Dados Azure para reserva de capacidade reservada pós-SQL. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma subscrição de empresa, os encargos são deduzidos do saldo de compromisso monetário da inscrição ou cobrados como excesso. Para uma subscrição individual com preços pay-as-you-go, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição.                                                                                  |
| Âmbito        | O âmbito da reserva vCore pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar: <br><br> **Partilhado,** o desconto de reserva vCore é aplicado aos grupos de servidores Hyperscale (Citus) que executam em quaisquer subscrições dentro do seu contexto de faturação. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição.  Para clientes Pay As You Go, o âmbito partilhado é todas as subscrições Pay As You Go criadas pelo administrador de conta. <br><br> **Subscrição única,** o desconto de reserva vCore é aplicado aos grupos de servidores Hyperscale (Citus) nesta subscrição. <br><br> **O grupo de recursos únicos,** o desconto de reserva é aplicado aos grupos de servidores Hyperscale (Citus) na subscrição selecionada e no grupo de recursos selecionado dentro dessa subscrição. |
| Região       | A região de Azure que é coberta pela Base de Dados Azure para a Reserva de Capacidade Pós-Escala (Citus).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Termo         | Um ano ou três anos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Quantidade     | A quantidade de recursos computacional que estão a ser adquiridos dentro da reserva de capacidade reservada da Hyperscale (Citus). Em particular, o número de coordenadores ou de nó de trabalhador vCores na região de Azure selecionada que estão a ser reservados e que receberão o desconto de faturação. Por exemplo, se estiver a executar (ou a planear executar) Grupos de servidores de Hiperescala (Citus) com a capacidade total de computação de 64 vCores e 32 vCores de nós de trabalhadores na região leste dos EUA, então especificaria a quantidade como 64 e 32 para coordenadores e nós de trabalhadores, respectivamente, para maximizar o benefício para todos os servidores.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>flexibilidade do tamanho vCore

vA flexibilidade de tamanho vCore ajuda a escalar coordenador para cima ou para baixo e os nós dos trabalhadores dentro de uma região, sem perder o benefício de capacidade reservada.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva vCore é aplicado automaticamente ao número de grupos de servidores Hyperscale (Citus) que correspondem à Base de Dados Azure para o âmbito de reserva de capacidade reservada postgreSQL. Pode atualizar o âmbito da base de dados Azure para a reserva de capacidade reservada postgreSQL – Hyperscale (Citus) através do portal Azure, PowerShell, CLI ou através da API.

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

* [O que são as reservas do Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Gerir o Azure Reservations](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Compreender o desconto das Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Compreender a utilização de reservas na sua subscrição Pay As You Go](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [Compreender a utilização de reservas na inscrição Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure Reservations no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros](https://docs.microsoft.com/partner-center/azure-reservations)
