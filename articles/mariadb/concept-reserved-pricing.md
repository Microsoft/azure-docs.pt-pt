---
title: Pré-pagamento para computação com capacidade reservada - Base de Dados Azure para MariaDB
description: Pré-pagamento para Base de Dados Azure para recursos computacionais MariaDB com capacidade reservada
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9a6bd3cab41c69075f5170a8a3aad4f059d970e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80159052"
---
# <a name="prepay-for-azure-database-for-mariadb-compute-resources-with-reserved-capacity"></a>Pré-pagamento para Base de Dados Azure para recursos computacionais MariaDB com capacidade reservada

A Base de Dados Azure para o MariaDB ajuda-o agora a economizar dinheiro pagando os recursos da computação em comparação com os preços de pagamento. Com a Base de Dados Azure para a capacidade reservada da MariaDB, você assume um compromisso inicial nos servidores MariaDB por um período de um ou três anos para obter um desconto significativo nos custos da computação. Para adquirir a Base de Dados Azure para a capacidade reservada ao MariaDB, é necessário especificar a região azure, o tipo de implantação, o nível de desempenho e o termo. </br>

Não necessita de atribuir a reserva a uma base de dados específica do Azure para servidores MariaDB. Uma base de dados Azure já em execução para MariaDB ou aquelas que são recentemente implantadas, receberá automaticamente o benefício de preços reservados. Ao comprar uma reserva, está a pré-pagar os custos do cálculo por um período de um ou três anos. Assim que comprar uma reserva, a base de dados Azure para taxas de computação MariaDB que correspondam aos atributos de reserva já não são cobradas às taxas de pagamento. Uma reserva não cobre software, networking ou custos de armazenamento associados ao servidor de base de dados MariaDB. No final do prazo de reserva, o benefício de faturação expira, e a Base de Dados Azure para MariaDB é faturada ao preço de pagamento. As reservas não renovam automaticamente. Para obter informações sobre preços, consulte a Base de Dados Azure para a oferta de capacidade reservada à [MariaDB.](https://azure.microsoft.com/pricing/details/mariadb/) </br>

Pode comprar base de dados Azure para a capacidade reservada mariaDB no [portal Azure.](https://portal.azure.com/) Para comprar a capacidade reservada:

* Você deve estar no papel de proprietário para pelo menos uma Empresa ou subscrição individual com taxas pay-as-you-go.
* Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com/). Ou, se essa definição for desativada, deve ser um Administrador EA na subscrição.
* Para o programa Cloud Solution Provider (CSP), apenas os agentes de administração ou agentes de vendas podem adquirir a Base de Dados Azure para a capacidade reservada da MariaDB. </br>

Os detalhes sobre como os clientes empresariais e os clientes Pay-As-You-Go são cobrados para compras de reservas, consulte o uso da [reserva Azure para](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) a sua inscrição na Enterprise e compreenda o uso da reserva Azure para a sua [subscrição Pay-As-You-Go](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## <a name="determine-the-right-server-size-before-purchase"></a>Determine o tamanho certo do servidor antes da compra

O tamanho da reserva deve basear-se na quantidade total de cálculo utilizado pela instância de bases de dados existente ou em breve implantada numa região específica e utilizando o mesmo nível de desempenho e geração de hardware.</br>

Por exemplo, suponhamos que está a executar uma base de dados gen5 – 32 vCore MariaDB e duas bases de dados Gen5 – 16 vCore MariaDB. Além disso, supõe-se que planeia implementar dentro do próximo mês um propósito geral adicional, o servidor de base de dados Gen5 – 32 vCore, e uma memória otimizada, gen5 – 16 vCore servidor de base de dados. Suponhamos que saiba que vai precisar destes recursos por pelo menos 1 ano. Neste caso, deve adquirir uma reserva de 64 (2x32) vCores, 1 ano de reserva para fins gerais de base de dados únicas - Gen5 e um 48 (2x16 + 16) vCore 1 ano reserva para memória única de base de dados otimizada - Gen5


## <a name="buy-azure-database-for-mariadb-reserved-capacity"></a>Comprar Base de Dados Azure para a capacidade reservada mariaDB

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione todas as reservas **de serviços** > **Reservations**.
3.  Selecione **Adicionar** e, em seguida, no painel de reservas de compra, selecione Base de **Dados Azure para MariaDB** para comprar uma nova reserva para as suas bases de dados MariaDB.
4.  Preencha os campos necessários. Bases de dados existentes ou novas que correspondam aos atributos que seleciona qualificam-se para obter o desconto de capacidade reservado. O número real da sua Base de Dados Azure para servidores MariaDB que obtêm o desconto depende do âmbito e quantidade selecionados.


![Visão geral dos preços reservados](media/concepts-reserved-pricing/mariadb-reserved-price.png)


A tabela seguinte descreve os campos necessários.

| Campo | Descrição |
| :------------ | :------- |
| Subscrição   | A subscrição utilizada para pagar a Base de Dados Azure para a reserva de capacidade reservada à MariaDB. O método de pagamento da subscrição é cobrado os custos iniciais da Base de Dados Azure para a reserva de capacidade reservada mariaDB. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma subscrição Enterprise, os custos são deduzidos do saldo de fidelização monetária da inscrição ou cobrados como utilização excedida. Para uma subscrição individual com preços de pagamento, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição.
| Âmbito | O âmbito da reserva vCore pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar: </br></br> **Partilhado,** o desconto de reserva vCore é aplicado na Base de Dados Azure para servidores MariaDB que executam em quaisquer subscrições dentro do seu contexto de faturação. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para clientes Pay As You Go, o âmbito partilhado é todas as subscrições Pay As You Go criadas pelo administrador de conta.</br></br> **Subscrição única,** o desconto de reserva vCore é aplicado na Base de Dados Azure para servidores MariaDB nesta subscrição. </br></br> **Grupo de recursos únicos**, o desconto de reserva é aplicado à Base de Dados Azure para servidores MariaDB na subscrição selecionada e no grupo de recursos selecionados dentro dessa subscrição.
| Região | A região azure que está coberta pela Base de Dados Azure para a reserva de capacidade reservada ao MariaDB.
| Tipo de implantação | A Base de Dados Azure para o tipo de recursos MariaDB que pretende comprar a reserva.
| Nível de Desempenho | O nível de serviço da Base de Dados Azure para servidores MariaDB.
| Termo | Um ano
| Quantidade | A quantidade de recursos computacionais que estão a ser adquiridos na Base de Dados Azure para a reserva de capacidade reservada à MariaDB. A quantidade é uma série de vCores na região azure selecionada e nível de desempenho que estão sendo reservados e receberão o desconto de faturação. Por exemplo, se estiver a executar ou a planear executar uma Base de Dados Azure para servidores MariaDB com a capacidade total de computação de Gen5 16 vCores na região leste dos EUA, então especificaria a quantidade como 16 para maximizar o benefício para todos os servidores.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para mais informações, consulte [trocas de self-service e reembolsos para Reservas Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>flexibilidade do tamanho vCore

a flexibilidade do tamanho vCore ajuda-o a escalar para cima ou para baixo dentro de um nível de desempenho e região, sem perder o benefício de capacidade reservado. 

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva vCore é aplicado automaticamente ao número de Bases de Dados Azure para servidores MariaDB que correspondem à Base de Dados Azure para o âmbito e atributos reservados à reserva de capacidade do MariaDB. Pode atualizar o âmbito da base de dados Azure para a reserva de capacidade reservada ao MariaDB através do portal Azure, PowerShell, CLI ou através da API. </br></br>
Para aprender a gerir a Base de Dados Azure para a capacidade reservada da MariaDB, consulte a gestão da Base de Dados Azure para a capacidade reservada do MariaDB.

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

* [O que são Reservas Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Gerir o Azure Reservations](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Compreender o desconto das Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Compreender a utilização de reservas na sua subscrição Pay As You Go](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mariadb)
* [Compreender a utilização de reservas na inscrição Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure Reservations no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros](https://docs.microsoft.com/partner-center/azure-reservations)
