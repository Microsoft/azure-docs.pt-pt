---
title: Preços de computação reservados - Base de Dados Azure para PostgreSQL - Servidor Único
description: Pré-pagamento da Base de Dados de Azure para recursos de computação postgreSQL com capacidade reservada
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 9b8dafa4a69358b3f6f09551ac426b908750e2f4
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735477"
---
# <a name="prepay-for-azure-database-for-postgresql---single-server-compute-resources-with-reserved-capacity"></a>Pré-pagamento para Azure Database para PostgreSQL - Recursos de computação de servidor único com capacidade reservada

A Azure Database for PostgreSQL ajuda-o agora a economizar dinheiro, pré-pagando recursos de computação em comparação com os preços de pagamento. Com a Azure Database para a capacidade reservada postgreSQL, você assume um compromisso inicial no servidor PostgreSQL por um período de um ou três anos para obter um desconto significativo sobre os custos de computação. Para adquirir a Base de Dados Azure para a capacidade reservada pós-SQL, é necessário especificar a região Azure, tipo de implantação, nível de desempenho e termo. </br>

Não precisa de atribuir a reserva a uma base de dados específica do Azure para servidores PostgreSQL. Uma Base de Dados Azure já em execução para postgreSQL (ou que são recentemente implantadas) terá automaticamente o benefício de preços reservados. Ao comprar uma reserva, está a pré-pagar os custos do cálculo por um período de um ou três anos. Assim que comprar uma reserva, a base de dados Azure para os encargos computativos PostgreSQL que correspondam aos atributos da reserva já não são cobradas nas tarifas pagas. Uma reserva não cobre software, networking ou custos de armazenamento associados aos servidores postgreSQL Database. No final do período de reserva, o benefício de faturação expira, e a Base de Dados Azure para PostgreSQL é faturada pelo preço de pagamento à sua saída. As reservas não renovam automaticamente. Para obter informações sobre preços, consulte a [Base de Dados Azure para a oferta de capacidade reservada pós-SQL.](https://azure.microsoft.com/pricing/details/postgresql/) </br>

> [!IMPORTANT]
> Os preços de capacidade reservados estão disponíveis para a Base de Dados Azure para PostgreSQL, tanto nas opções de implantação [de Citus single](./overview.md#azure-database-for-postgresql---single-server) como [hyperscale Citus.](./overview.md#azure-database-for-postgresql--hyperscale-citus) Para obter informações sobre os preços de RI em Hyperscale (Citus), consulte [esta página.](concepts-hyperscale-reserved-pricing.md)

Pode comprar Azure Database para capacidade reservada pósgreSQL no [portal Azure.](https://portal.azure.com/) Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/prepare-buy-reservation.md). Para comprar a capacidade reservada:

* Você deve estar na função de proprietário para pelo menos uma Enterprise ou subscrição individual com taxas pay-as-you-go.
* Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com/). Ou, se essa definição estiver desativada, deve ser um Administrador EA na subscrição.
* Para o programa Cloud Solution Provider (CSP), apenas os agentes administrativos ou agentes de vendas podem adquirir a Base de Dados Azure para a capacidade reservada pós-SQL. </br>

Os detalhes sobre como os clientes da empresa e os clientes Pay-As-You-Go são cobrados para compras de reservas, ver [compreender o uso da reserva Azure para a sua inscrição na Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) e compreender o uso da reserva [Azure para a sua subscrição Pay-As-You-Go.](../cost-management-billing/reservations/understand-reserved-instance-usage.md)


## <a name="determine-the-right-server-size-before-purchase"></a>Determine o tamanho certo do servidor antes da compra

O tamanho da reserva deve basear-se na quantidade total de cálculo utilizada pelos servidores existentes ou em breve implantados numa região específica e utilizando o mesmo nível de desempenho e geração de hardware.</br>

Por exemplo, suponhamos que está a executar uma base de dados Gen5 – 32 vCore PostgreSQL e duas bases de dados Gen5 – 16 vCore PostgreSQL. Além disso, supõe-se que planeie implementar dentro do próximo mês um servidor de base de dados gen5 – 8 vCore e um servidor de base de dados Gen5 – 32 vCore otimizado para memória. Suponhamos que saiba que vai precisar destes recursos pelo menos por um ano. Neste caso, você deve comprar um vCore de 40 (32 + 8), reserva de um ano para uma única base de dados geral - Gen5 e um 64 (2x16 + 32) reserva de um ano para memória única base de dados otimizada - Gen5


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Comprar Base de Dados Azure para capacidade reservada pósgresqL

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** e, em seguida, no painel de reservas de compra, selecione **Azure Database for PostgreSQL** para comprar uma nova reserva para as suas bases de dados PostgreSQL.
4. Preencha os campos necessários. Bases de dados existentes ou novas que correspondam aos atributos que seleciona qualificam-se para obter o desconto de capacidade reservado. O número real da sua Base de Dados Azure para servidores PostgreSQL que obtêm o desconto depende do âmbito e quantidade selecionados.


:::image type="content" source="media/concepts-reserved-pricing/postgresql-reserved-price.png" alt-text="Visão geral dos preços reservados":::


A tabela a seguir descreve os campos necessários.

| Campo | Descrição |
| :------------ | :------- |
| Subscrição   | A subscrição utilizada para pagar a Base de Dados Azure para reserva de capacidade reservada postgreSQL. O método de pagamento da subscrição é cobrado os custos iniciais da Base de Dados Azure para reserva de capacidade reservada pós-SQL. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma subscrição Enterprise, os custos são deduzidos do saldo do Pré-pagamento do Azure (que antes se chamava alocação monetária) da inscrição ou cobrados como utilização excedida. Para uma subscrição individual com preços pay-as-you-go, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição.
| Âmbito | O âmbito da reserva vCore pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar: </br></br> **Compartilhado**, o desconto de reserva vCore é aplicado na Base de Dados Azure para servidores PostgreSQL em execução em quaisquer subscrições dentro do seu contexto de faturação. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para clientes Pay As You Go, o âmbito partilhado é todas as subscrições Pay As You Go criadas pelo administrador de conta.</br></br> **Subscrição única**, o desconto de reserva vCore é aplicado à Base de Dados Azure para servidores PostgreSQL nesta subscrição. </br></br> **Grupo de recursos únicos**, o desconto de reserva é aplicado à Base de Dados Azure para servidores PostgreSQL na subscrição selecionada e no grupo de recursos selecionado dentro dessa subscrição.
| Region | A região de Azure que está coberta pela Base de Dados Azure para reserva de capacidade reservada pós-SQL.
| Tipo de implantação | A Base de Dados Azure para o tipo de recurso PostgreSQL para o quais pretende comprar a reserva.
| Nível de desempenho | O nível de serviço para a Base de Dados Azure para servidores PostgreSQL.
| Termo | Um ano
| Quantidade | A quantidade de recursos computativos a ser adquiridos na Base de Dados Azure para reserva de capacidade reservada pós-SQL. A quantidade é uma série de vCores na região de Azure selecionada e nível de desempenho que estão sendo reservados e receberá o desconto de faturação. Por exemplo, se estiver a executar ou a planear executar uma Base de Dados Azure para servidores PostgreSQL com a capacidade total de computação de Gen5 16 vCores na região leste dos EUA, então especificaria a quantidade como 16 para maximizar o benefício para todos os servidores.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>flexibilidade do tamanho vCore

vCore a flexibilidade do tamanho ajuda-o a escalar para cima ou para baixo dentro de um nível de desempenho e região, sem perder o benefício de capacidade reservada. Se escalar para vCores mais altos do que a sua capacidade reservada, você será cobrado para o excesso vCores usando preços pay-as-you-go.


## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva vCore é aplicado automaticamente ao número de Base de Dados Azure para servidores PostgreSQL que correspondem à Base de Dados Azure para o âmbito de reserva de capacidade reservada postgreSQL. Pode atualizar o âmbito da base de dados Azure para reserva de capacidade reservada pós-SQL através do portal Azure, PowerShell, CLI ou através da API.

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

* [O que são Reservas Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Gerir o Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Compreender o desconto das Reservas do Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Compreender a utilização de reservas na sua subscrição Pay As You Go](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [Compreender a utilização de reservas na inscrição Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](/partner-center/azure-reservations) (Reservas do Azure no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros)