---
title: Pré-pagamento para máquinas virtuais Azure para economizar dinheiro
description: Saiba como comprar Azure Reserved Virtual Machine Instances para economizar nos seus custos de cálculo.
author: vikramdesai01
manager: vikramdesai01
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2017
ms.author: vikdesai
ms.openlocfilehash: 5df56b304463ab981026fbe0c82d831d83d702c3
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380266"
---
# <a name="save-costs-with-azure-reserved-vm-instances-linux"></a>Poupe custos com Azure Reserved VM Instances (Linux)



Quando se compromete com uma instância VM reservada ao Azure, pode economizar dinheiro. O desconto de reserva é aplicado automaticamente ao número de máquinas virtuais em execução que correspondem ao âmbito e atributos da reserva. Não precisa de atribuir uma reserva a uma máquina virtual para obter os descontos. Uma compra de instância reservada cobre apenas a parte computacional da sua utilização em VM. Para os VMs do Windows, o medidor de utilização é dividido em dois metros separados. Há um medidor de cálculo, que é o mesmo que o medidor Linux, e um medidor IP do Windows. As despesas que vê quando faz a compra são apenas para os custos de cálculo. Os encargos não incluem os custos do software do Windows. Para obter mais informações sobre os custos de software, consulte [os custos de Software não incluídos com Azure Reserved VM Instances](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determinar o tamanho correto da VM antes da compra

Antes de comprar uma reserva, deverá determinar o tamanho do VM de que necessita. As seguintes secções irão ajudá-lo a determinar o tamanho certo do VM.

### <a name="use-reservation-recommendations"></a>Use recomendações de reserva

Pode usar recomendações de reserva para ajudar a determinar as reservas que deve comprar.

- Recomendações de compra e quantidade recomendada são mostradas quando compra uma instância reservada vM no portal Azure.
- O Azure Advisor fornece recomendações de compra para subscrições individuais.  
- Pode utilizar as APIs para obter recomendações de compra tanto para âmbito partilhado como para o âmbito de subscrição único. Para obter mais informações, consulte [APIs de recomendação de compra de instância reservada para clientes empresariais.](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)
- Para os clientes do Enterprise Agreement (EA) e do Microsoft Customer Agreement (MCA), as recomendações de compra para âmbitos de subscrição partilhados e únicos estão disponíveis com o [pacote de conteúdos Azure Consumption Insights Power BI](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Serviços que recebem descontos de reserva de máquina virtual

As suas reservas de máquina virtual podem aplicar-se à utilização de máquina virtual emitida de vários serviços e não apenas para implementações de máquina virtual. Os recursos que recebem descontos de reserva mudam dependendo da definição de flexibilidade de tamanho de instância.

#### <a name="instance-size-flexibility-setting"></a>Definição de flexibilidade de tamanho de instância

A definição de flexibilidade de tamanho de instância determina quais serviços recebem os descontos da instância reservada.

Quer a definição esteja ativada ou desativada, os descontos de reserva serão aplicados automaticamente a qualquer utilização de máquina virtual quando o *ConsumedService* for `Microsoft.Compute`. Portanto, verifique os dados de utilização para o valor *ConsumedService*. Alguns exemplos incluem:

- Máquinas virtuais
- Conjuntos de dimensionamento de máquinas virtuais
- Serviço de contentores
- Implementações do Azure Batch (no modo de subscrições do utilizador)
- Serviço de Kubernetes do Azure (AKS)
- Service Fabric

Quando a definição está ativada, os descontos de reserva aplicam-se automaticamente à utilização correspondente da máquina virtual quando o *ConsumedService* é qualquer um dos seguintes itens:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Verifique o valor de *ConsumedService* nos seus dados de utilização para determinar se a utilização está qualificada para descontos de reserva.

Para obter mais informações sobre a flexibilidade de tamanho de instância, veja [Flexibilidade de tamanho de máquina virtual com Instâncias de Máquina Virtual Reservadas](reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analise as suas informações de utilização

Analise as suas informações de utilização para ajudar a determinar quais as reservas que deve adquirir. Os dados de utilização estão disponíveis no ficheiro de utilização e nas APIs. Use-os juntos para determinar que reserva comprar. Verifique se há casos de VM que têm uma elevada utilização diariamente para determinar a quantidade de reservas a comprar. Evite a `Meter` subcategoria e `Product` os campos nos dados de utilização. Não distinguem os tamanhos VM que usam armazenamento premium. Se utilizar estes campos para determinar o tamanho VM para compra de reservas, poderá comprar o tamanho errado. Então não terá o desconto de reserva que espera. Em vez disso, consulte o `AdditionalInfo` campo no seu ficheiro de utilização ou a API de utilização para determinar o tamanho correto do VM.

O seu ficheiro de utilização mostra os seus encargos por período de faturação e uso diário. Para obter informações sobre o download do seu ficheiro de utilização, consulte [Ver e descarregue o seu uso e custos Azure](../cost-management-billing/understand/download-azure-daily-usage.md). Em seguida, utilizando as informações do ficheiro de utilização, pode [determinar qual a reserva a comprar.](../cost-management-billing/reservations/determine-reservation-purchase.md)

### <a name="purchase-restriction-considerations"></a>Considerações de restrição de compra

As instâncias VM reservadas estão disponíveis para a maioria dos tamanhos VM, com algumas exceções. Os descontos de reserva não se aplicam aos seguintes VMs:

- **Série VM** - Série A, Série Av2 ou série G.

- **Pré-visualização ou Promo VMs** - Qualquer série de VM ou tamanho que esteja em pré-visualização ou utilize o medidor promocional.

- **Nuvens** - As reservas não estão disponíveis para compra nas regiões da Alemanha ou da China.

- **Contingente insuficiente** - Uma reserva que seja atribuída a uma única subscrição deve ter a quota vCPU disponível na subscrição do novo RI. Por exemplo, se a subscrição-alvo tiver um limite de quota de 10 vCPUs para série D, então não pode comprar uma reserva por 11 Standard_D1 casos. O controlo de quotas para reservas inclui os VMs já implantados na subscrição. Por exemplo, se a subscrição tiver uma quota de 10 vCPUs para séries D e tiver duas standard_D1 instâncias implementadas, então pode comprar uma reserva de 10 standard_D1 casos nesta subscrição. Pode [criar um pedido de aumento de cotação](../azure-portal/supportability/resource-manager-core-quotas-request.md) para resolver este problema.

- **Restrições de capacidade** - Em circunstâncias raras, o Azure limita a compra de novas reservas para subconjuntos de tamanhos VM, devido à baixa capacidade numa região.

## <a name="buy-a-reserved-vm-instance"></a>Comprar uma Instância de VM Reservada

Pode comprar uma instância VM reservada no [portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/monthly-payments-reservations.md).
Estes requisitos aplicam-se à compra de uma instância VM reservada:

- Você deve estar numa função proprietário para pelo menos uma subscrição EA ou uma subscrição com uma taxa de pagamento como você-go.
- Para as assinaturas EA, a opção **Add Reserved Instances** deve ser ativada no [portal EA](https://ea.azure.com/). Em alternativa, se essa definição estiver desativada, terá de ser o Administrador EA da subscrição.
- Para o programa Cloud Solution Provider (CSP), apenas os agentes administrativos ou agentes de vendas podem comprar reservas.

Para comprar uma instância:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os serviços** > **Reservas**.
1. Selecione **Adicionar** para comprar uma nova reserva e, em seguida, clique em **Máquina virtual**.
1. Introduza os campos necessários. Executar instâncias da VM que correspondem aos atributos que seleciona qualificam-no para obter o desconto da reserva. O número real das instâncias da VM que obtêm o desconto depende do âmbito e da quantidade selecionada.

Se tiver um acordo EA, pode utilizar a **opção Adicionar mais** rapidamente para adicionar instâncias adicionais. A opção não está disponível para outros tipos de subscrição.


| Campo      | Descrição|
|------------|--------------|
|Subscrição|A assinatura usada para pagar a reserva. Os custos de reserva são cobrados de acordo com o método de pagamento indicado na subscrição. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Microsoft Customer Agreement ou uma subscrição individual com taxas pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Os custos são deduzidos do saldo de fidelização monetária, se disponível, ou cobrados como utilização excedida. Para uma subscrição com taxas pay-as-you-go, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição.|    
|Âmbito       |O âmbito da reserva pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar: <ul><li>**Âmbito de grupo de recursos único** – aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionado.</li><li>**Âmbito de subscrição individual** – aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.</li><li>**Âmbito partilhado** – aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que estejam no contexto de faturação. Para os clientes da EA, o contexto de faturação é a inscrição. Para subscrições individuais com taxas pay as you go, o âmbito da faturação reside em todas as subscrições elegíveis criadas pelo administrador de conta.</li></ul>|
|Região    |A região de Azure que está coberta pela reserva.|    
|Tamanho da VM     |O tamanho das instâncias VM.|
|Otimizar para     |A flexibilidade do tamanho da instância VM é selecionada por padrão. Clique **em definições avançadas** para alterar o valor de flexibilidade do tamanho da instância para aplicar o desconto de reserva a outros VMs no mesmo [grupo de tamanho VM](reserved-vm-instance-size-flexibility.md). A prioridade de capacidade prioriza a capacidade de datacenter das implementações. Oferece confiança adicional na sua capacidade de lançar as instâncias VM quando precisa delas. A prioridade da capacidade só está disponível quando o âmbito de reserva é uma subscrição única. |
|Termo        |Um ano ou três anos. Há também um prazo de 5 anos disponível apenas para HBv2 VMs.|
|Quantidade    |O número de casos a serem comprados dentro da reserva. A quantidade é o número de casos de VM em execução que podem obter o desconto de faturação. Por exemplo, se estiver a executar 10 VMs Standard_D2 nos EUA Orientais, então especificaria a quantidade como 10 para maximizar o benefício para todos os VMs em execução. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Dados de utilização e utilização de reservas

Os dados de utilização têm um preço efetivo de zero pela utilização que obtém um desconto de reserva. Pode ver qual a instância VM que recebeu o desconto de reserva para cada reserva.

Para obter mais informações sobre como os descontos de reserva aparecem nos dados de utilização, consulte [o uso da reserva Understand Azure para a sua inscrição na Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) se for cliente da EA. Se tiver uma subscrição individual, consulte [o uso da reserva Understand Azure para a sua subscrição Pay-As-You-Go](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Alterar uma reserva após a compra

Pode fazer os seguintes tipos de alterações numa reserva após a compra:

- Atualizar âmbito da reserva
- Flexibilidade do tamanho do exemplo (se aplicável)
- Propriedade

Você também pode dividir uma reserva em pedaços menores e fundir reservas já divididas. Nenhuma das alterações causa uma nova transação comercial ou altera a data de fim da reserva.

Não é possível efetuar diretamente os seguintes tipos de alterações após a compra:

- Uma região de reserva existente
- SKU
- Quantidade
- Duração

No entanto, pode *trocar* uma reserva se quiser fazer alterações.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

- Para saber como gerir uma reserva, veja [Gerir Reservas do Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md).
- Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:
    - [O que são as reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [Gerir Reservas no Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [Compreender como o desconto das reservas é aplicado](../cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [Compreender a utilização de reservas para uma subscrição com tarifas pay as you go](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [Compreender a utilização de reservas na inscrição Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [Custos de software Windows não incluídos nas reservas](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations) (Reservas do Azure no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros)


