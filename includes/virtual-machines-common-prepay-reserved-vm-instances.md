---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/21/2019
ms.openlocfilehash: f583796fc353852ef3898e28fa96524e08cfb4ad
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414636"
---
When you commit to an Azure reserved VM instance you can save money. The reservation discount is applied automatically to the number of running virtual machines that match the reservation scope and attributes. You don't need to assign a reservation to a virtual machine to get the discounts. A reserved instance purchase covers only the compute part of your VM usage. For Windows VMs, the usage meter is split into two separate meters. There's a compute meter, which is same as the Linux meter, and a Windows IP meter. The charges that you see when you make the purchase are only for the compute costs. Charges don't include Windows software costs. For more information about software costs, see [Software costs not included with Azure Reserved VM Instances](../articles/billing/billing-reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determine the right VM size before you buy

Before you buy a reservation, you should determine the size of the VM that you need. The following sections will help you determine the right VM size.

### <a name="use-reservation-recommendations"></a>Use reservation recommendations

You can use reservation recommendations to help determine the reservations you should purchase.

- Purchase recommendations and recommended quantity are show when you purchase a VM reserved instance in the Azure portal.
- Azure Advisor provides purchase recommendations for individual subscriptions.  
- You can use the APIs to get purchase recommendations for both shared scope and single subscription scope. For more information, see [Reserved instance purchase recommendation APIs for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- For Enterprise Agreement (EA) and Microsoft Customer Agreement (MCA) customers, purchase recommendations for shared and single subscription scopes are available with the [Azure Consumption Insights Power BI content pack](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Serviços que recebem descontos de reserva de máquina virtual

As suas reservas de máquina virtual podem aplicar-se à utilização de máquina virtual emitida de vários serviços e não apenas para implementações de máquina virtual. Os recursos que recebem descontos de reserva mudam dependendo da definição de flexibilidade de tamanho de instância.

#### <a name="instance-size-flexibility-setting"></a>Definição de flexibilidade de tamanho de instância

A definição de flexibilidade de tamanho de instância determina quais serviços recebem os descontos da instância reservada.

Quer a definição esteja ativada ou desativada, os descontos de reserva serão aplicados automaticamente a qualquer utilização de máquina virtual quando o *ConsumedService* for `Microsoft.Compute`. Portanto, verifique os dados de utilização para o valor *ConsumedService*. Alguns exemplos incluem:

- Máquinas virtuais
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

### <a name="analyze-your-usage-information"></a>Analyze your usage information
Analyze your usage information to help determine which reservations you should purchase.

Usage data is available in the usage file and APIs. Use them together to determine which reservation to purchase. Check for VM instances that have high usage on daily basis to determine the quantity of reservations to purchase.

Avoid the `Meter` subcategory and `Product` fields in usage data. They don't distinguish between VM sizes that use premium storage. If you use these fields to determine the VM size for reservation purchase, you may buy the wrong size. Then you won't get the reservation discount you expect. Instead, refer to the `AdditionalInfo` field in your usage file or usage API to determine the correct VM size.

### <a name="purchase-restriction-considerations"></a>Purchase restriction considerations

Reserved VM Instances are available for most VM sizes with some exceptions. Reservation discounts don't apply for the following VMs:

- **VM series** - A-series, Av2-series, or G-series.

- **Preview or Promo VMs** - Any VM-series or size that is in preview or uses promotional meter.

- **Clouds** - Reservations aren't available for purchase in Germany or China regions.

- **Insufficient quota** - A reservation that is scoped to a single subscription must have vCPU quota available in the subscription for the new RI. For example, if the target subscription has a quota limit of 10 vCPUs for D-Series, then you can't buy a reservation for 11 Standard_D1 instances. The quota check for reservations includes the VMs already deployed in the subscription. For example, if the subscription has a quota of 10 vCPUs for D-Series and has two standard_D1 instances deployed, then you can buy a reservation for 10 standard_D1 instances in this subscription. You can [create quote increase request](../articles/azure-supportability/resource-manager-core-quotas-request.md) to resolve this issue.

- **Capacity restrictions** - In rare circumstances, Azure limits the purchase of new reservations for subset of VM sizes, because of low capacity in a region.

## <a name="buy-a-reserved-vm-instance"></a>Buy a Reserved VM Instance

You can buy a reserved VM instance in the [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Pague pela reserva [antecipadamente ou com pagamentos mensais](../articles/billing/billing-monthly-payments-reservations.md).
These requirements apply to buying a reserved VM instance:

- You must be in an Owner role for at least one EA subscription or a subscription with a pay-as-you-go rate.
- For EA subscriptions, the **Add Reserved Instances** option must be enabled in the [EA portal](https://ea.azure.com/). Or, if that setting is disabled, you must be an EA Admin for the subscription.
- For the Cloud Solution Provider (CSP) program, only the admin agents or sales agents can buy reservations.

To buy an instance:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os serviços** > **Reservas**.
1. Select **Add** to purchase a new reservation and then click **Virtual machine**.
1. Enter required fields. Running VM instances that match the attributes you select qualify to get the reservation discount. The actual number of your VM instances that get the discount depend on the scope and quantity selected.

If you have an EA agreement, you can use the **Add more option** to quickly add additional instances. The option isn't available for other subscription types.


| Campo      | Descrição|
|------------|--------------|
|Subscrição|The subscription used to pay for the reservation. The payment method on the subscription is charged the costs for the reservation. The subscription type must be an enterprise agreement (offer numbers: MS-AZR-0017P or MS-AZR-0148P) or Microsoft Customer Agreement or an individual subscription with pay-as-you-go rates (offer numbers: MS-AZR-0003P or MS-AZR-0023P). The charges are deducted from the monetary commitment balance, if available, or charged as overage. For a subscription with pay-as-you-go rates, the charges are billed to the credit card or invoice payment method on the subscription.|    
|Âmbito       |The reservation’s scope can cover one subscription or multiple subscriptions (shared scope). If you select: <ul><li>**Âmbito de grupo de recursos único** – aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionado.</li><li>**Âmbito de subscrição individual** – aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.</li><li>**Âmbito partilhado** – aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que estejam no contexto de faturação. For EA customers, the billing context is the enrollment. Para subscrições individuais com taxas pay as you go, o âmbito da faturação reside em todas as subscrições elegíveis criadas pelo administrador de conta.</li></ul>|
|Região    |The Azure region that’s covered by the reservation.|    
|Tamanho de VM     |The size of the VM instances.|
|Optimize for     |VM instance size flexibility is selected by default. Click **Advanced settings** to change the instance size flexibility value to apply the reservation discount to other VMs in the same [VM size group](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). A prioridade de capacidade prioriza a capacidade de datacenter das implementações. It offers additional confidence in your ability to launch the VM instances when you need them. Capacity priority is only available when the reservation scope is single subscription. |
|Termo        |One year or three years.|
|Quantidade    |The number of instances being purchased within the reservation. The quantity is the number of running VM instances that can get the billing discount. For example, if you are running 10 Standard_D2 VMs in the East US, then you would specify quantity as 10 to maximize the benefit for all running VMs. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Usage data and reservation utilization

Os dados de utilização têm um preço efetivo de zero pela utilização que obtém um desconto de reserva. You can see which VM instance received the reservation discount for each reservation.

For more information about how reservation discounts appear in usage data, see [Understand Azure reservation usage for your Enterprise enrollment](../articles/billing/billing-understand-reserved-instance-usage-ea.md) if you are an EA customer. If you have an individual subscription, see [Understand Azure reservation usage for your Pay-As-You-Go subscription](../articles/billing/billing-understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Change a reservation after purchase

Pode fazer os seguintes tipos de alterações numa reserva após a compra:

- Atualizar âmbito da reserva
- Instance size flexibility (if applicable)
- Ownership

You can also split a reservation into smaller chunks and merge already split reservations. None of the changes cause a new commercial transaction or change the end date of the reservation.

You can't make the following types of changes after purchase, directly:

- An existing reservation’s region
- SKU
- Quantidade
- Duração

However, you can *exchange* a reservation if you want to make changes.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

- Para saber como gerir uma reserva, veja [Gerir Reservas do Azure](../articles/billing/billing-manage-reserved-vm-instance.md).
- Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:
    - [O que são as reservas do Azure?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Gerir Reservas no Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Compreender como o desconto das reservas é aplicado](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Compreender a utilização de reservas para uma subscrição com tarifas pay as you go](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Compreender a utilização de reservas na inscrição Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Custos de software Windows não incluídos nas reservas](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros](https://docs.microsoft.com/partner-center/azure-reservations)
