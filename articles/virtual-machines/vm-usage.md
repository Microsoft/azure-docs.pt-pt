---
title: Compreender o uso da máquina virtual Azure
description: Compreender detalhes de utilização de máquinas virtuais
services: virtual-machines
documentationcenter: ''
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines-linux
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 07/28/2020
ms.openlocfilehash: d43f94d3555a660d6b7c8f755eebfec253d31dc2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89322987"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Compreender o uso da máquina virtual Azure
Ao analisar os seus dados de utilização do Azure, podem obter-se informações de consumo poderosas – insights que podem permitir uma melhor gestão e alocação de custos em toda a sua organização. Este documento fornece um mergulho profundo nos detalhes do consumo do Azure Compute. Para obter mais detalhes sobre o uso geral do Azure, navegue para [compreender a sua conta](../cost-management-billing/understand/review-individual-bill.md).

## <a name="download-your-usage-details"></a>Transferir os detalhes de utilização
Para começar, [descarregue os seus dados de utilização](../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). A tabela abaixo fornece a definição e os valores de exemplo de utilização das Máquinas Virtuais implantadas através do Gestor de Recursos Azure. Este documento não contém informações detalhadas para VMs implementados através do nosso modelo clássico.


| Campo | Significado | Valores de exemplo | 
|---|---|---|
| Data de Utilização | A data em que o recurso foi usado | `11/23/2017` |
| ID do Medidor | Identifica o serviço de alto nível para o qual este uso pertence| `Virtual Machines`|
| Subcategoria do Medidor | O identificador do medidor faturado. <br><br> Para a utilização da Hora de Cálculo, existe um medidor para cada Tamanho VM + OS (Windows, Não-Windows) + Região. <br><br> Para a utilização de software Premium, há um medidor para cada tipo de software. A maioria das imagens de software premium têm medidores diferentes para cada tamanho do núcleo. Para mais informações, visite a [Página de Preços do Compute](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>| `2005544f-659d-49c9-9094-8e0aea1be3a5`|
| Nome do Medidor| Isto é específico para cada serviço em Azure. Para o cálculo, é sempre "Compute Hours".| `Compute Hours`|
| Região do Medidor| Identifica a localização do datacenter para determinados serviços cujo preço é definido com base na localização do datacenter.|  `JA East`|
| Unidade| Identifica a unidade em que o serviço é cobrado. Os recursos de cálculo são faturados por hora.| `Hours`|
| Consumido| A quantidade do recurso que foi consumido para aquele dia. Para o Compute, cobramos por cada minuto que o VM correu por uma hora (até 6 decimais de precisão).| `1, 0.5`|
| Localização do Recurso  | Identifica o datacenter onde o recurso está em execução.| `JA East`|
| Serviço Consumido | O serviço de plataforma Azure que usou.| `Microsoft.Compute`|
| Grupo de Recursos | O grupo de recursos no qual o recurso implementado está em execução. Para mais informações, consulte [a visão geral do Azure Resource Manager.](../azure-resource-manager/management/overview.md)|`MyRG`|
| ID da Instância | O identificador do recurso. O identificador contém o nome que especificou para o recurso quando o criou. Para os VMs, o ID de exemplo conterá o SubscriptionId, o ResourceGroupName e o VMName (ou nome definido em escala para utilização definida na escala).| `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1`<br><br>ou<br><br>`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1`|
| Etiquetas| Marque o que atribuir ao recurso. Utilize etiquetas para agrupar os registos de faturação. Saiba como marcar as suas Máquinas Virtuais utilizando o [CLI](./linux/tag.md) ou [o PowerShell](./windows/tag.md) Isto está disponível apenas para VMs do Gestor de Recursos.| `{"myDepartment":"RD","myUser":"myName"}`|
| Informações Adicionais | Metadados específicos do serviço. Para os VMs, preenchemos os seguintes dados no campo de informações adicionais: <br><br> Imagem tipo- imagem específica que correu. Encontre a lista completa de cordas suportadas abaixo em Tipos de Imagem.<br><br> Tipo de serviço: o tamanho que implementou.<br><br> VMName: nome do seu VM. Este campo só é preenchido para VMs definidos em escala. Se precisar do seu VM Name para vMs definidos em escala, pode encontrá-lo na cadeia de identificação de instância acima.<br><br> UseType: Isto especifica o tipo de utilização que isto representa.<br><br> ComputeHR é o uso da Hora de Cálculo para o VM subjacente, como Standard_D1_v2.<br><br> ComputeHR_SW é a taxa de software premium se o VM estiver a usar software premium, como o Microsoft R Server. | Máquinas Virtuais<br>`{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}`<br><br>Conjuntos de Dimensionamento de Máquinas Virtuais<br> `{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}`<br><br>Premium Software<br> `{"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"}` |

## <a name="image-type"></a>Tipo de imagem
Para algumas imagens na galeria Azure, o tipo de imagem é povoado no campo Informação Adicional. Isto permite que os utilizadores compreendam e rastreiem o que implementaram na sua Máquina Virtual. Os seguintes valores que são povoados neste campo com base na imagem que implementou:
- BitRock 
- FreeBSD canónico 
- Lógica Aberta 
- Oracle 
- SLES para SAP 
- Pré-visualização do SQL Server 14 no Windows Server 2012 R2 Preview 
- SUSE
- SUSE Premium
- StorSimple Cloud Appliance 
- Red Hat
- Chapéu Vermelho para Aplicações Empresariais SAP     
- Chapéu Vermelho para SAP HANA 
- Byol cliente do Windows 
- Byol do servidor do Windows 
- Pré-visualização do servidor do Windows 

## <a name="service-type"></a>Tipo de Serviço
O campo de tipo de serviço no campo Informações Adicionais corresponde ao tamanho exato do VM que implementou. Os VMs de armazenamento premium (baseado em SSD) e VMs de armazenamento não premium (baseado em HDD) têm o mesmo preço. Se implementar um tamanho baseado em SSD, como \_ o Standard DS2 \_ v2, vê o tamanho não SSD ( `Standard\_D2\_v2 VM` ) na coluna Sub-Category meter e no tamanho SSD ( `Standard\_DS2\_v2` ) no campo Informação Adicional.

## <a name="region-names"></a>Nomes da região
O nome da região preenchido no campo de Localização de Recursos nos detalhes de utilização varia do nome da região utilizado no Gestor de Recursos Azure. Aqui está um mapeamento entre os valores da região:

| **Nome da região do gestor de recursos** | **Localização de recursos em detalhes de utilização** |
|---|---|
| australiaeast |AU Leste|
| australiasoutheast | AU Sudeste|
| brazilsouth | Sul BR|
| Centro do Canadá | CA Central|
| CanadáEast | CA Leste|
| CentralIndia | IN Central|
| centralus | E.U.A. Central|
| chinaeast | Leste da China|
| chinanorth | Norte da China|
| eastasia | Ásia Leste|
| eastus | E.U.A. Leste|
| eastus2 | E.U.A. Leste 2|
| AlemanhaCentral | DE Central|
| AlemanhaNortheast | DE Nordeste|
| japaneast | JA Leste|
| japanwest | JA Oeste|
| KoreaCentral | KR do Sul Central|
| Coreias | Sul KR do Sul|
| northcentralus | E.U.A. Centro-Norte|
| northeurope | Europa do Norte|
| southcentralus | E.U.A. Centro-Sul|
| southeastasia | Sudeste Asiático|
| SouthIndia | IN Sul|
| UKNorth | Norte dos EUA|
| uksouth | Sul do Reino Unido|
| UKSouth2 | Sul do Reino Unido 2|
| ukwest | Oeste do Reino Unido|
| USDodCentral | US DoD Centro|
| USDodEast | US DoD - Leste|
| USGovArizona | USGov Arizona|
| usgoviowa | USGov Iowa|
| USGovTexas | USGov Texas|
| usgovvirginia | USGov Virginia|
| E.U.A. Centro-Oeste | E.U.A. Centro-Oeste|
| westeurope | Europa Ocidental|
| WestIndia | IN Oeste|
| westus | E.U.A. Oeste|
| westus2 | E.U.A. Oeste 2|


## <a name="virtual-machine-usage-faq"></a>FaQ de utilização de máquinas virtuais
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Que recursos são cobrados ao implantar um VM?    
Os VMs adquirem custos para o próprio VM, qualquer software premium em execução no VM, a conta de armazenamento\gerido disco associado ao VM, e as transferências de largura de banda de rede do VM.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Como posso saber se um VM está a usar o Azure Hybrid Benefit no CSV de utilização?
Se implementar usando o [Azure Hybrid Benefit,](https://azure.microsoft.com/pricing/hybrid-benefit/)é-lhe cobrada a taxa VM não-Windows, uma vez que está a trazer a sua própria licença para a nuvem. Na sua conta, pode distinguir quais os VMs do Gestor de Recursos que estão a executar o Azure Hybrid Benefit porque têm "Windows \_ Server BYOL" ou "Windows \_ Client BYOL" na coluna ImageType.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Como é que os tipos básicos vs. VM standard são diferenciados no CSV de utilização?
São oferecidos VMs básicos e standard da série A. Se implementar um VM básico, na sub categoria medidor, tem a cadeia "Básico". Se implementar um VM standard da série A, então o tamanho VM aparece como "A1 VM" uma vez que o Standard é o padrão. Para saber mais sobre as diferenças entre o Básico e o Standard, consulte [a Página de Preços](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>O que são tamanhos ExtraSmall, Small, Medium, Large e ExtraLarge?
ExtraSmall - ExtraLarge são os nomes legados da Standard \_ A0 – Standard \_ A4. Nos registos clássicos de utilização de VM, poderá ver esta convenção usada se tiver implementado estes tamanhos.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Qual é a diferença entre a Região do Medidor e a Localização de Recursos?
A Região dos Contadores está associada ao contador. Para alguns serviços Azure que usam um preço para todas as regiões, o campo da Região dos Metros pode estar em branco. No entanto, uma vez que os VMs têm preços dedicados por região para máquinas virtuais, este campo é povoado. Da mesma forma, a localização de recursos para máquinas virtuais é o local onde o VM é implantado. As regiões de Azure em ambos os campos são as mesmas, embora possam ter uma convenção de cordas diferente para o nome da região.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Por que o valor imageType está em branco no campo Informações Adicionais?
O campo ImageType é apenas preenchido para um subconjunto de imagens. Se não implementou uma das imagens acima, o ImageType está em branco.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Por que o VMName está em branco na Informação Adicional?
O VMName só é preenchido no campo informações adicionais para VMs num conjunto de escala. O campo InstanceID contém o nome VM para VMs definidos não escala.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>O que significa ComputeHR no campo UseType na Informação Adicional?
O ComputeHR significa Compute Hour, que representa o evento de utilização para o custo de infraestrutura subjacente. Se o UsageType for ComputeHR \_ SW, o evento de utilização representa a taxa de software premium para o VM.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Como sei se sou cobrado por software premium?
Ao explorar qual a VM Image que melhor se adequa às suas necessidades, não se esqueça de confercer [o Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute) A imagem tem a taxa de plano de software. Se vir "Grátis" pela tarifa, não há custo adicional para o software. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Qual é a diferença entre Microsoft.ClassicCompute e Microsoft.Compute no serviço Consumido?
A Microsoft.ClassicCompute representa recursos clássicos implantados através do Azure Service Manager. Se implementar através do Gestor de Recursos, então o Microsoft.Compute é preenchido no serviço consumido. Saiba mais sobre os [modelos Azure Deployment](../azure-resource-manager/management/deployment-models.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Porque é que o campo InstanceID está em branco para o meu uso da Máquina Virtual?
Se implementar através do modelo de implementação clássico, a cadeia InstanceID não está disponível.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Porque é que as etiquetas para os meus VMs não estão a fluir para os detalhes de utilização?
As tags fluem apenas para si o CSV de utilização apenas para VMs do Gestor de Recursos. As etiquetas de recursos clássicas não estão disponíveis nos detalhes de utilização.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Como pode a quantidade consumida ser mais de 24 horas por dia?
No modelo Clássico, a faturação de recursos é agregada ao nível do Cloud Service. Se tiver mais de um VM num Cloud Service que usa o mesmo medidor de faturação, o seu uso é agregado em conjunto. Os VM implantados através do Resource Manager são faturados ao nível de VM, pelo que esta agregação não se aplicará.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Porque é que os preços não estão disponíveis para os tamanhos DS/FS/GS/LS na página de preços?
Os VMs capazes de armazenamento premium são faturados ao mesmo ritmo que os VMs capazes de armazenamento não premium. Apenas os seus custos de armazenamento diferem. Visite a [página de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) para mais informações.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre os seus dados de utilização, consulte [a sua conta para o Microsoft Azure.](../cost-management-billing/understand/review-individual-bill.md)
