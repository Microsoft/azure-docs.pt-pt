---
title: Exemplo do PowerShell - balancear carga de tráfego para VMs de elevada disponibilidade - Azure
titlesuffix: Azure Load Balancer
description: Este Exemplo de Script do Azure PowerShell mostra como efetuar o balanceamento de carga de tráfego para VMs para elevada disponibilidade
services: load-balancer
documentationcenter: load-balancer
author: KumudD
ms.service: load-balancer
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.openlocfilehash: 70752f9726ac78ec32d0735dfefe37675ae7be29
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56592473"
---
# <a name="azure-powershell-script-example-load-balance-traffic-to-vms-for-high-availability"></a>Exemplo de script do PowerShell do Azure: Balancear carga de tráfego para VMs de elevada disponibilidade

Este exemplo de script do Azure PowerShell cria tudo o que é preciso para executar várias máquinas virtuais do Windows configuradas numa configuração de elevada disponibilidade e com balanceamento de carga. Depois de executar o script, terá três máquinas virtuais, associadas a um Conjunto de Disponibilidade do Azure e acessíveis através de um Balanceador de Carga do Azure.

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Quick Create VM")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual, um conjunto de disponibilidade, um balanceador de carga e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Cria uma configuração de sub-rede. Esta configuração é utilizada com o processo de criação de rede virtual. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede e sub-rede virtual do Azure. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)  | Cria um endereço IP público com um endereço IP estático e um nome DNS associado. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)  | Cria um balanceador de carga do Azure. |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Cria uma sonda do balanceador de carga. Uma sonda do balanceador de carga serve para monitorizar cada VM no conjunto de balanceadores de carga. Se qualquer VM ficar inacessível, o tráfego não é encaminhado para a VM. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Cria uma regra de balanceador de carga. Neste exemplo, é criada uma regra para a porta 80. À medida que o tráfego HTTP chega ao balanceador de carga, é encaminhado para a porta 80 de uma das VMs no conjunto de balanceadores de carga. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Cria uma regra de Tradução de Endereços de Rede (NAT) do balanceador de carga.  As regras NAT mapeiam uma porta do balanceador de carga para uma porta numa VM. Neste exemplo, é criada uma regra NAT para o tráfego SSH para cada VM no conjunto de balanceadores de carga.  |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria um grupo de segurança de rede (NSG), que é um limite de segurança entre a Internet e a máquina virtual. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Cria uma regra NSG para permitir tráfego de entrada. Neste exemplo, a porta 22 está aberta para o tráfego SSH. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Cria a placa da rede virtual e liga-a à rede virtual, à sub-rede e ao NSG. |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | Cria um conjunto de disponibilidade. Os conjuntos de disponibilidade garantem o tempo de atividade das aplicações ao propagar as máquinas virtuais nos recursos físicos de modo a que, se ocorrer uma falha, o conjunto completo não seja afetado. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Cria uma configuração de VM. Esta configuração inclui informações como o nome da VM, sistema operativo e credenciais administrativas. A configuração é utilizada durante a criação da VM. |
| [New-AzVM](/powershell/module/az.compute/new-azvm)  | Cria a máquina virtual e liga-a à placa de rede, à rede virtual, à sub-rede e ao NSG. Este comando também especifica a imagem de máquina virtual a ser utilizada e as credenciais administrativas.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Encontrará exemplos adicionais de scripts do PowerShell de redes na [Documentação de Descrição Geral de Redes do Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
