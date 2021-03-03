---
title: Códigos de erro para máquinas virtuais Azure Spot e conjuntos de escalas
description: Saiba mais sobre códigos de erro que poderá ver ao utilizar máquinas virtuais Azure Spot e definir instâncias.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 6d43935365580233063deb0e523d222351c22d54
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670617"
---
# <a name="error-messages-for-azure-spot-virtual-machines-and-scale-sets"></a>Mensagens de erro para máquinas virtuais Azure Spot e conjuntos de escala

Aqui estão alguns possíveis códigos de erro que pode receber quando utilizar máquinas virtuais Azure Spot e conjuntos de escala.


| Chave | Mensagem | Descrição |
|-----|---------|-------------|
| SkuNotAvailable | O nível solicitado para o recurso \<resource\> ' » não está atualmente disponível no local ' ' para \<location\> subscrição \<subscriptionID\> '. Tente outro nível ou desloque-se para um local diferente. | Não existe capacidade suficiente da Máquina Virtual Azure Spot neste local para criar o seu VM ou a sua instância de definição de escala. |
| DespejoPolicyCanBeSetOnonAzurespotVirtualMachines  |  A política de despejo só pode ser definida nas máquinas virtuais Azure Spot. | Este VM não é uma máquina virtual Azure Spot, por isso não pode definir a política de despejo. |
| AzurespotVMNotsupportedInAvailabilitySet  |  A máquina virtual Azure Spot não é suportada no Conjunto de Disponibilidade. | Tem de escolher utilizar uma Máquina Virtual Azure Spot ou utilizar um VM num conjunto de disponibilidade, não pode escolher ambos. |
| AzureSpotFeatureNotEnabledForSubscription  |  Subscrição não ativada com recurso Azure Spot Virtual Machine. | Utilize uma subscrição que suporte máquinas virtuais Azure Spot. |
| VMPriorityCannotBeApplied  |  O valor prioritário especificado {0} ' ' não pode ser aplicado à Máquina Virtual ' ' uma vez que nenhuma prioridade foi {1} especificada quando a Máquina Virtual foi criada. | Especifique a prioridade quando o VM é criado. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Incapaz de executar a operação ' {0} ' uma vez que o preço máximo fornecido ' {1} USD' é inferior ao preço à vista atual ' {2} USD' para O tamanho da máquina virtual Azure Spot {3} '. | Selecione um preço máximo mais alto. Para obter mais informações, consulte as informações sobre preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Valor do preço máximo inválido. Os únicos valores suportados para o preço máximo são -1 ou um decimal superior a zero. O valor máximo de preço de -1 indica que a Máquina Virtual Azure Spot não será despejada por razões de preço. | Insira um preço máximo válido. Para obter mais informações, consulte os preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | Não é permitida a alteração máxima dos preços quando o VM {0} ' » é atualmente atribuído. Deallo e tente de novo. | Stop\Deallocate the VM para que possa alterar o preço máximo. |
| MaxPriceChangeNotAllowed | Não é permitida a mudança de preço máxima. | Não é possível alterar o preço máximo deste VM. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  A Azure Spot Virtual Machine não é suportada para esta versão API. | A versão API tem de ser 2019-03-01. |
| AzurespotIsNotsupportedForThisvmsize  |  A máquina virtual Azure Spot não é suportada para este tamanho VM {0} . | Selecione outro tamanho VM. Para mais informações, consulte [as Máquinas Virtuais Azure Spot.](./spot-vms.md) |
| MaxPriceIssupportedOnlyForAzurespotVirtualMachines  |  O preço máximo é suportado apenas para máquinas virtuais Azure Spot. | Para mais informações, consulte [as Máquinas Virtuais Azure Spot.](./spot-vms.md) |
| MoveResourcesWithAzureSpotVMNotSupported  |  O pedido de recursos move contém uma máquina virtual Azure Spot. Não suportado. Verifique os detalhes de erro para identificação de máquinas virtuais. | Não é possível mover máquinas virtuais Azure Spot. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  O pedido de recursos Move contém um conjunto de escala de máquina virtual Azure Spot. Não suportado. Verifique os detalhes de erro para identificação de balança de máquina virtual. | Não é possível mover as instâncias de escala de máquina virtual Azure Spot. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | A máquina virtual Azure Spot não é suportada em escala de máquina virtual definida com o modo de orquestração VM. | Desagreça o modo de orquestração à escala de máquina virtual definida para utilizar as instâncias da Máquina Virtual Azure Spot. |


**Próximos passos** Para mais informações, consulte [as Máquinas Virtuais spot.](./spot-vms.md)