---
title: Capacidade reservada na Azure Cosmos DB para otimizar custos
description: Saiba como comprar a Azure Cosmos DB reservada capacidade para economizar nos seus custos de cálculo.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 5c2d1d286572b21879742a1a9c6ab3975441373d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98602678"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Otimizar o custo com a capacidade de reserva no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A capacidade reservada do Azure Cosmos DB ajuda-o a poupar dinheiro ao consolidar uma reserva de recursos do Azure Cosmos DB durante um ou três anos. Com a capacidade reservada do Azure Cosmos DB, pode obter um desconto no débito aprovisionado dos recursos do Cosmos DB. Exemplos de recursos incluem bases de dados e contentores (tabelas, coleções e grafos).

A capacidade reservada da Azure Cosmos DB pode reduzir significativamente os custos do seu Cosmos DB &mdash; até 65% em preços regulares com um compromisso inicial de um ano ou três anos. A capacidade reservada proporciona um desconto de faturação e não afeta o estado de funcionamento dos seus recursos DB Azure Cosmos.

A Azure Cosmos DB de capacidade reservada cobre o rendimento previsto para os seus recursos. Não abrange os custos de armazenamento e de rede. Assim que comprar uma reserva, os encargos de produção que correspondam aos atributos da reserva já não são cobrados nas tarifas pagas. Para mais informações sobre reservas, consulte o artigo reservas do [Azure.](../cost-management-billing/reservations/save-compute-costs-reservations.md)

Você pode comprar Azure Cosmos DB capacidade reservada a partir do [portal Azure](https://portal.azure.com). Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/prepare-buy-reservation.md). Para comprar capacidade de reserva:

* Você deve estar na função Proprietário para pelo menos uma Enterprise ou subscrição individual com taxas pay-as-you-go.  
* Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com). Ou, se essa definição estiver desativada, deve ser um Administrador EA na subscrição.
* Para o programa Cloud Solution Provider (CSP), apenas os agentes administrativos ou agentes de vendas podem comprar capacidade reservada ao Azure Cosmos DB.

## <a name="determine-the-required-throughput-before-purchase"></a>Determinar a produção necessária antes da compra

A dimensão da compra de capacidade reservada deve basear-se na quantidade total de produção que os recursos DB da Azure Cosmos existentes ou em breve serão utilizados utilizarão de hora a hora. Por exemplo: Compre 30.000 RU/s de capacidade reservada se for esse o seu padrão de utilização horário consistente. Neste exemplo, qualquer produção a provisionada acima de 30.000 RU/s será faturada usando a sua taxa Pay-as-you-go. Se a produção for abaixo dos 30.000 RU/s em uma hora, então a capacidade extra reservada para essa hora será desperdiçada.

Calculamos recomendações de compra com base no seu padrão de utilização horária. O uso ao longo dos últimos 7, 30 e 60 dias é analisado, e recomenda-se a compra de capacidade reservada que maximize as suas poupanças. Pode ver os tamanhos de reserva recomendados no portal Azure utilizando os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).  

2. Selecione **Todos os**  >  **serviços Reservas**  >  **Adicionar.**

3. A partir do painel **de reservas de compras,** escolha **Azure Cosmos DB**.

4. Selecione o separador **Recomendado** para visualizar reservas recomendadas:

Pode filtrar recomendações pelos seguintes atributos:

- **Prazo** (1 ano ou 3 anos)
- **Frequência de faturação** (Mensal ou Adiantado)
- **Tipo de produção** (RU/s vs multi-região escrever RU/s)

Além disso, pode estender recomendações para estar dentro de um único grupo de recursos, subscrição única ou toda a sua inscrição no Azure. 

Aqui está uma recomendação de exemplo:

:::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png" alt-text="Recomendações de capacidade reservada":::

Esta recomendação de compra de uma reserva de 30.000 RU/s indica que, entre 3 anos de reservas, um tamanho de reserva de 30.000 RU/s maximizará a poupança. Neste caso, a recomendação é calculada com base nos últimos 30 dias de utilização da Azure Cosmos DB. Se este cliente espera que os últimos 30 dias de utilização da Azure Cosmos DB sejam representativos do uso futuro, maximizariam as poupanças comprando uma reserva de 30.000 RU/s.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Comprar capacidade reservada da Azure Cosmos DB

1. Inicie sessão no [portal do Azure](https://portal.azure.com).  

2. Selecione **Todos os**  >  **serviços Reservas**  >  **Adicionar.**  

3. A partir do painel **de reservas de compras,** escolha **Azure Cosmos DB** para comprar uma nova reserva.  

4. Preencha os campos necessários, conforme descrito no quadro seguinte:

   :::image type="content" source="./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png" alt-text="Preencha o formulário de capacidade reservada":::

   |Campo  |Descrição  |
   |---------|---------|
   |Âmbito   |   Opção que controla quantas subscrições podem usar o benefício de faturação associado à reserva. Também controla a forma como a reserva é aplicada a subscrições específicas. <br/><br/>  Se selecionar **Shared,** o desconto de reserva é aplicado a instâncias DB da Azure Cosmos que executam em qualquer subscrição dentro do seu contexto de faturação. O contexto de faturação baseia-se na forma como se inscreveu no Azure. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para os clientes pay-as-you-go, o âmbito partilhado é todas as subscrições individuais com taxas de pagamento criadas pelo administrador da conta.  <br/><br/>  Se selecionar **uma subscrição única,** o desconto de reserva é aplicado às instâncias DB da Azure Cosmos na subscrição selecionada. <br/><br/> Se selecionar **um grupo de recursos único,** o desconto de reserva é aplicado às instâncias DB da Azure Cosmos na subscrição selecionada e no grupo de recursos selecionado dentro dessa subscrição. <br/><br/> Pode alterar o âmbito de reserva depois de comprar a capacidade reservada.  |
   |Subscrição  |   Subscrição que é usada para pagar a capacidade reservada do Azure Cosmos DB. O método de pagamento na subscrição selecionada é utilizado para cobrar os custos. A subscrição deve ser um dos seguintes tipos: <br/><br/>  Acordo de Empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P): Para uma subscrição da Enterprise, os encargos são deduzidos do saldo Azure Prepayment (anteriormente chamado compromisso monetário) ou cobrados como overage. <br/><br/> Subscrição individual com taxas pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P): Para uma subscrição individual com taxas pay-as-you-go, os encargos são cobrados no cartão de crédito ou método de pagamento de fatura na subscrição.    |
   | Grupo de Recursos | Grupo de recursos ao qual é aplicado o desconto de capacidade reservada. |
   |Termo  |   Um ano ou três anos.   |
   |Tipo de produção   |  A produção é a provisionada como unidades de pedido. Você pode comprar uma reserva para o rendimento previsto para ambas as configurações - uma única região escreve, bem como várias regiões escrevem. O tipo de produção tem dois valores para escolher: 100 RU/s por hora e 100 multi-regiões escreve RU/s por hora.|
   | Unidades de Capacidade Reservadas| A quantidade de produção que quer reservar. Pode calcular este valor determinando a produção necessária para todos os seus recursos DeSportes (por exemplo, bases de dados ou contentores) por região. Em seguida, multiplique-a pelo número de regiões que irá associar à sua base de dados Cosmos. Por exemplo: Se tiver cinco regiões com 1 milhão de RU/seg em cada região, selecione 5 milhões de RU/seg para a compra da capacidade de reserva. |


5. Depois de preencher o formulário, calcula-se o preço necessário para a compra da capacidade reservada. A saída também mostra a percentagem de desconto que obtém com as opções escolhidas. Clique em **seguida Selecionar**

6. No painel **de reservas de compra,** reveja o desconto e o preço da reserva. Este preço de reserva aplica-se aos recursos DB da Azure Cosmos com produção abastada em todas as regiões.  

   :::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png" alt-text="Resumo da capacidade reservada":::

7. Selecione **Review + compre** e depois compre **agora.** Vê a seguinte página quando a compra é bem sucedida:

Depois de comprar uma reserva, é imediatamente aplicada a quaisquer recursos DB Azure Cosmos existentes que correspondam aos termos da reserva. Se não tiver quaisquer recursos DB Azure Cosmos existentes, a reserva será aplicada quando implementar uma nova instância Cosmos DB que corresponda aos termos da reserva. Em ambos os casos, o período da reserva começa imediatamente após uma compra bem sucedida.

Quando a sua reserva expira, as suas instâncias DB Azure Cosmos continuam a decorrer e são cobradas nas tarifas regulares de pagamento à medida que vão.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva é aplicado automaticamente aos recursos DB da Azure Cosmos que correspondem ao âmbito e atributos da reserva. Pode atualizar o âmbito da reserva através do portal Azure, PowerShell, Azure CLI ou da API.

*  Para saber como são aplicados os descontos de capacidade reservados à Azure Cosmos DB, consulte [o desconto de reserva Azure](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md).

* Para saber mais sobre reservas do Azure, veja os seguintes artigos:

   * [O que são reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Gerir reservas do Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [Compreender a utilização de reservas na inscrição Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [Compreender a utilização de reservas na sua subscrição Pay As You Go](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Reservas Azure no programa CSP do Centro Parceiro](/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).