---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/21/2019
ms.openlocfilehash: 119ef52bd801f66cd25a844d3a87b1906881c484
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021186"
---
Ao se comprometer com uma instância de VM reservada do Azure, você pode economizar dinheiro. O desconto de reserva é aplicado automaticamente ao número de máquinas virtuais em execução que correspondem ao escopo e aos atributos de reserva. Você não precisa atribuir uma reserva a uma máquina virtual para obter os descontos. Uma compra de instância reservada abrange apenas a parte de computação do uso da VM. Para VMs do Windows, o medidor de uso é dividido em dois medidores separados. Há um medidor de computação, que é o mesmo que o medidor do Linux e um medidor de IP do Windows. Os encargos que você vê quando faz a compra são apenas para os custos de computação. Os encargos não incluem os custos de software do Windows. Para obter mais informações sobre os custos de software, consulte [custos de software não incluídos com instâncias de VM reservadas do Azure](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determinar o tamanho correto da VM antes de comprar

Antes de comprar uma reserva, você deve determinar o tamanho da VM de que precisa. As seções a seguir ajudarão você a determinar o tamanho correto da VM.

### <a name="use-reservation-recommendations"></a>Usar recomendações de reserva

Você pode usar recomendações de reserva para ajudar a determinar as reservas que devem ser compradas.

- As recomendações de compra e a quantidade recomendada são mostradas quando você adquire uma instância reservada de VM no portal do Azure.
- O Azure Advisor fornece recomendações de compra para assinaturas individuais.  
- Você pode usar as APIs para obter recomendações de compra para o escopo compartilhado e o escopo de assinatura única. Para obter mais informações, consulte [APIs de recomendação de compra de instância reservada para clientes empresariais](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Para clientes Enterprise Agreement (EA) e Microsoft Customer Agreement (MCA), as recomendações de compra para escopos de assinatura compartilhada e única estão disponíveis com o [Azure consumption insights pacote de conteúdo do Power bi](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Serviços que recebem descontos de reserva de máquina virtual

As suas reservas de máquina virtual podem aplicar-se à utilização de máquina virtual emitida de vários serviços e não apenas para implementações de máquina virtual. Os recursos que recebem descontos de reserva mudam dependendo da definição de flexibilidade de tamanho de instância.

#### <a name="instance-size-flexibility-setting"></a>Definição de flexibilidade de tamanho de instância

A definição de flexibilidade de tamanho de instância determina quais serviços recebem os descontos da instância reservada.

Quer a definição esteja ativada ou desativada, os descontos de reserva serão aplicados automaticamente a qualquer utilização de máquina virtual quando o *ConsumedService* for `Microsoft.Compute`. Portanto, verifique os dados de utilização para o valor *ConsumedService*. Alguns exemplos incluem:

- Virtual Machines
- Conjuntos de dimensionamento de máquinas virtuais
- Serviço de contentores
- Implementações do Azure Batch (no modo de subscrições do utilizador)
- Serviço Kubernetes do Azure (AKS)
- Service Fabric

Quando a definição está ativada, os descontos de reserva aplicam-se automaticamente à utilização correspondente da máquina virtual quando o *ConsumedService* é qualquer um dos seguintes itens:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Verifique o valor de *ConsumedService* nos seus dados de utilização para determinar se a utilização está qualificada para descontos de reserva.

Para obter mais informações sobre a flexibilidade de tamanho de instância, veja [Flexibilidade de tamanho de máquina virtual com Instâncias de Máquina Virtual Reservadas](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analisar suas informações de uso
Analise suas informações de uso para ajudar a determinar quais reservas devem ser compradas.

Os dados de uso estão disponíveis no arquivo de uso e nas APIs. Use-os juntos para determinar qual reserva deve ser comprada. Verifique as instâncias de VM que têm alto uso diariamente para determinar a quantidade de reservas a serem compradas.

Evite a subcategoria `Meter` e `Product` campos nos dados de uso. Eles não fazem distinção entre os tamanhos de VM que usam o armazenamento Premium. Se você usar esses campos para determinar o tamanho da VM para a compra de reserva, você poderá comprar o tamanho errado. Em seguida, você não obterá o desconto de reserva esperado. Em vez disso, consulte o campo `AdditionalInfo` em seu arquivo de uso ou API de uso para determinar o tamanho correto da VM.

### <a name="purchase-restriction-considerations"></a>Considerações sobre a restrição de compra

As instâncias de VM reservadas estão disponíveis para a maioria dos tamanhos de VM com algumas exceções. Os descontos de reserva não se aplicam às seguintes VMs:

- **Série de VMs** -séries a, Av2 ou G.

- **VMs de visualização ou promoção** -qualquer série de VM ou tamanho que esteja na visualização ou use o medidor promocional.

- **Nuvens** – as reservas não estão disponíveis para compra nas regiões da Alemanha ou da China.

- **Cota insuficiente** – uma reserva com escopo para uma única assinatura deve ter a cota vCPU disponível na assinatura para a nova ri. Por exemplo, se a assinatura de destino tiver um limite de cota de 10 vCPUs para a série D, você não poderá comprar uma reserva para 11 instâncias de Standard_D1. A verificação de cota para reservas inclui as VMs já implantadas na assinatura. Por exemplo, se a assinatura tiver uma cota de 10 vCPUs para a série D e tiver duas instâncias de standard_D1 implantadas, você poderá comprar uma reserva para 10 instâncias de standard_D1 nesta assinatura. Você pode [criar uma solicitação de aumento de cotação](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) para resolver esse problema.

- **Restrições de capacidade** -em raras circunstâncias, o Azure limita a compra de novas reservas para o subconjunto de tamanhos de VM, devido à baixa capacidade em uma região.

## <a name="buy-a-reserved-vm-instance"></a>Comprar uma instância de VM reservada

Você pode comprar uma instância de VM reservada no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Pague pela reserva [antecipadamente ou com pagamentos mensais](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).
Esses requisitos se aplicam à compra de uma instância de VM reservada:

- Você deve estar em uma função de proprietário para pelo menos uma assinatura de EA ou uma assinatura com uma taxa pré-paga.
- Para assinaturas EA, a opção **adicionar instâncias reservadas** deve ser habilitada no [portal de ea](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você deverá ser um administrador de EA para a assinatura.
- Para o programa CSP (provedor de soluções na nuvem), somente os agentes de administração ou os agentes de vendas podem comprar reservas.

Para comprar uma instância:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione **Todos os serviços** > **Reservas**.
1. Selecione **Adicionar** para comprar uma nova reserva e, em seguida, clique em **máquina virtual**.
1. Introduza os campos necessários. Executar instâncias da VM que correspondem aos atributos que seleciona qualificam-no para obter o desconto da reserva. O número real das instâncias da VM que obtêm o desconto depende do âmbito e da quantidade selecionada.

Se você tiver um contrato EA, poderá usar a **opção Adicionar mais** para adicionar instâncias adicionais rapidamente. A opção não está disponível para outros tipos de assinatura.


| Campo      | Descrição|
|------------|--------------|
|Subscrição|A assinatura usada para pagar pela reserva. O método de pagamento na assinatura é cobrado pelos custos da reserva. O tipo de assinatura deve ser um Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou o contrato de cliente da Microsoft ou uma assinatura individual com tarifas pagas conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Os encargos são deduzidos do saldo de compromisso monetário, se disponível ou cobrados como excedentes. Para uma assinatura com tarifas pagas conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.|    
|Âmbito       |O escopo da reserva pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado). Se você selecionar: <ul><li>**Âmbito de grupo de recursos único** – aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionado.</li><li>**Âmbito de subscrição individual** – aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.</li><li>**Âmbito partilhado** – aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que estejam no contexto de faturação. Para clientes do EA, o contexto de cobrança é o registro. Para subscrições individuais com taxas pay as you go, o âmbito da faturação reside em todas as subscrições elegíveis criadas pelo administrador de conta.</li></ul>|
|Região    |A região do Azure que é coberta pela reserva.|    
|Tamanho de VM     |O tamanho das instâncias de VM.|
|Otimizar para     |A flexibilidade do tamanho da instância VM é selecionada por padrão. Clique em **Configurações avançadas** para alterar o valor de flexibilidade do tamanho da instância para aplicar o desconto de reserva a outras VMs no mesmo [grupo de tamanhos de VM](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). A prioridade de capacidade prioriza a capacidade de datacenter das implementações. Ele oferece confiança adicional em sua capacidade de iniciar as instâncias de VM quando você precisar delas. A prioridade de capacidade só estará disponível quando o escopo de reserva for uma única assinatura. |
|Duração        |Um ano ou três anos.|
|Quantidade    |O número de instâncias que estão sendo adquiridas dentro da reserva. A quantidade é o número de instâncias de VM em execução que podem obter o desconto de cobrança. Por exemplo, se você estiver executando 10 VMs Standard_D2 no leste dos EUA, você deve especificar Quantity como 10 para maximizar o benefício de todas as VMs em execução. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Dados de uso e utilização de reserva

Os dados de utilização têm um preço efetivo de zero pela utilização que obtém um desconto de reserva. Você pode ver qual instância de VM recebeu o desconto de reserva para cada reserva.

Para obter mais informações sobre como os descontos de reserva aparecem nos dados de uso, consulte [entender o uso de reserva do Azure para o registro de sua empresa](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) se você for um cliente do ea. Se você tiver uma assinatura individual, consulte [entender o uso de reserva do Azure para sua assinatura paga conforme o uso](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Alterar uma reserva após a compra

Pode fazer os seguintes tipos de alterações numa reserva após a compra:

- Atualizar âmbito da reserva
- Flexibilidade de tamanho de instância (se aplicável)
- Propriedade

Você também pode dividir uma reserva em partes menores e mesclar reservas já divididas. Nenhuma das alterações causa uma nova transação comercial ou alterar a data de término da reserva.

Você não pode fazer os seguintes tipos de alterações após a compra, diretamente:

- Uma região de reserva existente
- SKU
- Quantidade
- Duração

No entanto, você pode *trocar* uma reserva se desejar fazer alterações.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

- Para saber como gerir uma reserva, veja [Gerir Reservas do Azure](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md).
- Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:
    - [O que são as reservas do Azure?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [Gerir Reservas no Azure](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [Compreender como o desconto de reserva é aplicado](../articles/cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [Compreender a utilização de reservas para uma subscrição com tarifas pay as you go](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [Compreender a utilização de reservas na inscrição Enterprise](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [Custos de software Windows não incluídos nas reservas](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [Azure Reservations no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros](https://docs.microsoft.com/partner-center/azure-reservations)
