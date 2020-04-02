---
title: Códigos de erro para VMs de Spot Azure e conjuntos de escala casos
description: Aprenda sobre códigos de erro que poderia ver ao utilizar VMs spot e instâncias de conjunto de escala.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5a34dc2b9468c6c5af4af0e0addfd8b9ebb7e792
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547821"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Mensagens de erro para VMs de mancha e conjuntos de escala

Aqui estão alguns possíveis códigos de erro que pode receber ao utilizar VMs spot e conjuntos de escala.


| Chave | Mensagem | Descrição |
|-----|---------|-------------|
| SkuNotAvailable | O nível solicitado\<para\>o recurso " recurso\<"\>não está\<atualmente disponível na localização ' localização ' localização ' para subscrição ' subscrição ' iD\>'. Por favor, tente outro nível ou desloque-se para um local diferente. | Não há capacidade suficiente do Azure Spot neste local para criar a sua vm ou instância de conjunto de escala. |
| Política de DespejoCanBeSetOnlyOnAzureSpotVirtualMáquinas virtuais  |  A política de despejo só pode ser definida nas Máquinas Virtuais Azure Spot. | Este VM não é um VM spot, então você não pode definir a política de despejo. |
| Conjunto de disponibilidade sinuosa azureSpotVMNotSupportedInavailabilitySet  |  A Máquina Virtual Azure Spot não é suportada no Conjunto de Disponibilidade. | Você precisa escolher usar um VM Spot ou usar um VM em um conjunto de disponibilidade, você não pode escolher ambos. |
| AzureSpotFeatureNotEnabledForSubscription  |  Subscrição não ativada com recurso Azure Spot. | Utilize uma subscrição que suporta VMs spot. |
| VMPriorityCanApplied  |  O valor{0}prioritário especificado não pode ser{1}aplicado à Máquina Virtual , uma vez que não foi especificada qualquer prioridade aquando da criação da Máquina Virtual. | Especifique a prioridade quando o VM for criado. |
| Preço SpotPriceGreaterThanProvidedMaxPrice  |  Incapaz de executar{0}a operação ' '{1} uma vez que o preço{2} máximo fornecido ' USD '{3}é inferior ao preço atual ' USD ' para o tamanho de VM Spot Azure '. | Selecione um preço máximo mais elevado. Para mais informações, consulte informações sobre preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Valor de preço máximo inválido. Os únicos valores suportados pelo preço máximo são -1 ou um decimal superior a zero. O preço máximo de -1 indica que a máquina virtual Azure Spot não será despejada por razões de preço. | Introduza um preço máximo válido. Para mais informações, consulte os preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeChangeNotAllowedForAllocatedVMs | A variação máxima dos preços{0}não é permitida quando o VM ' é atualmente atribuído. Por favor, desaloque e tente de novo. | Stop\Deallocate o VM para que possa alterar o preço máximo. |
| MaxPriceChangeNão Permitido | Não é permitida uma mudança de preço máxima. | Não pode alterar o preço máximo para este VM. |
| AzureSpotIsNotSupported ForThisAPIVersion  |  O Azure Spot não é suportado para esta versão API. | A versão API tem de ser 2019-03-01. |
| AzureSpotIsNotSupported ForThisVMSize  |  O Azure Spot não é suportado para este tamanho {0}VM . | Selecione outro tamanho VM. Para mais informações, consulte [Spot Virtual Machines](./linux/spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  O preço máximo é suportado apenas para máquinas virtuais Azure Spot. | Para mais informações, consulte [Spot Virtual Machines](./linux/spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  O pedido de recursos move contém uma máquina virtual Azure Spot. Isto não é atualmente apoiado. Verifique os detalhes do erro para obter ids de máquina virtual. | Não pode mover VMs spot. |
| MoveResourcesWithAzureSpotVmsSNotSupported  |  O pedido de recursos Move contém um conjunto de máquinas virtuais Azure Spot. Isto não é atualmente apoiado. Verifique os detalhes de erro para obter ids de escala de máquina virtual. | Não é possível mover as instâncias de conjunto de escala spot. |
| EphemeralOSDiskSNotSupportedForSpotVMs | Os discos efémeros não são suportados para VMs de mancha. | Utilize um disco operativo regular para o seu VM spot. |
| Modo de Orquestração AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | A Máquina Virtual Azure Spot não é suportada em conjunto de escala de máquina virtual com modo de orquestração VM. | Detete o modo de orquestração para a escala virtual da máquina definida para utilizar as instâncias spot. |


**Próximos passos** Para mais informações, consulte [as Máquinas Virtuais.](./linux/spot-vms.md)