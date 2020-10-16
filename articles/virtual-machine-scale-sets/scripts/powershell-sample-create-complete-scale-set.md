---
title: Amostras Azure PowerShell - Criar um conjunto completo de escala de máquina virtual
description: Este script cria um conjunto de escala de máquina virtual em execução do Windows Server 2016, onde os recursos individuais são configurados e criados.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: powershell
ms.date: 05/29/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: d37f5f624459db6bc336884987a16c60503492a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89078508"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Criar um conjunto de dimensionamento de máquinas virtuais completo com o PowerShell

Este script cria um conjunto de dimensionamento de máquinas virtuais com o Windows Server 2016. Os recursos individuais são configurados e criados, em vez de utilizarem as [opções de criação de recursos incorporados disponíveis aqui em New-AzVmss](powershell-sample-create-simple-scale-set.md). Depois de executar o script, pode aceder às instâncias de VMs através de RDP.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Execute o seguinte comando para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>Explicação do script
Este script utiliza os seguintes comandos para criar a implementação. Cada item na tabela liga a documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Cria uma configuração de sub-rede. Esta configuração é utilizada com o processo de criação de rede virtual. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Cria uma configuração de IP de front-end para um balanceador de carga. |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Cria uma configuração do conjunto de endereços de back-end para um balanceador de carga. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Cria uma configuração de regras de NAT de entrada para um balanceador de carga. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Cria um balanceador de carga. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Cria uma configuração de pesquisa para um balanceador de carga. |
| [Add-AzLoadBalanceruleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Cria uma configuração de regras para um balanceador de carga. |
| [Set-AzLoadBalancer](/powershell/module/az.Network/Set-azLoadBalancer) | Atualize o balanceador de carga com as informações fornecidas. |
| [New-AzVmssIpConfig](/powershell/module/az.Compute/New-azVmssIpConfig) | Crie uma configuração de IP para as instâncias de VM do conjunto de dimensionamento. As instâncias de VM estão ligadas ao conjunto de back-end do balanceador de carga, ao conjunto NAT e à sub-rede da rede virtual. |
| [New-AzVmssConfig](/powershell/module/az.Compute/New-azVmssConfig) | Cria uma configuração de conjunto de dimensionamento. Esta configuração inclui informações como o número de instâncias VM para criar, o SKU (tamanho) de VM e o modo de política de atualização. A configuração é adicionada pelos cmdlets adicionais e é utilizada durante a criação do conjunto de dimensionamento. |
| [Set-AzVmssStorageProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Defina a imagem a ser utilizada para as instâncias VM e adicione-a à configuração de conjunto de dimensionamento. |
| [Set-AzVmssOsProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Define o nome de utilizador administrativo, as credenciais de palavra-passe e o prefixo de atribuição de nomes de VM. Adicione estes valores à configuração do conjunto de dimensionamento. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.Compute/Add-azVmssNetworkInterfaceConfiguration) | Adicione uma interface de rede virtual às instâncias de VM, com base na configuração de IP. Adicione estes valores à configuração do conjunto de dimensionamento. |
| [New-AzVmss](/powershell/module/az.Compute/New-azVmss) | Crie o conjunto de dimensionamento, com base nas informações fornecidas na configuração do conjunto de dimensionamento. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).
