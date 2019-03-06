---
title: Exemplos do Azure PowerShell – Criar um conjunto de dimensionamento de máquinas virtuais completo | Microsoft Docs
description: Exemplos do Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ebbc47739b2be72d0dd98c0659bfcaba512e79e9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448931"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Criar um conjunto de dimensionamento de máquinas virtuais completo com o PowerShell

Este script cria um conjunto de dimensionamento de máquinas virtuais com o Windows Server 2016. Recursos individuais são configurados e criados, em vez da utilizar o [criação de recursos incorporadas as opções disponíveis aqui em New-AzVmss](powershell-sample-create-simple-scale-set.md). Depois de executar o script, pode aceder às instâncias de VMs através de RDP.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

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
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Cria uma configuração de regras para um balanceador de carga. |
| [Set-AzLoadBalancer](/powershell/module/az.Network/Set-azLoadBalancer) | Atualize o balanceador de carga com as informações fornecidas. |
| [New-AzVmssIpConfig](/powershell/module/az.Compute/New-azVmssIpConfig) | Crie uma configuração de IP para as instâncias de VM do conjunto de dimensionamento. As instâncias de VM estão ligadas ao conjunto de back-end do balanceador de carga, ao conjunto NAT e à sub-rede da rede virtual. |
| [New-AzVmssConfig](/powershell/module/az.Compute/New-azVmssConfig) | Cria uma configuração de conjunto de dimensionamento. Esta configuração inclui informações como o número de instâncias VM para criar, o SKU (tamanho) de VM e o modo de política de atualização. A configuração é adicionada pelos cmdlets adicionais e é utilizada durante a criação do conjunto de dimensionamento. |
| [Set-AzVmssStorageProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Defina a imagem a ser utilizada para as instâncias VM e adicione-a à configuração de conjunto de dimensionamento. |
| [Set-AzVmssOsProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Define o nome de utilizador administrativo, as credenciais de palavra-passe e o prefixo de atribuição de nomes de VM. Adicione estes valores à configuração do conjunto de dimensionamento. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.Compute/Add-azVmssNetworkInterfaceConfiguration) | Adicione uma interface de rede virtual às instâncias de VM, com base na configuração de IP. Adicione estes valores à configuração do conjunto de dimensionamento. |
| [New-AzVmss](/powershell/module/az.Compute/New-azVmss) | Crie o conjunto de dimensionamento, com base nas informações fornecidas na configuração do conjunto de dimensionamento. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode encontrar exemplos adicionais do script do conjunto de dimensionamento de máquinas virtuais do PowerShell na [documentação do conjunto de dimensionamento de máquinas virtuais do Azure](../powershell-samples.md).
