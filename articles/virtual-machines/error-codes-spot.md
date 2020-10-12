---
title: Códigos de erro para VMs Azure Spot e séries de séries
description: Saiba mais sobre códigos de erro que poderá ver ao utilizar VMs spot e ver instâncias definidas em escala.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 3e151145bd3c4d1db87ef5d1b0d553555a580279
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816750"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Mensagens de erro para VMs spot e conjuntos de escala

Aqui estão alguns possíveis códigos de erro que pode receber ao utilizar VMs spot e conjuntos de escala.


| Chave | Mensagem | Descrição |
|-----|---------|-------------|
| SkuNotAvailable | O nível solicitado para o recurso \<resource\> ' » não está atualmente disponível no local ' ' para \<location\> subscrição \<subscriptionID\> '. Por favor, tente outro nível ou desloque-se para um local diferente. | Não há capacidade suficiente do Azure Spot neste local para criar o seu VM ou a sua instância de definição de escala. |
| DespejoPolicyCanBeSetOnonAzurespotVirtualMachines  |  A política de despejo só pode ser definida nas máquinas virtuais Azure Spot. | Este VM não é um Spot VM, por isso não podes definir a política de despejo. |
| AzurespotVMNotsupportedInAvailabilitySet  |  A máquina virtual Azure Spot não é suportada no Conjunto de Disponibilidade. | Você precisa escolher usar um VM spot ou usar um VM em um conjunto de disponibilidade, você não pode escolher ambos. |
| AzureSpotFeatureNotEnabledForSubscription  |  Subscrição não ativada com recurso Azure Spot. | Utilize uma subscrição que suporte VMs spot. |
| VMPriorityCannotBeApplied  |  O valor prioritário especificado {0} ' ' não pode ser aplicado à Máquina Virtual ' ' uma vez que nenhuma prioridade foi {1} especificada quando a Máquina Virtual foi criada. | Especifique a prioridade quando o VM é criado. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Incapaz de executar a operação ' {0} ' ' uma vez que o preço máximo fornecido ' {1} USD' é inferior ao preço à vista atual ' {2} USD' para o tamanho VM de Azure Spot {3} '. | Selecione um preço máximo mais alto. Para obter mais informações, consulte as informações sobre preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Valor do preço máximo inválido. Os únicos valores suportados para o preço máximo são -1 ou um decimal superior a zero. O valor máximo de preço de -1 indica que a máquina virtual Azure Spot não será despejada por razões de preço. | Insira um preço máximo válido. Para obter mais informações, consulte os preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | Não é permitida a alteração máxima dos preços quando o VM {0} ' » é atualmente atribuído. Por favor, faça um acordo e tente de novo. | Stop\Deallocate the VM para que possa alterar o preço máximo. |
| MaxPriceChangeNotAllowed | Não é permitida a mudança de preço máxima. | Não é possível alterar o preço máximo deste VM. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  O Azure Spot não é suportado para esta versão API. | A versão API tem de ser 2019-03-01. |
| AzurespotIsNotsupportedForThisvmsize  |  O Azure Spot não é suportado para este tamanho VM {0} . | Selecione outro tamanho VM. Para mais informações, consulte [as Máquinas Virtuais Spot.](./spot-vms.md) |
| MaxPriceIssupportedOnlyForAzurespotVirtualMachines  |  O preço máximo é suportado apenas para máquinas virtuais Azure Spot. | Para mais informações, consulte [as Máquinas Virtuais Spot.](./spot-vms.md) |
| MoveResourcesWithAzureSpotVMNotSupported  |  O pedido de recursos move contém uma máquina virtual Azure Spot. Atualmente, isto não é apoiado. Verifique os detalhes de erro para identificação de máquinas virtuais. | Não pode mover VMs spot. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  O pedido de recursos Move contém um conjunto de escala de máquina virtual Azure Spot. Atualmente, isto não é apoiado. Verifique os detalhes de erro para identificação de balança de máquina virtual. | Não é possível mover instâncias de escala spot. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | A máquina virtual Azure Spot não é suportada em Conjunto de Escala de Máquina Virtual com modo de Orquestração VM. | Desagrema o modo de orquestração à escala de máquina virtual definida para utilizar instâncias spot. |


**Próximos passos** Para mais informações, consulte [as Máquinas Virtuais spot.](./spot-vms.md)