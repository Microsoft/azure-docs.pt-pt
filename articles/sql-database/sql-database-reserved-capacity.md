---
title: Economize custos para o banco de dados SQL do Azure vCores
description: Saiba como comprar a capacidade reservada do banco de dados SQL do Azure para economizar em seus custos de computação.
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
ms.openlocfilehash: 70e274d765b409d4024155f8553ba92200598394
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687517"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Economize custos para recursos de computação do banco de dados SQL com capacidade reservada do banco de dados SQL

Economize dinheiro com o banco de dados SQL do Azure confirmando uma reserva de recursos de computação em comparação com os preços pagos conforme o uso. Com a capacidade reservada do banco de dados SQL do Azure, você faz um compromisso com o uso do banco de dados SQL por um período de um ou três anos para obter um desconto significativo nos custos de computação. Para adquirir a capacidade reservada do banco de dados SQL, você precisa especificar a região do Azure, o tipo de implantação, o nível de desempenho e o termo.


Você não precisa atribuir a reserva a instâncias específicas do banco de dados SQL (bancos de dados individuais, pools elásticos ou instâncias gerenciadas). As instâncias de banco de dados SQL correspondentes, que já estão em execução ou que foram implantadas recentemente, obterão automaticamente o benefício. Ao comprar uma reserva, você está comprometido com o uso dos custos de computação por um período de um ou três anos. Assim que você comprar uma reserva, os encargos de computação do banco de dados SQL que corresponderem aos atributos de reserva não serão mais cobrados com as tarifas pagas conforme o uso. Uma reserva não abrange os encargos de software, rede ou armazenamento associados à instância do banco de dados SQL. No final do prazo de reserva, o benefício de cobrança expira e os bancos de dados SQL são cobrados com o preço pago conforme o uso. As reservas não são renovadas automaticamente. Para obter informações sobre preços, consulte a [oferta de capacidade reservada do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Você pode comprar a capacidade reservada do banco de dados SQL do Azure no [portal do Azure](https://portal.azure.com). Pague pela reserva [antecipadamente ou com pagamentos mensais](../billing/billing-monthly-payments-reservations.md). Para comprar a capacidade reservada do banco de dados SQL:

- Você deve estar na função de proprietário de pelo menos uma assinatura corporativa ou individual com tarifas pagas conforme o uso.
- Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com). Ou, se essa configuração estiver desabilitada, você deverá ser um administrador de EA na assinatura.
- Para o programa CSP (provedor de soluções na nuvem), somente os agentes de administração ou os agentes de vendas podem adquirir a capacidade reservada do banco de dados SQL.

Os detalhes sobre como clientes corporativos e clientes pagos conforme o uso são cobrados por compras de reserva, consulte [entender o uso de reserva do Azure para seu registro corporativo](../billing/billing-understand-reserved-instance-usage-ea.md) e [entender o uso de reserva do Azure para seu pré-pago assinatura](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Determinar o tamanho do SQL correto antes da compra

O tamanho da reserva deve ser baseado na quantidade total de computação usada pelos bancos de dados individuais existentes ou em breve para serem implantados, pools elásticos ou instâncias gerenciadas em uma região específica e usando o mesmo nível de desempenho e geração de hardware.

Por exemplo, suponhamos que você esteja executando uma finalidade geral, Gen5 – 16 pool elástico do vCore e dois bancos de dados de negócios de Gen5 – 4. Além disso, vamos pretender que você planeje implantar no próximo mês uma finalidade geral adicional, Gen5 – 16 pool elástico do vCore e uma empresa crítica, Gen5 – pool elástico vCore de 32. Além disso, vamos supor que você saiba que precisará desses recursos por pelo menos 1 ano. Nesse caso, você deve comprar um vCores de 32 (2x16), reserva de 1 ano para o único banco de dados/pool elástico de uso geral-Gen5 e uma reserva de 40 (2x4 + 32) de 1 ano para o banco de dados/pool elástico comercial crítico-Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Comprar capacidade reservada do banco de dados SQL

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** e, em seguida, no painel reservas de compra, selecione **banco de dados SQL** para comprar uma nova reserva para o banco de dados SQL.
4. Preencha os campos obrigatórios. Bancos de dados individuais novos ou existentes, pools elásticos ou instâncias gerenciadas que correspondem aos atributos que você selecionar qualificar para obter o desconto de capacidade reservada. O número real de suas instâncias do banco de dados SQL que obtém o desconto depende do escopo e da quantidade selecionada.
    ![captura de tela antes de enviar a compra de capacidade reservada do banco de dados SQL](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

A tabela a seguir descreve os campos obrigatórios.

| Campo      | Descrição|
|------------|--------------|
|Subscrição|A assinatura usada para pagar a reserva de capacidade reservada do banco de dados SQL. O método de pagamento na assinatura é cobrado pelos custos iniciais para a reserva de capacidade reservada do banco de dados SQL. O tipo de assinatura deve ser um Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um contrato individual com preço pago conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma subscrição Enterprise, os custos são deduzidos do saldo de fidelização monetária da inscrição ou cobrados como utilização excedida. Para uma assinatura individual com o preço pago conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.|
|Âmbito       |O escopo da reserva de vCore pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado). Se você selecionar: <br/><br/>**Compartilhado**, o desconto de reserva vCore é aplicado às instâncias do banco de dados SQL em execução em qualquer assinatura em seu contexto de cobrança. Para clientes corporativos, o escopo compartilhado é o registro e inclui todas as assinaturas dentro do registro. Para clientes Pay As You Go, o âmbito partilhado é todas as subscrições Pay As You Go criadas pelo administrador de conta.<br/><br/>**Assinatura única**, o desconto de reserva vCore é aplicado às instâncias do banco de dados SQL nesta assinatura. <br/><br/>**Um grupo de recursos único**, o desconto de reserva é aplicado às instâncias do banco de dados SQL na assinatura selecionada e ao grupo de recursos selecionado dentro dessa assinatura.|
|Região      |A região do Azure que é coberta pela reserva de capacidade reservada do banco de dados SQL.|
|Tipo de implantação|O tipo de recurso SQL para o qual você deseja comprar a reserva.|
|Escalão de Desempenho|A camada de serviço para as instâncias do banco de dados SQL.
|Termo        |Um ano ou três anos.|
|Quantidade    |A quantidade de recursos de computação que está sendo adquirida na reserva de capacidade reservada do banco de dados SQL. A quantidade é um número de vCores na região do Azure e no nível de desempenho selecionados que estão sendo reservados e receberão o desconto de cobrança. Por exemplo, se você estiver executando ou planejando executar instâncias do banco de dados SQL com a capacidade de computação total de Gen5 16 vCores na região leste dos EUA, você deverá especificar Quantity como 16 para maximizar o benefício de todas as instâncias. |

1. Examine o custo da reserva de capacidade reservada do banco de dados SQL na seção de **custos** .
1. Selecione **Comprar**.
1. Selecione **exibir esta reserva** para ver o status de sua compra.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Self-service exchanges and refunds for Azure Reservations](../billing/billing-azure-reservations-self-service-exchange-and-refund.md) (Trocas e reembolsos self-service para Reservas do Azure).

## <a name="vcore-size-flexibility"></a>flexibilidade do tamanho vCore

a flexibilidade do tamanho vCore ajuda você a escalar ou reduzir verticalmente dentro de um nível de desempenho e região, sem perder o benefício de capacidade reservada. A capacidade reservada do banco de dados SQL também oferece a você a flexibilidade de mover temporariamente seus bancos de dados ativos entre pools e bancos de dados individuais como parte de suas operações normais (na mesma região e nível de desempenho) sem perder a capacidade reservada contam. Ao manter um buffer não aplicado em sua reserva, você pode gerenciar efetivamente os picos de desempenho sem exceder seu orçamento.

## <a name="limitation"></a>Limitação

Não é possível reservar bancos de dados SQL baseados em DTU (Basic, Standard ou Premium).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva vCore é aplicado automaticamente ao número de instâncias do banco de dados SQL que correspondem ao escopo e aos atributos de reserva de capacidade reservada do banco de dados SQL. Você pode atualizar o escopo da reserva de capacidade reservada do banco de dados SQL por meio de [portal do Azure](https://portal.azure.com), PowerShell, CLI ou por meio da API.

Para saber como gerenciar a reserva de capacidade reservada do banco de dados SQL, consulte [gerenciar capacidade reservada do banco de dados SQL](../billing/billing-manage-reserved-vm-instance.md).

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

- [O que são as reservas do Azure?](../billing/billing-save-compute-costs-reservations.md)
- [Gerir o Azure Reservations](../billing/billing-manage-reserved-vm-instance.md)
- [Compreender o desconto das Reservas do Azure](../billing/billing-understand-reservation-charges.md)
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](../billing/billing-understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)
- [Azure Reservations no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros](https://docs.microsoft.com/partner-center/azure-reservations)
