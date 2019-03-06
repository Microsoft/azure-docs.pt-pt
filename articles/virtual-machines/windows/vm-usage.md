---
title: Compreender a utilização de máquina virtual do Azure | Documentos da Microsoft
description: Compreender os detalhes de utilização de máquinas virtuais
services: virtual-machines
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: ''
tags: azure-virtual-machine
ms.assetid: ''
ms.service: ''
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: memccror
ms.openlocfilehash: b515a0b226723989b1cc73356f1377da421dc9aa
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456334"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Compreender a utilização de máquina virtual do Azure
Ao analisar os dados de utilização do Azure, pode ser obtida a informações sobre consumo poderosas – informações que podem ativar a custam melhor gerenciamento e a alocação em toda a organização. Este documento fornece uma descrição aprofundada seus detalhes de consumo de computação do Azure. Para obter mais detalhes sobre a utilização do Azure geral, navegue para [entender a sua fatura](../../billing/billing-understand-your-bill.md).

## <a name="download-your-usage-details"></a>Transferir os seus detalhes de utilização
Para começar, [transferir os seus detalhes de utilização](../../billing/billing-download-azure-invoice-daily-usage-date.md). A tabela abaixo fornece os valores de definição e exemplo de utilização para máquinas virtuais implementadas através do Azure Resource Manager. Este documento não contém informações detalhadas para as VMs implementadas através do nosso modelo clássico.


| Campos             | Significado                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Valores de exemplo                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Data de Utilização         | A data em que o recurso foi utilizado.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  “11/23/2017”                                                                                                                                                                                                                                                                                                                                                     |
| ID do Medidor           | Identifica o serviço de nível superior que esta utilização corresponde a.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | "Máquinas virtuais"                                                                                                                                                                                                                                                                                                                                               |
| Subcategoria do Medidor | O identificador do medidor faturado. <ul><li>Para a utilização da hora de computação, há um medidor para cada tamanho da VM + o sistema operacional (Windows, não Windows) + região.</li><li>Para utilização de software de Premium, existe um medidor para cada tipo de software. A maioria das imagens de software de premium têm diferentes indicadores para cada tamanho de núcleo. Para obter mais informações, visite o [página de preços de computação.](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>                                                                                                                                                                                                                                                                                                                                         | “2005544f-659d-49c9-9094-8e0aea1be3a5”                                                                                                                                                                                                                                                                                                                           |
| Nome do Medidor         | Isso é específico para cada serviço no Azure. Para computação, é sempre "Horas de computação".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Horas de computação"                                                                                                                                                                                                                                                                                                                                                  |
| Região do Medidor       | Identifica a localização do datacenter para determinados serviços cujo preço é definido com base na localização do datacenter.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  "JA Leste"                                                                                                                                                                                                                                                                                                                                                       |
| Unidade               | Identifica a unidade que o serviço é cobrado. Recursos de computação são faturados por hora.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Horas"                                                                                                                                                                                                                                                                                                                                                          |
| Consumido           | A quantidade do recurso que foi consumido para esse dia. Para computação, faturamos cada minuto que a VM for executada durante uma determinada hora (até 6 casas decimais de precisão).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    “1”, “0.5”                                                                                                                                                                                                                                                                                                                                                    |
| Localização do Recurso  | Identifica o datacenter onde o recurso está em execução.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | "JA Leste"                                                                                                                                                                                                                                                                                                                                                        |
| Serviço Consumido   | O serviço de plataforma do Azure que utilizou.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft.Compute"                                                                                                                                                                                                                                                                                                                                              |
| Grupo de Recursos     | O grupo de recursos no qual o recurso implementado está em execução. Para obter mais informações, consulte [descrição geral do Azure Resource Manager.](../../azure-resource-manager/resource-group-overview.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    "MyRG"                                                                                                                                                                                                                                                                                                                                                        |
| ID da Instância        | O identificador do recurso. O identificador contém o nome que especificou para o recurso quando o criou. Para as VMs, o ID de instância irá conter o SubscriptionId, ResourceGroupName e VMName (ou conjunto de dimensionamento de nome para a utilização do conjunto de dimensionamento).                                                                                                                                                                                                                                                                                                                                                                                                                    | "/ subscrições/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx / resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1"<br><br>ou<br><br>"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1”                                                                                           |
| Etiquetas               | Etiqueta de que atribuir ao recurso. Utilize etiquetas para registos de faturação de grupo. Saiba como [etiquetar as suas máquinas virtuais.](tag.md) Isso só está disponível para VMs do Resource Manager.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{"myDepartment":"RD","myUser":"myName"}"                                                                                                                                                                                                                                                                                                                        |
| Informações Adicionais    | Metadados de serviço específicos. Para as VMs, vamos preencher o seguinte no campo de informações adicionais: <ul><li>Imagem de específicos do tipo de imagem que executou. Encontre a lista completa das cadeias suportadas abaixo em tipos de imagem.</li><li>Tipo de serviço: o tamanho que implementou.</li><li>VMName: nome da sua VM. Só é preenchido para o conjunto de dimensionamento de VMs. Se tiver o nome da VM de dimensionamento de VMs do conjunto, pode encontrar na cadeia de ID de instância acima.</li><li>UsageType: Esta ação Especifica o tipo de utilização, que isso representa.<ul><li>ComputeHR é a utilização de hora de computação para a VM subjacente, como Standard_D1_v2.</li><li>ComputeHR_SW corresponde à cobrança de software de premium, se a VM estiver a utilizar o software de premium, como o Microsoft R Server.</li></ul></li></ul>    | Virtual Machines {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}<br><br>Virtual Machine Scale Sets {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}<br><br>Premium Software {"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"} |

## <a name="image-type"></a>Tipo de Imagem
Para algumas imagens na galeria do Azure, o tipo de imagem é preenchido no campo de informações adicionais. Isto permite aos utilizadores compreender e controlar o que eles serem implementados na respetiva Máquina Virtual. Os valores que são preenchidos neste campo com base na imagem que implementou são os seguintes:
  - BitRock 
  - Canónico 
  - FreeBSD 
  - Lógica aberta 
  - Oracle 
  - SLES para SAP 
  - Pré-visualização do SQL Server 14 no Windows Server 2012 R2 Preview 
  - SUSE
  - SUSE Premium
  - StorSimple Cloud Appliance 
  - Red Hat
  - Red Hat para o SAP Business Applications     
  - Red Hat para o SAP HANA 
  - Windows Client BYOL 
  - Windows Server BYOL 
  - Windows Server Preview 

## <a name="service-type"></a>Tipo de Serviço
O campo de tipo de serviço no campo de informações adicionais corresponde ao tamanho VM exato que implementou. (Baseado em SSD) de VMs do Premium storage e não premium storage VMs (baseados em HDD) são o mesmo preço. Se implementar um tamanho baseado em SSD, como o padrão\_DS2\_v2, pode ver o tamanho não SSD (' padrão\_D2\_v2 VM ") na coluna subcategoria do medidor e o tamanho de SSD (' padrão\_DS2\_ v2 ") no campo de informações adicionais.

## <a name="region-names"></a>Nomes de região
O nome da região preenchido no campo de localização de recursos nos detalhes de utilização varia do nome da região utilizado no Azure Resource Manager. Este é um mapeamento entre os valores de região:

|    **Nome da região do Resource Manager**       |    **Localização do recurso nos detalhes de utilização**    |
|--------------------------|------------------------------------------|
|    lesteaustrália         |    AU Leste                               |
|    sudesteaustrália    |    AU Sudeste                          |
|    sulbrasil           |    Sul BR                              |
|    CanadaCentral         |    Canadá Central                            |
|    CanadaEast            |    Leste do Canadá                               |
|    CentralIndia          |    IN Central                            |
|    euacentral             |    EUA Central                            |
|    chinaeast             |    Leste da China                            |
|    chinanorth            |    China Norte                           |
|    eastasia              |    Ásia Oriental                             |
|    eualeste                |    EUA Leste                               |
|    eualeste2               |    EUA Leste 2                             |
|    GermanyCentral        |    Alemanha Central                            |
|    GermanyNortheast      |    Nordeste da Alemanha                          |
|    lestejapão             |    Este JA                               |
|    oestejapão             |    Oeste JA                               |
|    KoreaCentral          |    KR do Sul Central                            |
|    KoreaSouth            |    Sul KR do Sul                              |
|    euacentronorte        |    EUA Centro-Norte                      |
|    northeurope           |    Europa do Norte                          |
|    southcentralus        |    EUA Centro-Sul                      |
|    sudesteasiático         |    Sudeste Asiático                        |
|    SouthIndia            |    IN Sul                              |
|    UKNorth               |    Norte dos EUA                              |
|    uksouth               |    Reino Unido Sul                              |
|    UKSouth2              |    Sul do Reino Unido 2                            |
|    ukwest                |    Reino Unido Oeste                               |
|    USDoDCentral          |    US DoD Centro                        |
|    USDoDEast             |    US DoD Leste                           |
|    USGovArizona          |    Gov (US) - Arizona                         |
|    usgoviowa             |    USGov Iowa                            |
|    USGovTexas            |    Gov (US) - Texas                           |
|    usgovvirginia         |    USGov Virginia                        |
|    westcentralus         |    E.U.A. Centro-Oeste                       |
|    europaocidental            |    Europa Ocidental                           |
|    WestIndia             |    Índia Ocidental                               |
|    westus                |    EUA Oeste                               |
|    westus2               |    E.U.A. Oeste 2                             |


## <a name="virtual-machine-usage-faq"></a>FAQ de utilização de máquina virtual
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Quais recursos são cobrados ao implementar uma VM?    
As VMs adquirem os custos para a VM propriamente dito, qualquer software de premium em execução na VM, o disco de account\managed de armazenamento associado à VM, e a largura de banda de rede transfere a partir da VM.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Como posso saber se uma VM está a utilizar o benefício híbrido do Azure no CSV de utilização?
Se implementar com o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), é-lhe cobrada a tarifa da VM de não-Windows, uma vez que estão a levar sua própria licença para a nuvem. Na sua fatura, pode distinguir que VMs do Resource Manager estão a executar o benefício híbrido do Azure porque têm um "Windows\_Server BYOL" ou "Windows\_cliente BYOL" na coluna ImageType.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Como são vs básicas. Tipos de VM Standard diferenciados no CSV de utilização?
Basic e VMs de série A Standard são oferecidas. Se implementar uma VM básica, na subcategoria do medidor, tem a cadeia de caracteres "Básico". Se implementar uma VM de série A Standard, em seguida, o tamanho da VM é apresentado como "A1 VM", uma vez que o escalão Standard é a predefinição. Para saber mais sobre as diferenças entre os escalões básico e Standard, veja a [página de preços](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Quais são os tamanhos de extra pequena, pequena, média, grande e extra grande?
ExtraSmall - ExtraLarge são os nomes de legado para o Standard\_A0 – Standard\_A4. Nos registos de utilização VM clássicos, poderá ver essa convenção utilizada se tiver implementado estes tamanhos.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>O que é a diferença entre a região do medidor e localização de recursos?
A região do medidor está associado com o medidor. Para alguns serviços do Azure que utilizar um preço de todas as regiões, o campo de região do medidor poderia estar em branco. No entanto, uma vez que as VMs têm dedicados os preços por região para máquinas virtuais, este campo é preenchido. Da mesma forma, a localização do recurso para máquinas virtuais é o local onde a VM é implementada. A região do Azure em ambos os campos são iguais, embora possam ter uma convenção de cadeia de caracteres diferentes para o nome da região.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Por que é o valor de ImageType em branco no campo de informações adicionais?
O campo de ImageType só é preenchido para um subconjunto de imagens. Se não implemente uma das imagens acima, o ImageType está em branco.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Por que é o VMName em branco nas informações adicionais?
VMName só é preenchido no campo de informações adicionais para as VMs num conjunto de dimensionamento. O campo de InstanceID contém o nome da VM para VMs do conjunto de dimensionamento não.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>O que significa ComputeHR no campo UsageType as informações adicionais?
ComputeHR significa horas de computação de mensagens em fila que representa o evento de utilização para o custo da infraestrutura subjacente. Se o UsageType for ComputeHR\_SW, o evento de utilização representa o custo de software de premium para a VM.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Como posso saber se me cobrada a de software do premium?
Ao explorar a imagem de VM melhor se adequa às suas necessidades, certifique-se de que consulte o [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). A imagem tem a taxa de plano de software. Se vir "Gratuito" para a taxa, não há sem custos adicionais para o software. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>O que é a diferença entre a Microsoft. classiccompute e Microsoft. Compute no serviço consumido?
Microsoft. classiccompute representa os recursos clássicos implementados através do Gestor de serviço do Azure. Se implementar através do Resource Manager, Microsoft. Compute é preenchido no serviço consumido. Saiba mais sobre o [modelos de implementação do Azure](../../azure-resource-manager/resource-manager-deployment-model.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Por que motivo é que o campo de InstanceID em branco para a minha utilização da Máquina Virtual?
Se implementar através do modelo de implementação clássica, a cadeia de caracteres InstanceID não está disponível.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Por que são as etiquetas para minhas VMs não que fluem para os detalhes de utilização?
Etiquetas apenas fluir a o CSV de utilização para VMs do Resource Manager apenas. As etiquetas de recursos clássicos não estão disponíveis nos detalhes de utilização.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Como é que a quantidade consumida pode ser mais de 24 horas de um dia?
No modelo clássico, a faturação de recursos é agregada ao nível do serviço em nuvem. Se tiver mais de uma VM num serviço Cloud que utiliza o mesmo contador de faturação, sua utilização é agregada em conjunto. As VMs implementadas através do Resource Manager são faturadas ao nível da VM, para que esta agregação não será aplicada.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Por isso que o preço é não disponível para os tamanhos DS/FS/GS/LS na página de preços?
O armazenamento Premium com capacidade de VMs são faturadas a mesma tarifa não premium storage VMs com capacidade. Apenas os custos de armazenamento diferem. Visite o [página de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre os seus detalhes de utilização, veja [compreender a sua fatura do Microsoft Azure.](../../billing/billing-understand-your-bill.md)

