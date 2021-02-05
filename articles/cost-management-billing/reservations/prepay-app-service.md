---
title: Economizar no Serviço de Aplicações do Azure com a capacidade reservada
description: Saiba como pode economizar custos para a Azure App Service Premium v3 instâncias reservadas e Taxa de Selo Isolada.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/01/2021
ms.author: banders
ms.custom: references_regions
ms.openlocfilehash: 89e0c62b580c0c354fc7277e61b452005a86e3d9
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99577814"
---
# <a name="save-costs-with-azure-app-service-reserved-instances"></a>Economize custos com o Azure App Service de instâncias reservadas

Este artigo explica como pode economizar com o Azure App Service reservado instâncias para instâncias Premium v3 e Taxas de Selo Isoladas.

## <a name="save-with-premium-v3-reserved-instances"></a>Salvar com premium v3 instâncias reservadas

Quando se compromete com uma instância reservada do Azure App Service Premium v3, pode economizar dinheiro. O desconto de reserva é aplicado automaticamente ao número de casos de execução que correspondem ao âmbito e atributos da reserva. Não precisa atribuir uma reserva a um caso para obter os descontos.

## <a name="determine-the-right-reserved-instance-size-before-you-buy"></a>Determine o tamanho da instância reservada certa antes de comprar

Antes de comprar uma reserva, deverá determinar o tamanho do caso reservado Premium v3 de que necessita. As seguintes secções irão ajudá-lo a determinar o tamanho certo de instância reservado Premium v3.

### <a name="use-reservation-recommendations"></a>Use recomendações de reserva

Pode usar recomendações de reserva para ajudar a determinar as reservas que deve comprar.

- Recomendações de compra e quantidade recomendada são mostradas quando compra um premium v3 reservado no portal Azure.
- O Azure Advisor fornece recomendações de compra para subscrições individuais.
- Pode utilizar as APIs para obter recomendações de compra tanto para âmbito partilhado como para o âmbito de subscrição único. Para obter mais informações, consulte [APIs de recomendação de compra de instância reservada para clientes empresariais.](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)
- Para os clientes do Enterprise Agreement (EA) e do Microsoft Customer Agreement (MCA), as recomendações de compra para âmbitos de subscrição partilhados e únicos estão disponíveis com o [pacote de conteúdos Azure Consumption Insights Power BI](/power-bi/service-connect-to-azure-consumption-insights).

#### <a name="instance-size-flexibility-setting"></a>Definição de flexibilidade de tamanho de instância

A definição de flexibilidade de tamanho de instância determina quais serviços recebem os descontos da instância reservada.

Quer a definição esteja dentro ou fora, os descontos de reserva aplicam-se automaticamente a qualquer utilização de instância reservada premium v3 correspondente.

### <a name="analyze-your-usage-information"></a>Analise as suas informações de utilização

Analise as suas informações de utilização para ajudar a determinar quais as reservas que deve adquirir. Os dados de utilização estão disponíveis no ficheiro de utilização e nas APIs. Use-os juntos para determinar que reserva comprar. Verifique se os casos Premium v3 têm uma elevada utilização diária para determinar a quantidade de reservas a comprar.

O seu ficheiro de utilização mostra os seus encargos por período de faturação e uso diário. Para obter informações sobre o download do seu ficheiro de utilização, consulte [Ver e descarregue o seu uso e custos Azure](../understand/download-azure-daily-usage.md). Em seguida, utilizando as informações do ficheiro de utilização, pode [determinar qual a reserva a comprar.](determine-reservation-purchase.md)

### <a name="purchase-restriction-considerations"></a>Considerações de restrição de compra

Os descontos de reserva não se aplicam às seguintes instâncias Premium v3:

- **Pré-visualização ou instâncias Promo** - Qualquer série ou tamanho reservado a exemplos premium v3 que esteja em pré-visualização ou utilize o medidor promocional.
- **Nuvens** - As reservas não estão disponíveis para compra nas regiões da Alemanha ou da China.

## <a name="buy-a-premium-v3-reserved-instance"></a>Comprar um Premium v3 instância reservada

Pode comprar um bilhete reservado Premium v3 reservado no [portal Azure.](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D) Pague pela reserva [antecipadamente ou com pagamentos mensais](prepare-buy-reservation.md). Estes requisitos aplicam-se à compra de um exemplo reservado premium v3:

- Você deve estar numa função proprietário para pelo menos uma subscrição EA ou uma subscrição com uma taxa de pagamento como você-go.
- Para as assinaturas EA, a opção **Add Reserved Instances** deve ser ativada no [portal EA](https://ea.azure.com/). Em alternativa, se essa definição estiver desativada, terá de ser o Administrador EA da subscrição.
- Para o programa Cloud Solution Provider (CSP), apenas os agentes administrativos ou agentes de vendas podem comprar reservas.

Para comprar uma instância:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3. **Selecione Adicionar** para comprar uma nova reserva e, em seguida, clique em **Exemplo**.
4. Introduza os campos necessários. Executar As instâncias reservadas premium v3 que correspondem aos atributos que seleciona qualificam-se para o desconto de reserva. O número real dos seus casos reservados Premium v3 que obtêm o desconto dependem do âmbito e quantidade selecionados.

Se tiver um acordo EA, pode utilizar a **opção Adicionar mais** rapidamente para adicionar instâncias adicionais. A opção não está disponível para outros tipos de subscrição.

| **Campo** | **Descrição** |
|---|---|
| Subscrição | A assinatura usada para pagar a reserva. Os custos de reserva são cobrados de acordo com o método de pagamento indicado na subscrição. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Microsoft Customer Agreement ou uma subscrição individual com taxas pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Os custos são deduzidos do saldo de fidelização monetária, se disponível, ou cobrados como utilização excedida. Para uma subscrição com taxas pay-as-you-go, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição. |
| Âmbito | O âmbito da reserva pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar: <ul><li>**Âmbito de grupo de recursos único** – aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionado. </li><li>**Âmbito de subscrição individual** – aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.</li><li>**Âmbito partilhado** – aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que estejam no contexto de faturação. Para os clientes da EA, o contexto de faturação é a inscrição. Para subscrições individuais com taxas pay as you go, o âmbito da faturação reside em todas as subscrições elegíveis criadas pelo administrador de conta.</li></ul> |
| Region | A região de Azure que está coberta pela reserva. |
| Premium v3 tamanho de instância reservado | O tamanho dos casos reservados Premium v3. |
| Otimizar para | Premium v3 A flexibilidade do tamanho do tamanho da instância reservada é selecionada por padrão. Clique **em definições avançadas** para alterar o valor de flexibilidade do tamanho da instância para aplicar o desconto de reserva a outras instâncias premium v3 reservadas no mesmo [grupo de tamanhos Premium v3 Reserved Instance](../../virtual-machines/reserved-vm-instance-size-flexibility.md). A prioridade de capacidade prioriza a capacidade de datacenter das implementações. Oferece confiança adicional na sua capacidade de lançar os casos reservados Premium v3 quando precisa deles. A prioridade da capacidade só está disponível quando o âmbito de reserva é uma subscrição única. |
| Termo | Um ano ou três anos. Há também um prazo de 5 anos disponível apenas para instâncias reservadas HBv2 Premium v3. |
| Quantidade | O número de casos a serem comprados dentro da reserva. A quantidade é o número de casos reservados Premium v3 que podem obter o desconto de faturação. Por exemplo, se estiver a executar 10 \_ instâncias reservadas Standard D2 Premium v3 nos EUA, então especificaria a quantidade como 10 para maximizar o benefício para todas as instâncias reservadas Premium v3 em execução. |

## <a name="save-with-isolated-stamp-fees"></a>Poupe com taxas de selo isoladas

Pode poupar dinheiro no Imposto de Selo do Plano Isolado do Serviço de Aplicações ao consolidar uma reserva para a sua utilização de selos durante três anos. Para comprar capacidade de reserva do Imposto de Selo do Plano Isolado, tem de escolher a região do Azure onde o carimbo será implementado e o número de selos a comprar.

Quando compra uma reserva, a utilização do Imposto de Selo do Plano Isolado que corresponde aos atributos da reserva deixa de ser cobrada às tarifas de pay as you go. A reserva é aplicada automaticamente ao número de selos de Plano Isolado que correspondem à região e ao âmbito da capacidade de reserva. Não precisa de atribuir uma reserva a um selo de plano isolado. A reserva não se aplica às funções de trabalho, pelo que todos os outros recursos associados ao selo serão cobrados separadamente.

Quando a capacidade reservada expirar, os Selos de Plano Isolado continuam a ser executados, mas são cobrados à tarifa de pay as you go. As reservas não se renovam automaticamente.

## <a name="determine-the-right-isolated-stamp-reservation-to-purchase"></a>Determine a reserva de selo isolado certo para comprar

Ao comprar uma reserva, está a comprometer-se com a utilização de quantidades reservadas nos próximos três anos. Verifique os seus dados de utilização para determinar quantos Selos do Plano Isolado do Serviço de Aplicações está a usar consistentemente e pode usar no futuro.

Além disso, confirme que compreender como o Selo de Plano Isolado emite o medidor do Linux ou do Windows.

- Por predefinição, um Selo de Plano Isolado vazio emite o medidor de selos do Windows. Por exemplo, sem funções de trabalho implementadas. Continua a emitir este medidor se as funções de trabalho do Windows forem implementadas no selo.
- O medidor muda para o medidor de selos do Linux se implementar uma função de trabalho do Linux.
- Nos casos em que se implementem funções de trabalho do Linux e do Windows, o selo emite o medidor do Windows.

Portanto, o medidor de selos pode mudar entre o Windows e o Linux durante a vida útil do selo.

Compre reservas de selos do Windows se tiver um ou mais funções de trabalho do Windows no selo. A única vez que deve comprar uma reserva de selo do Linux é se planear ter _apenas_ funções de trabalho do Linux no selo.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Comprar capacidade de reserva de Selos de Plano Isolado

Pode comprar capacidade de reserva de Plano Isolado no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Pague pela reserva [antecipadamente ou com pagamentos mensais](./prepare-buy-reservation.md). Para comprar a capacidade de reserva, deve ter a função de proprietário em pelo menos uma subscrição Enterprise ou uma subscrição individual com tarifas pay as you go.

- Para subscrições Enterprise, a opção **Adicionar Instâncias Reservadas** deve ser ativada no [EA Portal](https://ea.azure.com/). Em alternativa, se a definição estiver desativada, tem ser um Administrador EA.
- Para o programa Fornecedor de Soluções Cloud (CSP), apenas os agentes de administração ou os agentes de vendas podem comprar capacidade reservada do Azure Synapse Analytics.

**Para Comprar:**

1. Aceda ao [Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Selecione uma subscrição. Use a lista **Subscrição** para escolher a subscrição que serve para pagar a capacidade de reserva. Os custos são cobrados de acordo com o método de pagamento indicado na capacidade de reserva. O tipo de subscrição tem de ser um contrato Enterprise (número da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Pay As You Go (números da oferta: MS-AZR-0003P ou MS-AZR-0023P) ou uma subscrição CSP.
    - Para uma subscrição Enterprise, os custos são deduzidos do saldo do Pré-pagamento do Azure (que antes se chamava alocação monetária) da inscrição ou cobrados como utilização excedida.
    - Para a subscrição Pay As You Go, os custos são debitados no cartão de crédito ou cobrados de acordo com o método de pagamento indicado na subscrição.
1. Selecione um **Âmbito** para escolher um âmbito de subscrição.
    - **Âmbito de grupo de recursos único** – aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionado.
    - **Âmbito de subscrição individual** – aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.
    - **Âmbito partilhado** – aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que estejam no contexto de faturação. Para clientes do Contrato Enterprise, o contexto de faturação é a inscrição. Para subscrições individuais com taxas pay as you go, o âmbito da faturação reside em todas as subscrições elegíveis criadas pelo administrador de conta.
1. Selecione uma **Região** para escolher uma região do Azure coberta pela capacidade de reserva e adicione a reserva ao carrinho.
1. Selecione um tipo de Plano Isolado e clique em **Selecionar**.  
    ![Exemplo ](./media/prepay-app-service/app-service-isolated-stamp-select.png)
1. Insira a quantidade de selos de Plano Isolado do Serviço de Aplicações a reservar. Por exemplo, uma quantidade de três dar-lhe-ia três selos reservados por região. Clique em **Seguinte: Rever + Comprar**.
1. Reveja e clique em **Comprar agora**.

Após a compra, aceda a [Reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) para ver o estado da compra e monitorize-as em qualquer altura.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](exchange-and-refund-azure-reservations.md).

## <a name="discount-application-shown-in-usage-data"></a>Aplicação de desconto mostrada nos dados de utilização

Os dados de utilização têm um preço efetivo de zero pela utilização que obtém um desconto de reserva. Os dados de utilização mostram o desconto de reserva para cada instância de selo em cada reserva.

Para obter mais informações sobre como o desconto de reserva é mostrado nos dados de utilização, veja [Get Enterprise Agreement reservation costs and usage](understand-reserved-instance-usage-ea.md) (Compreender custos de reserva e utilização do Contrato Enterprise) se for um cliente de Contrato Enterprise (EA). Caso contrário, veja [Compreender a utilização de reservas do Azure para a sua subscrição individual com tarifas pay as you go](understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:
  - [O que são as reservas do Azure?](save-compute-costs-reservations.md)
  - [Compreender a forma como é aplicado um desconto de reserva de Selo de Plano Isolado do Serviço de Aplicações do Azure](reservation-discount-app-service.md)
  - [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)