---
title: Noções básicas sobre o uso da máquina virtual do Azure | Microsoft Docs
description: Entender os detalhes de uso da máquina virtual
services: virtual-machines
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: ''
tags: azure-virtual-machine
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: memccror
ms.openlocfilehash: 303bf41d943267c42c1311d185a4ff65baf5178f
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834601"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Noções básicas sobre o uso da máquina virtual do Azure
Analisando os dados de uso do Azure, as ideias de consumo poderosas podem ser obtidas – informações que podem permitir um melhor gerenciamento e alocação de custos em toda a sua organização. Este documento fornece um detalhamento dos detalhes de consumo de computação do Azure. Para obter mais detalhes sobre o uso geral do Azure, navegue até [noções básicas sobre sua fatura](../../billing/billing-understand-your-bill.md).

## <a name="download-your-usage-details"></a>Baixar os detalhes de uso
Para começar, [Baixe os detalhes de uso](../../billing/billing-download-azure-invoice-daily-usage-date.md). A tabela a seguir fornece os valores de definição e exemplo de uso para máquinas virtuais implantadas por meio do Azure Resource Manager. Este documento não contém informações detalhadas para VMs implantadas por meio de nosso modelo clássico.


| Campos             | Significado                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Valores de exemplo                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Data de Utilização         | A data em que o recurso foi usado.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  “11/23/2017”                                                                                                                                                                                                                                                                                                                                                     |
| ID de Medidor           | Identifica o serviço de nível superior ao qual esse uso pertence.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | "Máquinas virtuais"                                                                                                                                                                                                                                                                                                                                               |
| Subcategoria do Medidor | O identificador do medidor faturado. <ul><li>Para o uso de horas de computação, há um medidor para cada VM tamanho + sistema operacional (Windows, não Windows) + região.</li><li>Para o uso de software Premium, há um medidor para cada tipo de software. A maioria das imagens de software Premium tem medidores diferentes para cada tamanho de núcleo. Para obter mais informações, visite a [página de preços de computação.](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>                                                                                                                                                                                                                                                                                                                                         | "2005544f-659d-49c9-9094-8e0aea1be3a5"                                                                                                                                                                                                                                                                                                                           |
| Nome de Medidor         | Isso é específico para cada serviço no Azure. Para computação, é sempre "Horas de Computação".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Horas de Computação"                                                                                                                                                                                                                                                                                                                                                  |
| Região do Medidor       | Identifica a localização do datacenter para determinados serviços cujo preço é definido com base na localização do datacenter.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  "Leste do Japão"                                                                                                                                                                                                                                                                                                                                                       |
| Unidade               | Identifica a unidade na qual o serviço é cobrado. Os recursos de computação são cobrados por hora.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Duração                                                                                                                                                                                                                                                                                                                                                          |
| Consumido           | A quantidade do recurso que foi consumida para esse dia. Para computação, cobramos por cada minuto que a VM foi executada por uma determinada hora (até 6 decimais de precisão).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    “1”, “0.5”                                                                                                                                                                                                                                                                                                                                                    |
| Localização do Recurso  | Identifica o datacenter onde o recurso está em execução.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | "Leste do Japão"                                                                                                                                                                                                                                                                                                                                                        |
| Serviço Consumido   | O serviço da plataforma do Azure que você usou.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft. Compute"                                                                                                                                                                                                                                                                                                                                              |
| Grupo de Recursos     | O grupo de recursos no qual o recurso implementado está em execução. Para obter mais informações, consulte [Azure Resource Manager visão geral.](../../azure-resource-manager/resource-group-overview.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    "MyRG"                                                                                                                                                                                                                                                                                                                                                        |
| ID da instância        | O identificador do recurso. O identificador contém o nome que especificou para o recurso quando o criou. Para VMs, a ID da instância conterá SubscriptionId, ResourceGroupName e VMName (ou nome do conjunto de dimensionamento para uso do conjunto de dimensionamento).                                                                                                                                                                                                                                                                                                                                                                                                                    | "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyRG/Providers/Microsoft. Compute/virtualMachines/MyVM1"<br><br>ou<br><br>"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyRG/Providers/Microsoft. Compute/virtualMachineScaleSets/MyVMSS1"                                                                                           |
| Tags               | Marca que você atribui ao recurso. Use marcas para agrupar registros de cobrança. Saiba como [marcar suas máquinas virtuais.](tag.md) Isso está disponível somente para VMs do Gerenciador de recursos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{"myDepartment":"RD","myUser":"myName"}"                                                                                                                                                                                                                                                                                                                        |
| Informação Adicional    | Metadados específicos do serviço. Para VMs, preenchemos o seguinte no campo informações adicionais: <ul><li>Imagem específica do tipo de imagem que você executou. Localize a lista completa de cadeias de caracteres com suporte abaixo em tipos de imagem.</li><li>Tipo de serviço: o tamanho que você implantou.</li><li>VMName: nome da sua VM. Isso só é populado para VMs do conjunto de dimensionamento. Se você precisar do nome da VM para VMs do conjunto de dimensionamento, poderá encontrar isso na cadeia de caracteres de ID da instância acima.</li><li>UsageType Isso especifica o tipo de uso que representa.<ul><li>ComputeHR é o uso de hora de computação para a VM subjacente, como Standard_D1_v2.</li><li>ComputeHR_SW é a cobrança de software Premium se a VM estiver usando software Premium, como Microsoft R Server.</li></ul></li></ul>    | Virtual Machines {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}<br><br>Conjuntos de dimensionamento de máquinas virtuais {"ImageType": "canônico", "ServiceType": "Standard_DS1_v2", "VMName": "myVM1", "UsageType": "ComputeHR"}<br><br>Premium Software {"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"} |

## <a name="image-type"></a>Tipo de imagem
Para algumas imagens na galeria do Azure, o tipo de imagem é populado no campo informações adicionais. Isso permite que os usuários compreendam e acompanhem o que eles implantaram em sua máquina virtual. Os valores que são preenchidos nesse campo com base na imagem implantada são os seguintes:
  - BitRock 
  - Canónico 
  - FreeBSD 
  - Lógica aberta 
  - Oracle 
  - SLES para SAP 
  - Versão prévia do SQL Server 14 no Windows Server 2012 R2 Preview 
  - SUSE
  - SUSE Premium
  - StorSimple Cloud Appliance 
  - Red Hat
  - Red Hat para SAP Business Applications     
  - Red Hat para SAP HANA 
  - BYOL de cliente do Windows 
  - BYOL do Windows Server 
  - Visualização do Windows Server 

## <a name="service-type"></a>Tipo de Serviço
O campo tipo de serviço no campo informações adicionais corresponde ao tamanho exato da VM que você implantou. As VMs de armazenamento Premium (baseadas em SSD) e as VMs de armazenamento não Premium (baseadas em HDD) têm o mesmo preço. Se você implantar um tamanho baseado em SSD, como o\_Standard\_DS2 v2, verá o tamanho não SSD ("VM D2\_\_v2" padrão) na coluna de subcategoria do medidor e o tamanho de SSD (' Standard DS2\_ \_ v2 ') no campo informações adicionais.

## <a name="region-names"></a>Nomes de região
O nome da região populado no campo local do recurso nos detalhes de uso varia de acordo com o nome da região usada no Azure Resource Manager. Aqui está um mapeamento entre os valores de região:

|    **Nome da região do Resource Manager**       |    **Local do recurso em detalhes de uso**    |
|--------------------------|------------------------------------------|
|    lesteaustrália         |    Leste da Austrália                               |
|    sudesteaustrália    |    Sudeste da Austrália                          |
|    sulbrasil           |    Sul BR                              |
|    CanadaCentral         |    Centro do Canadá                            |
|    CanadaEast            |    Leste do Canadá                               |
|    CentralIndia          |    Central                            |
|    euacentral             |    EUA Central                            |
|    chinaeast             |    Leste da China                            |
|    chinanorth            |    Norte da China                           |
|    ásiaoriental              |    Ásia Oriental                             |
|    eualeste                |    East US                               |
|    eualeste2               |    EUA Leste 2                             |
|    GermanyCentral        |    DE Central                            |
|    GermanyNortheast      |    Nordeste da Alemanha                          |
|    lestejapão             |    Leste do Japão                               |
|    oestejapão             |    Oeste do Japão                               |
|    KoreaCentral          |    KR do Sul Central                            |
|    KoreaSouth            |    Sul KR do Sul                              |
|    euacentronorte        |    EUA Centro-Norte                      |
|    europanorte           |    Europa do Norte                          |
|    euacentrosul        |    EUA Centro-Sul                      |
|    sudesteasiático         |    Sudeste Asiático                        |
|    SouthIndia            |    Sul da Índia                              |
|    UKNorth               |    Norte dos EUA                              |
|    uksouth               |    Reino Unido Sul                              |
|    UKSouth2              |    Sul do R.U. 2                            |
|    ukwest                |    Reino Unido Oeste                               |
|    USDoDCentral          |    US DoD Centro                        |
|    USDoDEast             |    US DoD Leste                           |
|    USGovArizona          |    Gov (US) - Arizona                         |
|    usgoviowa             |    USGov Iowa                            |
|    USGovTexas            |    Gov (US) - Texas                           |
|    usgovvirginia         |    USGov Virginia                        |
|    westcentralus         |    E.U.A. Centro-Oeste                       |
|    europaocidental            |    Europa Ocidental                           |
|    WestIndia             |    Oeste da Índia                               |
|    euaoeste                |    EUA Oeste                               |
|    westus2               |    Oeste dos E.U.A 2                             |


## <a name="virtual-machine-usage-faq"></a>Perguntas frequentes sobre uso da máquina virtual
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Quais recursos são cobrados ao implantar uma VM?    
As VMs adquirem custos para a própria VM, qualquer software Premium em execução na VM, o disco account\managed de armazenamento associado à VM e as transferências de largura de banda de rede da VM.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Como saber se uma VM está usando Benefício Híbrido do Azure no CSV de uso?
Se você implantar usando o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), a taxa de VM não Windows será cobrada, pois você está trazendo sua própria licença para a nuvem. Em sua conta, você pode distinguir quais VMs do Resource Manager estão em execução benefício híbrido do Azure porque elas têm o\_"Windows Server BYOL" ou\_o "Windows Client BYOL" na coluna ImageType.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Como o básico vs. Tipos de VM padrão diferenciados no CSV de uso?
As VMs da série A básica e standard são oferecidas. Se você implantar uma VM básica, na subcategoria do medidor, ela terá a cadeia de caracteres "básica". Se você implantar uma VM de série A padrão, o tamanho da VM será exibido como "VM a1", pois Standard é o padrão. Para saber mais sobre as diferenças entre o básico e o Standard, consulte a [página de preços](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>O que são tamanhos ExtraSmall, pequeno, médio, grande e ExtraLarge?
ExtraSmall-ExtraLarge são os nomes herdados para\_Standard a0 –\_Standard a4. Em registros de uso de VM clássica, você poderá ver essa convenção usada se tiver implantado esses tamanhos.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Qual é a diferença entre a região do medidor e o local do recurso?
A região do medidor está associada ao medidor. Para alguns serviços do Azure que usam um preço para todas as regiões, o campo região de medidor pode ficar em branco. No entanto, como as VMs têm preços dedicados por região para máquinas virtuais, esse campo é populado. Da mesma forma, o local do recurso para máquinas virtuais é o local onde a VM é implantada. A região do Azure nos dois campos é a mesma, embora eles possam ter uma Convenção de cadeia de caracteres diferente para o nome da região.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Por que o valor ImageType está em branco no campo informações adicionais?
O campo ImageType só é populado para um subconjunto de imagens. Se você não tiver implantado uma das imagens acima, o ImageType ficará em branco.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Por que o VMName está em branco nas informações adicionais?
O VMName é populado apenas no campo informações adicionais para VMs em um conjunto de dimensionamento. O campo InstanceID contém o nome da VM para VMs de conjunto sem escala.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>O que significa ComputeHR no campo UsageType nas informações adicionais?
ComputeHR significa hora de computação que representa o evento de uso para o custo de infraestrutura subjacente. Se o UsageType for ComputeHR\_SW, o evento de uso representará a cobrança de software Premium para a VM.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Como fazer saber se eu sou cobrado pelo software Premium?
Ao explorar qual imagem de VM melhor atende às suas necessidades, não deixe de conferir o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). A imagem tem a taxa de plano de software. Se você vir "gratuito" para a taxa, não haverá nenhum custo adicional para o software. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Qual é a diferença entre Microsoft. ClassicCompute e Microsoft. Compute no serviço consumido?
Microsoft. ClassicCompute representa os recursos clássicos implantados por meio do Service Manager do Azure. Se você implantar por meio do Gerenciador de recursos, o Microsoft. Compute será populado no serviço consumido. Saiba mais sobre os [modelos de implantação do Azure](../../azure-resource-manager/resource-manager-deployment-model.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Por que o campo InstanceID está em branco para uso da máquina virtual?
Se você implantar por meio do modelo de implantação clássico, a cadeia de caracteres InstanceID não estará disponível.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Por que as marcas das minhas VMs não estão fluindo para os detalhes de uso?
As marcas só fluem para você o CSV de uso somente para VMs do Gerenciador de recursos. As marcas de recurso clássico não estão disponíveis nos detalhes de uso.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Como a quantidade consumida pode ser mais de 24 horas por dia?
No modelo clássico, a cobrança de recursos é agregada no nível do serviço de nuvem. Se você tiver mais de uma VM em um serviço de nuvem que usa o mesmo medidor de cobrança, seu uso será agregado em conjunto. As VMs implantadas por meio do Resource Manager são cobradas no nível da VM, portanto, essa agregação não será aplicada.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Por que os preços não estão disponíveis para os tamanhos DS/FS/GS/LS na página de preços?
As VMs com capacidade de armazenamento Premium são cobradas com a mesma taxa que as VMs compatíveis com armazenamento não Premium. Somente os custos de armazenamento são diferentes. Visite a [página de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre seus detalhes de uso, confira [entender sua fatura por Microsoft Azure.](../../billing/billing-understand-your-bill.md)

