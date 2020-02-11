---
title: Códigos de erro para VMs de Spot Azure e conjuntos de escala casos
description: Aprenda sobre códigos de erro que poderia ver ao utilizar VMs spot e instâncias de conjunto de escala.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/20/2019
ms.author: cynthn
ms.openlocfilehash: 1863c510967dcf80044d768aee1a37643a068ce4
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77115863"
---
# <a name="preview-error-messages-for-spot-vms-and-scale-sets"></a>Pré-visualização: Mensagens de erro para VMs de mancha e conjuntos de escala


> [!IMPORTANT]
> Os VMs de spot e os conjuntos de escala de máquinas virtuais estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Aqui estão alguns possíveis códigos de erro que pode receber ao utilizar VMs spot e conjuntos de escala.


| Chave | Mensagem | Descrição |
|-----|---------|-------------|
| SkuNotAvailable | O nível solicitado para o recurso\<\>de recursos não está atualmente disponível no local "\<localização\>" para subscrição\<\>de subscrição .' Por favor, tente outro nível ou desloque-se para um local diferente. | Não há capacidade suficiente do Azure Spot neste local para criar a sua vm ou instância de conjunto de escala. |
| Política de DespejoCanBeSetOnlyOnAzureSpotVirtualMáquinas virtuais  |  A política de despejo só pode ser definida nas Máquinas Virtuais Azure Spot. | Este VM não é um VM spot, então você não pode definir a política de despejo. |
| Conjunto de disponibilidade sinuosa azureSpotVMNotSupportedInavailabilitySet  |  A Máquina Virtual Azure Spot não é suportada no Conjunto de Disponibilidade. | Você precisa escolher usar um VM Spot ou usar um VM em um conjunto de disponibilidade, você não pode escolher ambos. |
| AzureSpotFeatureNotEnabledForSubscription  |  Subscrição não ativada com recurso Azure Spot. | Precisa de ter uma subscrição que suporte Os VMs spot. |
| VMPriorityCanApplied  |  O valor prioritário especificado '{0}' não pode ser aplicado à{1}da Máquina Virtual , uma vez que não foi especificada qualquer prioridade aquando da criação da Máquina Virtual. | Precisa especificar a prioridade quando o VM é criado. |
| Preço SpotPriceGreaterThanProvidedMaxPrice  |  Incapaz de executar a operação '{0}' uma vez que o preço máximo fornecido '{1} USD' é inferior ao preço atual à vista '{2} USD' para o tamanho de VM De Spot '{3}'. | Selecione um preço máximo mais elevado. Para mais informações, consulte informações sobre preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Valor de preço máximo inválido. Os únicos valores suportados pelo preço máximo são -1 ou um decimal superior a zero. O preço máximo de -1 indica que a máquina virtual Azure Spot não será despejada por razões de preço. | Introduza um preço máximo válido. Para mais informações, consulte os preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeChangeNotAllowedForAllocatedVMs | A variação máxima dos preços não é permitida quando o{0}vm é atualmente atribuído. Por favor, desaloque e tente de novo. | Stop\Deallocate o VM para que possa alterar o preço máximo. |
| MaxPriceChangeNão Permitido | Não é permitida uma mudança de preço máxima. | Não pode alterar o preço máximo para este VM. |
| AzureSpotIsNotSupported ForThisAPIVersion  |  O Azure Spot não é suportado para esta versão API. | A versão API tem de ser 2019-03-01. |
| AzureSpotIsNotSupported ForThisVMSize  |  O Azure Spot não é suportado para este tamanho VM {0}. | Selecione outro tamanho VM. Para mais informações, consulte [Spot Virtual Machines](./linux/spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  O preço máximo é suportado apenas para máquinas virtuais Azure Spot. | Para mais informações, consulte [Spot Virtual Machines](./linux/spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  O pedido de recursos move contém uma máquina virtual Azure Spot. Não há suporte para isso no momento. Verifique os detalhes do erro para obter ids de máquina virtual. | Não pode mover VMs spot. |
| MoveResourcesWithAzureSpotVmsSNotSupported  |  O pedido de recursos Move contém um conjunto de máquinas virtuais Azure Spot. Não há suporte para isso no momento. Verifique os detalhes de erro para obter ids de escala de máquina virtual. | Não é possível mover as instâncias de conjunto de escala spot. |
| EphemeralOSDiskSNotSupportedForSpotVMs | Os discos efémeros não são suportados para VMs de mancha. | Tem de utilizar um disco operativo regular para o seu VM spot. |
| Modo de Orquestração AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | A Máquina Virtual Azure Spot não é suportada em conjunto de escala de máquina virtual com modo de orquestração VM. | Detete o modo de orquestração para a escala virtual da máquina definida para utilizar as instâncias spot. |


**Próximos passos** Para mais informações, consulte [as Máquinas Virtuais.](./linux/spot-vms.md)