---
title: Preços reservados de computação - Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
description: Pré-pagamento para Azure Database for PostgreSQL - Hyperscale (Citus) computa recursos com capacidade reservada.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 35d5d196eccb222bf17e0a129fe4e4041b1f6053
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600737"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>Pré-pagamento para Base de Dados de Azure para PostgreSQL - Hiperescala (Citus) com capacidade reservada

A Azure Database for PostgreSQL – Hyperscale (Citus) ajuda-o agora a economizar dinheiro, pré-pagando recursos de computação em comparação com os preços de pay-as-you-go. Com a capacidade reservada da Hyperscale (Citus), você compromete-se antecipadamente com o grupo de servidores Hyperscale (Citus) por um período de um ou três anos para obter um desconto significativo nos custos de computação. Para adquirir a capacidade reservada da Hyperscale (Citus), tem de especificar a região de Azure, o termo de reserva e a frequência de faturação.

> [!IMPORTANT]
> Este artigo é sobre capacidade reservada para Azure Database for PostgreSQL – Hyperscale (Citus). Para obter informações sobre a capacidade reservada para a Base de Dados Azure para PostgreSQL – Servidor Único, consulte [Prepay for Azure Database for PostgreSQL – Single Server compute resources with reserved capacit](./concept-reserved-pricing.md).

Não precisa de atribuir a reserva a grupos específicos de servidores Hyperscale (Citus). Um grupo de servidores de Hiperescala (Citus) já em execução ou aqueles que são recentemente implantados automaticamente obtêm automaticamente o benefício de preços reservados. Ao comprar uma reserva, está a pagar os custos do cálculo por um ano ou três anos. Assim que comprar uma reserva, os encargos de cálculo da Hyperscale (Citus) que correspondam aos atributos da reserva já não são cobrados nas tarifas pagas. 

Uma reserva não cobre software, networking ou taxas de armazenamento associadas aos grupos de servidores Hyperscale (Citus). No final do período de reserva, o benefício de faturação expira e os grupos de servidores Hyperscale (Citus) são cobrados pelo preço de pagamento à medida que vão. As reservas não são novas. Para obter informações sobre preços, consulte a [Base de Dados Azure para a oferta de capacidade reservada postgreSQL – Hyperscale (Citus).](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)

Pode comprar capacidade reservada para a Hyperscale (Citus) no [portal Azure.](https://portal.azure.com/) Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/prepare-buy-reservation.md). Para comprar a capacidade reservada:

* Você deve estar na função de proprietário para pelo menos um Acordo de Empresa (EA) ou subscrição individual com taxas pay-as-you-go.
* Para as subscrições do Contrato de Empresa, **adicionar instâncias reservadas** deve ser ativado no [Portal da EA](https://ea.azure.com/). Ou, se essa definição for desativada, deve ser um administrador do Acordo de Empresa na subscrição.
* Para o programa Cloud Solution Provider (CSP), apenas os agentes administrativos ou agentes de vendas podem adquirir a capacidade reservada hyperscale (Citus).

Para obter informações sobre como os clientes do Enterprise Agreement e os clientes pay-as-you-go são cobrados para compras de reservas, consulte:
- [Compreenda o uso da reserva Azure para a sua inscrição no Enterprise Agreement](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Compreenda o uso da reserva Azure para a sua subscrição pay-as-you-go](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-server-group-size-before-purchase"></a>Determine o tamanho certo do grupo do servidor antes da compra

O tamanho da reserva baseia-se na quantidade total de cálculo utilizada pelo coordenador existente ou em breve implantado e nos nós de trabalhadores em grupos de servidores Hyperscale (Citus) dentro de uma região específica.

Por exemplo, suponhamos que esteja a executar um grupo de servidores Hyperscale (Citus) com 16 coordenadores vCore e três nós de trabalhadores 8 vCore. Além disso, vamos supor que planeia implementar dentro do próximo mês um grupo adicional de servidores Hyperscale (Citus) com um coordenador 32 vCore e dois nós de trabalhadores 4 vCore. Vamos também supor que precisa destes recursos por pelo menos um ano.

Neste caso, compre uma reserva de um ano para:

* Total de 16 vCores + 32 vCores = 48 vCores para os nosdes coordenadores
* Total de 3 nóns x 8 vCores + 2 nodes x 4 vCores = 24 + 8 = 32 vCores para os nosdes dos trabalhadores

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Comprar Base de Dados Azure para capacidade reservada pósgresqL

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione **Todos os serviços** > **Reservas**.
1. Selecione **Adicionar**. No painel **de reservas de compras,** selecione **Azure Database for PostgreSQL** para adquirir uma nova reserva para as suas bases de dados PostgreSQL.
1. Selecione o tipo **de Cálculo Hyperscale (Citus)** para comprar e clique em **Select**.
1. Reveja a quantidade para o tipo de cálculo selecionado no separador **Produtos.**
1. Continue no separador **Comprar + Revisão** para terminar a sua compra.

A tabela a seguir descreve os campos necessários.

| Campo        | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscrição | A subscrição utilizada para pagar a Base de Dados Azure para reserva de capacidade reservada postgreSQL. O método de pagamento da subscrição é cobrado os custos iniciais da Base de Dados Azure para reserva de capacidade reservada pós-SQL. O tipo de subscrição deve ser um Acordo de Empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma subscrição do Contrato de Empresa, os encargos são deduzidos do saldo do pré-pagamento Azure (anteriormente chamado de compromisso monetário) ou cobrados como excesso de idade. Para uma subscrição individual com preços pay-as-you-go, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição.                                                                                  |
| Âmbito        | O âmbito da reserva vCore pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar **Shared,** o desconto de reserva vCore é aplicado aos grupos de servidores Hyperscale (Citus) que executam em quaisquer subscrições dentro do seu contexto de faturação. Para os clientes do Enterprise Agreement, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para os clientes pay-as-you-go, o âmbito partilhado são todas as subscrições pay-as-you-go criadas pelo administrador da conta. Se selecionar **uma subscrição única,** o desconto de reserva vCore é aplicado aos grupos de servidores Hyperscale (Citus) nesta subscrição. Se selecionar **um grupo de recursos único,** o desconto de reserva é aplicado aos grupos de servidores Hyperscale (Citus) na subscrição selecionada e no grupo de recursos selecionado dentro dessa subscrição. |
| Região       | A região de Azure que é coberta pela Base de Dados Azure para a Reserva de Capacidade Pós-Escala (Citus).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Termo         | Um ano ou três anos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Quantidade     | A quantidade de recursos computacional que estão a ser adquiridos dentro da reserva de capacidade reservada da Hyperscale (Citus). Em particular, o número de coordenadores ou de nó de trabalhador vCores na região de Azure selecionada que estão a ser reservados e que receberão o desconto de faturação. Por exemplo, se estiver a executar (ou planeia executar) Grupos de servidores de hiperescala (Citus) com a capacidade total de computação de 64 vCores e 32 vCores de nó de trabalhadores na região leste dos EUA, especifique a quantidade como 64 e 32 para os nós coordenador e operário, respectivamente, para maximizar o benefício para todos os servidores.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para mais informações, consulte [trocas de self-service e reembolsos para reservas Azure.](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)

## <a name="vcore-size-flexibility"></a>flexibilidade do tamanho vCore

vA flexibilidade de tamanho vCore ajuda a escalar coordenador para cima ou para baixo e os nós dos trabalhadores dentro de uma região, sem perder o benefício de capacidade reservada.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva vCore é aplicado automaticamente ao número de grupos de servidores Hyperscale (Citus) que correspondem à Base de Dados Azure para o âmbito de reserva de capacidade reservada postgreSQL. Pode atualizar o âmbito da Base de Dados Azure para a Reserva de Capacidade Pós-Escala (Citus) através do portal Azure, PowerShell, Azure CLI ou da API.

Para saber mais sobre reservas do Azure, veja os seguintes artigos:

* [O que são reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Gerir reservas do Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Compreenda o desconto de reserva Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Compreenda o uso da reserva para a sua subscrição pay-as-you-go](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [Compreenda o uso da reserva para a sua inscrição no Enterprise Agreement](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Reservas Azure no programa Partner Center Cloud Solution Provider](/partner-center/azure-reservations)