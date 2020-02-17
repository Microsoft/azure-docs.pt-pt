---
title: Capacidade reservada em Azure Cosmos DB para otimizar custo
description: Aprenda a comprar capacidade reservada à Azure Cosmos DB para economizar nos seus custos de computação.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 8e29683b994d66e769a24bb2d386a2120cf8eab9
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367680"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Otimizar o custo com capacidade reservada no Azure Cosmos DB

A capacidade reservada do Azure Cosmos DB ajuda-o a poupar dinheiro ao consolidar uma reserva de recursos do Azure Cosmos DB durante um ou três anos. Com a capacidade reservada do Azure Cosmos DB, pode obter um desconto no débito aprovisionado dos recursos do Cosmos DB. Exemplos de recursos incluem bases de dados e contentores (tabelas, coleções e grafos).

A capacidade reservada da Azure Cosmos DB pode reduzir significativamente os seus custos de DB cosmos&mdash;até 65% a preços regulares com um compromisso inicial de um ano ou três anos. A capacidade reservada proporciona um desconto de faturação e não afeta o estado de funcionamento dos seus recursos DB Azure Cosmos.

A capacidade reservada da Azure Cosmos DB cobre a entrada prevista para os seus recursos. Não abrange os custos de armazenamento e de rede. Assim que comprar uma reserva, as taxas de entrada que correspondem aos atributos de reserva já não são cobradas pelas tarifas pagas. Para mais informações sobre reservas, consulte o artigo de [reservas do Azure.](../cost-management-billing/reservations/save-compute-costs-reservations.md)

Pode comprar a capacidade reservada do Azure Cosmos DB ao [portal Azure.](https://portal.azure.com) Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/monthly-payments-reservations.md). Para comprar capacidade de reserva:

* Você deve estar no papel de Proprietário para pelo menos uma Empresa ou subscrição individual com taxas pay-as-you-go.  
* Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com). Ou, se essa definição for desativada, deve ser um Administrador EA na subscrição.
* Para o programa Cloud Solution Provider (CSP), apenas agentes de administração ou agentes de vendas podem comprar capacidade reservada à Azure Cosmos DB.

## <a name="determine-the-required-throughput-before-purchase"></a>Determine a entrada necessária antes da compra

O tamanho da reserva deve basear-se na quantidade total de produção que os recursos existentes ou em breve implantados azure Cosmos DB utilizarão. Pode ver os tamanhos de reserva recomendados no portal Azure utilizando os seguintes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).  

2. Selecione **todos os serviços** > **Reservas** > **Adicionar**.

3. Do painel de **reservas de compra,** escolha **Azure Cosmos DB**.

4. Selecione o separador **Recomendado** para ver reservas recomendadas:

Pode filtrar recomendações pelos seguintes atributos:

- **Prazo** (1 ano ou 3 anos)
- **Frequência de faturação** (Mensal ou Frontal)
- **Tipo de entrada** (RU's vs Multi-master RU's)

Além disso, pode fazer o âmbito de recomendações para estar dentro de um único grupo de recursos, subscrição única ou toda a sua inscrição no Azure. Pode apresentar recomendações baseadas no uso nos últimos 7 dias, 30 dias ou 60 dias.

Aqui está uma recomendação exemplo:

![Recomendações de Capacidade Reservada](./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png)

Esta recomendação de compra de uma reserva de 30.000 RU/s indica que, entre 3 anos de reservas, um tamanho de reserva de 30.000 RU/s maximizará a poupança. Neste caso, a recomendação é calculada com base nos últimos 30 dias de utilização da Azure Cosmos DB. Se este cliente espera que os últimos 30 dias de utilização da Azure Cosmos DB sejam representativos do uso futuro, maximizarão as poupanças através da compra de uma reserva de 30.000 RU/s.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Comprar capacidade reservada azure Cosmos DB

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).  

2. Selecione **todos os serviços** > **Reservas** > **Adicionar**.  

3. A partir do painel de **reservas de compra,** escolha **Azure Cosmos DB** para comprar uma nova reserva.  

4. Preencha os campos necessários, conforme descrito no quadro seguinte:

   ![Preencha o formulário de capacidade reservado](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Campo  |Descrição  |
   |---------|---------|
   |Âmbito   |   Opção que controla quantas subscrições podem usar o benefício de faturação associado à reserva. Também controla a forma como a reserva é aplicada a subscrições específicas. <br/><br/>  Se selecionar **Shared**, o desconto de reserva é aplicado aos casos De DD Da Azure Cosmos que executam qualquer subscrição dentro do seu contexto de faturação. O contexto de faturação baseia-se na forma como se inscreveu no Azure. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para os clientes pay-as-you-go, o âmbito partilhado são todas as subscrições individuais com taxas de pagamento criadas pelo administrador da conta.  <br/><br/>  Se selecionar **subscrição Single,** o desconto de reserva é aplicado às instâncias Do BD do Azure Cosmos na subscrição selecionada. <br/><br/> Se selecionar o **grupo de recursos Single,** o desconto de reserva é aplicado às instâncias do Azure Cosmos DB na subscrição selecionada e ao grupo de recursos selecionados dentro dessa subscrição. <br/><br/> Pode alterar o âmbito da reserva depois de comprar a capacidade reservada.  |
   |Subscrição  |   Subscrição que é usada para pagar a capacidade reservada do Azure Cosmos DB. O método de pagamento da subscrição selecionada é utilizado para cobrar os custos. A subscrição deve ser um dos seguintes tipos: <br/><br/>  Acordo empresarial (números de oferta: MS-AZR-0017P ou MS-AZR-0148P): Para uma subscrição da Enterprise, os encargos são deduzidos do saldo de compromisso monetário da inscrição ou cobrados como overage. <br/><br/> Subscrição individual com taxas pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P): Para uma subscrição individual com taxas de pagamento, os encargos são cobrados para o cartão de crédito ou método de pagamento de fatura na subscrição.    |
   | Grupo de Recursos | Grupo de recursos ao qual é aplicado o desconto de capacidade reservado. |
   |Termo  |   Um ano ou três anos.   |
   |Tipo de entrada   |  A entrada é aprovisionada como unidades de pedido. Você pode comprar uma reserva para o fornecimento de entrada para ambas as configurações - uma região única escreve, bem como várias escritas de região. O tipo de entrada tem dois valores para escolher: 100 RU/s por hora e 100 RU/s multi-master por hora.|
   | Unidades de Capacidade Reservadas| A quantidade de entrada que quer reservar. Pode calcular este valor determinando a entrada necessária para todos os seus recursos Db Cosmos (por exemplo, bases de dados ou contentores) por região. Em seguida, multiplique-o pelo número de regiões que irá associar à sua base de dados cosmos. Por exemplo: Se tiver cinco regiões com 1 milhão de RU/seg em cada região, selecione 5 milhões de RU/seg para a compra da capacidade de reserva. |


5. Depois de preencher o formulário, calcula-se o preço necessário para comprar a capacidade reservada. A saída também mostra a percentagem de desconto que obtém com as opções escolhidas. Próximo clique **Selecione**

6. No painel **de reservas de compra,** reveja o desconto e o preço da reserva. Este preço de reserva aplica-se aos recursos DB da Azure Cosmos com a entrada disponibilizada em todas as regiões.  

   ![Resumo de capacidade reservada](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Selecione **Review + compre** e, em seguida, **compre agora**. Vê a seguinte página quando a compra for bem sucedida:

Depois de comprar uma reserva, é aplicada imediatamente a quaisquer recursos existentes da Azure Cosmos DB que correspondam aos termos da reserva. Se não tiver recursos DB Azure Cosmos existentes, a reserva será aplicável quando implementar uma nova instância Cosmos DB que corresponda aos termos da reserva. Em ambos os casos, o período da reserva começa imediatamente após uma compra bem sucedida.

Quando a sua reserva expira, os seus casos de Azure Cosmos DB continuam a funcionar e são cobrados às tarifas regulares de pagamento-como-você-go.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Self-service exchanges and refunds for Azure Reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) (Trocas e reembolsos self-service para Reservas do Azure).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva é aplicado automaticamente aos recursos DO Azure Cosmos DB que correspondem ao âmbito de reserva e atributos. Pode atualizar o âmbito da reserva através do portal Azure, PowerShell, Azure CLI ou da API.

*  Para saber como são aplicados descontos de capacidade reservados ao Azure Cosmos DB, consulte Entenda o desconto de [reserva Azure.](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md)

* Para saber mais sobre as reservas do Azure, veja os seguintes artigos:

   * [O que são as reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Gerir reservas do Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [Compreender a utilização de reservas na inscrição Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [Compreender a utilização de reservas na sua subscrição Pay As You Go](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Reservas azure no programa CSP partner Center](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
