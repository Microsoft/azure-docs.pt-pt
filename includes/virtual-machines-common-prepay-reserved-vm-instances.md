---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/27/2020
ms.openlocfilehash: d41affc55134ad34c325c12ab4a14f4013c58f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371652"
---
Quando se compromete com um caso De VM reservado ao Azure, pode economizar dinheiro. O desconto de reserva é aplicado automaticamente ao número de máquinas virtuais em execução que correspondem ao âmbito de reserva e atributos. Não precisa atribuir uma reserva a uma máquina virtual para obter os descontos. Uma compra de instância reservada cobre apenas a parte computada do seu uso VM. Para os VMs do Windows, o medidor de utilização é dividido em dois metros separados. Há um medidor de cálculo, que é o mesmo que o medidor linux, e um medidor IP do Windows. As acusações que vê quando faz a compra são apenas para os custos do cálculo. Os encargos não incluem os custos do software do Windows. Para obter mais informações sobre os custos do software, consulte os custos de Software não incluídos com as [Instâncias VM reservadas do Azure.](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determinar o tamanho correto da VM antes da compra

Antes de comprar uma reserva, deve determinar o tamanho do VM de que necessita. As seguintes secções irão ajudá-lo a determinar o tamanho vm certo.

### <a name="use-reservation-recommendations"></a>Utilize recomendações de reserva

Pode usar recomendações de reserva para ajudar a determinar as reservas que deve comprar.

- As recomendações de compra e a quantidade recomendada são mostradas quando compra uma instância vm reservada no portal Azure.
- O Azure Advisor fornece recomendações de compra para subscrições individuais.  
- Pode utilizar as APIs para obter recomendações de compra tanto para o âmbito partilhado como para o âmbito de subscrição único. Para mais informações, consulte a recomendação de [compra de instância seletiva para clientes empresariais.](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)
- Para os clientes do Acordo Empresarial (EA) e microsoft Customer Agreement (MCA), as recomendações de compra para âmbitos de subscrição partilhados e únicos estão disponíveis com o pacote de [conteúdos Azure Consumption Insights Power BI](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Serviços que recebem descontos de reserva de máquina virtual

As suas reservas de máquina virtual podem aplicar-se à utilização de máquina virtual emitida de vários serviços e não apenas para implementações de máquina virtual. Os recursos que recebem descontos de reserva mudam dependendo da definição de flexibilidade de tamanho de instância.

#### <a name="instance-size-flexibility-setting"></a>Definição de flexibilidade de tamanho de instância

A definição de flexibilidade de tamanho de instância determina quais serviços recebem os descontos da instância reservada.

Quer a definição esteja ativada ou desativada, os descontos de reserva serão aplicados automaticamente a qualquer utilização de máquina virtual quando o *ConsumedService* for `Microsoft.Compute`. Portanto, verifique os dados de utilização para o valor *ConsumedService*. Alguns exemplos incluem:

- Virtual Machines
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

Para obter mais informações sobre a flexibilidade de tamanho de instância, veja [Flexibilidade de tamanho de máquina virtual com Instâncias de Máquina Virtual Reservadas](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analise as suas informações de utilização

Analise as suas informações de utilização para ajudar a determinar quais as reservas que deve comprar. Os dados de utilização estão disponíveis no ficheiro de utilização e nas APIs. Use-os juntos para determinar que reserva comprar. Consulte os casos de VM que tenham uma utilização elevada diariamente para determinar a quantidade de reservas para comprar. Evite `Meter` a subcategoria e `Product` os campos nos dados de utilização. Não distinguem os tamanhos vm que usam armazenamento premium. Se utilizar estes campos para determinar o tamanho do VM para a compra de reservas, poderá comprar o tamanho errado. Então não terá o desconto de reserva que espera. Em vez disso, consulte o `AdditionalInfo` campo no seu ficheiro de utilização ou a API de utilização para determinar o tamanho vm correto.

O seu ficheiro de utilização mostra as suas despesas através do período de faturação e do uso diário. Para obter informações sobre o download do seu ficheiro de utilização, consulte [o View e descarregue o seu uso e encargos do Azure.](../articles/cost-management-billing/understand/download-azure-daily-usage.md) Em seguida, utilizando as informações do ficheiro de utilização, pode [determinar qual reserva comprar.](../articles/cost-management-billing/reservations/determine-reservation-purchase.md)

### <a name="purchase-restriction-considerations"></a>Considerações de restrição de compra

Os casos de VM reservados estão disponíveis para a maioria dos tamanhos vm com algumas exceções. Os descontos de reserva não se aplicam aos seguintes VMs:

- **Série VM** - Série A, série Av2 ou série G.

- **Pré-visualização ou VMs Promocionais** - Qualquer série vm ou tamanho que esteja na pré-visualização ou utilize medidor promocional.

- **Clouds** - As reservas não estão disponíveis para compra nas regiões da Alemanha ou da China.

- **Quota insuficiente** - Uma reserva que seja orientada para uma única subscrição deve ter quota vCPU disponível na subscrição do novo RI. Por exemplo, se a subscrição-alvo tiver um limite de quota de 10 vCPUs para série D, então não pode comprar uma reserva por 11 Standard_D1 instâncias. O controlo de quotas para reservas inclui os VMs já implantados na subscrição. Por exemplo, se a subscrição tiver uma quota de 10 vCPUs para série D e tiver dois standard_D1 instâncias implementadas, então pode comprar uma reserva para 10 standard_D1 instâncias nesta subscrição. Pode [criar um pedido](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) de aumento de cotação para resolver este problema.

- **Restrições** de capacidade - Em circunstâncias raras, o Azure limita a compra de novas reservas para o subconjunto de tamanhos VM, devido à baixa capacidade numa região.

## <a name="buy-a-reserved-vm-instance"></a>Comprar uma Instância de VM Reservada

Pode comprar uma instância VM reservada no [portal Azure.](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D) Pague pela reserva [antecipadamente ou com pagamentos mensais](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).
Estes requisitos aplicam-se à compra de uma instância VM reservada:

- Você deve estar em uma função de Proprietário para pelo menos uma subscrição EA ou uma subscrição com uma taxa pay-as-you-go.
- Para as assinaturas EA, a opção **Adicionar Instâncias Reservadas** deve ser ativada no [portal EA](https://ea.azure.com/). Em alternativa, se essa definição estiver desativada, terá de ser o Administrador EA da subscrição.
- Para o programa Cloud Solution Provider (CSP), apenas os agentes de administração ou agentes de vendas podem comprar reservas.

Para comprar uma instância:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione todas as reservas **de serviços** > **Reservations**.
1. Selecione **Adicionar** para comprar uma nova reserva e, em seguida, clicar na **máquina Virtual**.
1. Introduza os campos necessários. Executar instâncias da VM que correspondem aos atributos que seleciona qualificam-no para obter o desconto da reserva. O número real das instâncias da VM que obtêm o desconto depende do âmbito e da quantidade selecionada.

Se tiver um acordo eA, pode utilizar a **opção Adicionar mais** para adicionar rapidamente instâncias adicionais. A opção não está disponível para outros tipos de subscrição.


| Campo      | Descrição|
|------------|--------------|
|Subscrição|A subscrição costumava pagar a reserva. O método de pagamento da subscrição é cobrado os custos da reserva. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Contrato de Cliente da Microsoft ou uma subscrição individual com taxas de pagamento (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Os encargos são deduzidos do saldo do compromisso monetário, se disponível, ou cobrados como excesso. Para uma subscrição com taxas de pagamento, os encargos são cobrados para o cartão de crédito ou método de pagamento de fatura na subscrição.|    
|Âmbito       |O âmbito da reserva pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar: <ul><li>**Âmbito de grupo de recursos único** – aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionado.</li><li>**Âmbito de subscrição individual** – aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.</li><li>**Âmbito partilhado** – aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que estejam no contexto de faturação. Para os clientes da EA, o contexto de faturação é a inscrição. Para subscrições individuais com taxas pay as you go, o âmbito da faturação reside em todas as subscrições elegíveis criadas pelo administrador de conta.</li></ul>|
|Região    |A região de Azure que está coberta pela reserva.|    
|Tamanho da VM     |O tamanho dos casos vm.|
|Otimizar para     |A flexibilidade do tamanho da instância VM é selecionada por padrão. Clique em **configurações avançadas** para alterar o valor de flexibilidade do tamanho da instância para aplicar o desconto de reserva a outros VMs no mesmo grupo de [tamanho VM](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). A prioridade de capacidade prioriza a capacidade de datacenter das implementações. Oferece confiança adicional na sua capacidade de lançar os casos vm quando precisa deles. A prioridade de capacidade só está disponível quando o âmbito da reserva for uma subscrição única. |
|Termo        |Um ano ou três anos.|
|Quantidade    |O número de casos adquiridos dentro da reserva. A quantidade é o número de casos vm em execução que podem obter o desconto de faturação. Por exemplo, se estiver a executar 10 Standard_D2 VMs nos EUA Orientais, então especificaria a quantidade como 10 para maximizar o benefício para todos os VMs em execução. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Dados de utilização e utilização de reservas

Os dados de utilização têm um preço efetivo de zero pela utilização que obtém um desconto de reserva. Pode ver qual a instância vm que recebeu o desconto de reserva para cada reserva.

Para obter mais informações sobre como os descontos de reserva aparecem nos dados de utilização, consulte o uso da reserva Do Minéeça Para a [sua inscrição](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) na Enterprise se for cliente DaE. Se tiver uma subscrição individual, consulte o uso da [reserva Do Understand Azure para a sua subscrição Pay-As-You-Go](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Alterar uma reserva após a compra

Pode fazer os seguintes tipos de alterações numa reserva após a compra:

- Atualizar âmbito da reserva
- Flexibilidade de tamanho de instância (se aplicável)
- Propriedade

Você também pode dividir uma reserva em pedaços menores e fundir reservas já divididas. Nenhuma das alterações provoca uma nova transação comercial ou altera a data final da reserva.

Não é possível efazer os seguintes tipos de alterações após a compra, diretamente:

- Uma região de reserva existente
- SKU
- Quantidade
- Duração

No entanto, pode *trocar* uma reserva se quiser fazer alterações.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)de apoio.

## <a name="next-steps"></a>Passos seguintes

- Para saber como gerir uma reserva, veja [Gerir Reservas do Azure](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md).
- Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:
    - [O que são Reservas Azure?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [Gerir Reservas no Azure](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [Compreender como o desconto de reserva é aplicado](../articles/cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [Compreender a utilização de reservas para uma subscrição com tarifas pay as you go](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [Compreender a utilização de reservas na inscrição Enterprise](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [Custos de software Windows não incluídos nas reservas](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [Azure Reservations no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros](https://docs.microsoft.com/partner-center/azure-reservations)
