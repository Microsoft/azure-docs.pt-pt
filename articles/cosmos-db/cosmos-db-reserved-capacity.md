---
title: Otimize o custo de Azure Cosmos DB recursos com capacidade reservada
description: Saiba como comprar Azure Cosmos DB capacidade reservada para economizar em seus custos de computação.
author: bandersmsft
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 80ee55ad6b0a8034e225f291b28ad478be82165a
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806484"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Otimize o custo com capacidade reservada no Azure Cosmos DB

Azure Cosmos DB capacidade reservada ajuda você a economizar dinheiro confirmando uma reserva de Azure Cosmos DB recursos por um ou três anos. Com Azure Cosmos DB capacidade reservada, você pode obter um desconto na taxa de transferência provisionada para Cosmos DB recursos. Exemplos de recursos são bancos de dados e contêineres (tabelas, coleções e grafos).

Azure Cosmos DB capacidade reservada pode reduzir significativamente sua Cosmos DB&mdash;custa até 65% em preços regulares com um compromisso antecipado de um ou três anos. A capacidade reservada fornece um desconto de cobrança e não afeta o estado de tempo de execução de seus recursos de Azure Cosmos DB.

Azure Cosmos DB capacidade reservada abrange a taxa de transferência provisionada para seus recursos. Ele não cobre o armazenamento e os encargos de rede. Assim que você comprar uma reserva, os encargos de taxa de transferência que corresponderem aos atributos de reserva não serão mais cobrados com as tarifas pagas conforme o uso. Para obter mais informações sobre reservas, consulte o artigo [reservas do Azure](../billing/billing-save-compute-costs-reservations.md) .

Você pode comprar Azure Cosmos DB capacidade reservada do [portal do Azure](https://portal.azure.com). Pague pela reserva [antecipada ou com pagamentos mensais](../billing/billing-monthly-payments-reservations.md). Para comprar a capacidade reservada:

* Você deve estar na função de proprietário de pelo menos uma assinatura corporativa ou individual com tarifas pagas conforme o uso.  
* Para assinaturas empresariais, **adicionar instâncias reservadas** deve estar habilitada no [portal de ea](https://ea.azure.com). Ou, se essa configuração estiver desabilitada, você deverá ser um administrador de EA na assinatura.
* Para o programa CSP (provedor de soluções na nuvem), somente agentes de administração ou agentes de vendas podem comprar Azure Cosmos DB capacidade reservada.

## <a name="determine-the-required-throughput-before-purchase"></a>Determinar a taxa de transferência necessária antes da compra

O tamanho da reserva deve ser baseado na quantidade total de taxa de transferência que os recursos existentes ou em breve ser implantados Azure Cosmos DB usarão. Você pode determinar a taxa de transferência necessária das seguintes maneiras:

* Obtenha os dados históricos para a taxa de transferência total provisionada em suas contas de Azure Cosmos DB, bancos de dados e coleções em todas as regiões. Por exemplo, você pode avaliar a média de produtividade provisionada diária baixando sua instrução de uso `https://account.azure.com`diário de.

* Se você for um cliente do Enterprise Agreement (EA), poderá baixar seu arquivo de uso para obter os detalhes da Azure Cosmos DB taxa de transferência. Consulte o valor do **tipo de serviço** na seção **informações adicionais** do arquivo de uso.

* Você pode somar a taxa de transferência média para todas as cargas de trabalho em suas contas de Azure Cosmos DB que você espera executar para os próximos um ou três anos. Em seguida, você pode usar essa quantidade para a reserva.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Comprar Azure Cosmos DB capacidade reservada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).  

2. Selecione **todos os serviços** > **reservas** > **Adicionar**.  

3. No painel **reservas de compra** , escolha **Azure Cosmos DB** para comprar uma nova reserva.  

4. Preencha os campos obrigatórios, conforme descrito na tabela a seguir:

   ![Preencher o formulário de capacidade reservada](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Campo  |Descrição  |
   |---------|---------|
   |Scope   |   Opção que controla quantas assinaturas podem usar o benefício de cobrança associado à reserva. Ele também controla como a reserva é aplicada a assinaturas específicas. <br/><br/>  Se você selecionar **compartilhado**, o desconto de reserva será aplicado a Azure Cosmos DB instâncias executadas em qualquer assinatura em seu contexto de cobrança. O contexto de cobrança é baseado em como você se inscreveu no Azure. Para clientes corporativos, o escopo compartilhado é o registro e inclui todas as assinaturas dentro do registro. Para clientes pagos conforme o uso, o escopo compartilhado é todas as assinaturas individuais com tarifas pagas conforme o uso criadas pelo administrador da conta.  <br/><br/>  Se você selecionar **assinatura única**, o desconto de reserva será aplicado a Azure Cosmos DB instâncias na assinatura selecionada. <br/><br/> Se você selecionar **um único grupo de recursos**, o desconto de reserva será aplicado a Azure Cosmos DB instâncias na assinatura selecionada e ao grupo de recursos selecionado dentro dessa assinatura. <br/><br/> Você pode alterar o escopo de reserva depois de comprar a capacidade reservada.  |
   |Subscription  |   A assinatura que é usada para pagar pelo Azure Cosmos DB capacidade reservada. O método de pagamento na assinatura selecionada é usado para cobrar os custos. A assinatura deve ser um dos seguintes tipos: <br/><br/>  Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P): Para uma assinatura corporativa, os encargos são deduzidos do saldo de compromisso monetário do registro ou cobrados como excedentes. <br/><br/> Assinatura individual com tarifas pagas conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P): Para uma assinatura individual com tarifas pagas conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.    |
   | Grupo de Recursos | Grupo de recursos ao qual o desconto de capacidade reservada é aplicado. |
   |Termo  |   Um ano ou três anos.   |
   |Tipo de Débito   |  A taxa de transferência é provisionada como unidades de solicitação. Você pode comprar uma reserva para a taxa de transferência provisionada para as duas configurações – gravações de região única, bem como várias gravações de região. O tipo de taxa de transferência tem dois valores para escolher: 100 RU/s por hora e 100 vários mestres RU/s por hora.|
   | Unidades de Capacidade Reservadas| A quantidade de taxa de transferência que você deseja reservar. Você pode calcular esse valor determinando a taxa de transferência necessária para todos os seus Cosmos DB recursos (por exemplo, bancos de dados ou contêineres) por região. Em seguida, você o multiplica pelo número de regiões que você associará ao banco de dados Cosmos. Por exemplo: Se você tiver cinco regiões com 1 milhão RU/seg em todas as regiões, selecione 5 milhões RU/s para a compra de capacidade de reserva. |


5. Depois de preencher o formulário, o preço necessário para comprar a capacidade reservada é calculado. A saída também mostra a porcentagem de desconto que você obtém com as opções escolhidas. Em seguida, clique em **selecionar**

6. No painel **reservas de compra** , examine o desconto e o preço da reserva. Esse preço de reserva se aplica a Azure Cosmos DB recursos com taxa de transferência provisionada em todas as regiões.  

   ![Resumo da capacidade reservada](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Selecione **revisar + comprar** e **comprar agora**. Você verá a página a seguir quando a compra for bem-sucedida:

Depois de comprar uma reserva, ela é aplicada imediatamente a todos os recursos existentes do Azure Cosmos DB que correspondem aos termos da reserva. Se você não tiver recursos de Azure Cosmos DB existentes, a reserva será aplicada quando você implantar uma nova instância de Cosmos DB que corresponda aos termos da reserva. Em ambos os casos, o período da reserva começa imediatamente após uma compra bem-sucedida.

Quando sua reserva expirar, suas instâncias de Azure Cosmos DB continuarão sendo executadas e serão cobradas com as tarifas pagas conforme o uso normais.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Para obter ajuda com a identificação da capacidade reservada correta, consulte [entender como o desconto de reserva é aplicado a Azure Cosmos DB](../billing/billing-understand-cosmosdb-reservation-charges.md).

Você pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, consulte [trocas e reembolsos de autoatendimento para reservas do Azure](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva é aplicado automaticamente aos recursos de Azure Cosmos DB que correspondem ao escopo de reserva e aos atributos. Você pode atualizar o escopo da reserva por meio do portal do Azure, do PowerShell, do CLI do Azure ou da API.

*  Para saber como os descontos de capacidade reservados são aplicados a Azure Cosmos DB, consulte [entender o desconto de reserva do Azure](../billing/billing-understand-cosmosdb-reservation-charges.md).

* Para saber mais sobre as reservas do Azure, consulte os seguintes artigos:

   * [O que são as reservas do Azure?](../billing/billing-save-compute-costs-reservations.md)  
   * [Gerir reservas do Azure](../billing/billing-manage-reserved-vm-instance.md)  
   * [Compreender a utilização de reservas na inscrição Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Compreender a utilização de reservas na sua subscrição Pay As You Go](../billing/billing-understand-reserved-instance-usage.md)
   * [Reservas do Azure no programa CSP do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
