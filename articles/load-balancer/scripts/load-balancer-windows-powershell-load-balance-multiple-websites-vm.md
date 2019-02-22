---
title: Exemplo do PowerShell – balanceamento de carga de múltiplos sites com o Azure PowerShell | Microsoft Docs
description: Este exemplo de script do Azure PowerShell mostra como fazer o balanceamento de carga de múltiplos sites para a mesma máquina virtual
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.openlocfilehash: 6bedba2c68c05ee7ed6518996e54d007f3e83dc1
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56591647"
---
# <a name="azure-powershell-script-example-load-balance-multiple-websites"></a>Exemplo de script do PowerShell do Azure: Vários Web sites de balanceamento de carga

O exemplo de script do Azure PowerShell cria uma rede virtual com duas máquinas virtuais (VM) que são membros de um conjunto de disponibilidade. Um balanceador de carga direciona o tráfego de dois endereços IP separados para as duas VMs. Depois de executar o script, pode implementar o software de servidor Web para as VMs e alojar múltiplos sites, cada um com o seu próprio endereço IP.

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.ps1  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma rede virtual, um balanceador de carga e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | Cria um conjunto de disponibilidade do Azure para fornecer elevada disponibilidade. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Cria uma configuração de sub-rede. Esta configuração é utilizada com o processo de criação de rede virtual. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Cria uma configuração do IP de front-end para um balanceador de carga. |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Cria uma configuração do conjunto de endereços de back-end para um balanceador de carga. |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Cria uma pesquisa NLB. É utilizada uma pesquisa NLB para monitorizar cada VM no conjunto do NLB. Se qualquer VM ficar inacessível, o tráfego não é encaminhado para a VM. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Cria uma regra do NLB. Neste exemplo, é criada uma regra para a porta 80. À medida que o tráfego HTTP chega ao NLB, é encaminhado para a porta 80 de uma das VMs no conjunto do NLB. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Cria um balanceador de carga. |
| [New-AzNetworkInterfaceIpConfig](/powershell/module/az.network/new-aznetworkinterfaceipconfig) | Define funcionalidades avançadas para uma interface de rede virtual. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Cria uma interface de rede. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Cria uma configuração de VM. Esta configuração inclui informações como o nome da VM, sistema operativo e credenciais administrativas. A configuração é utilizada durante a criação da VM. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Cria uma máquina virtual. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Encontrará exemplos adicionais de scripts do PowerShell de redes na [Documentação de Descrição Geral de Redes do Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
