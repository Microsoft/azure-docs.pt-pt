---
title: Comandos comuns do PowerShell para redes virtuais do Azure
description: Comandos comuns do PowerShell para ajudá-lo a começar a criar uma rede virtual e seus recursos associados para VMs.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 3abde706ddff297094c7fbb1579b534894b349d2
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032922"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Comandos comuns do PowerShell para redes virtuais do Azure

Se você quiser criar uma máquina virtual, precisará criar uma [rede virtual](../../virtual-network/virtual-networks-overview.md) ou saber sobre uma rede virtual existente na qual a VM pode ser adicionada. Normalmente, ao criar uma VM, você também precisa considerar a criação dos recursos descritos neste artigo.

Veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a subscrição que quer utilizar e iniciar sessão na sua conta do Azure.

Algumas variáveis podem ser úteis para você se executar mais de um dos comandos neste artigo:

- $location-o local dos recursos de rede. Você pode usar [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) para localizar uma [região geográfica](https://azure.microsoft.com/regions/) que funcione para você.
- $myResourceGroup-o nome do grupo de recursos em que os recursos de rede estão localizados.

## <a name="create-network-resources"></a>Criar recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Criar configurações de sub-rede |$subnet 1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1"-AddressPrefix XX. X. X. X/XX<BR>$subnet 2 = New-AzVirtualNetworkSubnetConfig-Name "mySubnet2"-AddressPrefix XX. X. X. X/XX<BR><BR>Uma rede típica pode ter uma sub-rede para um [balanceador de carga voltado](../../load-balancer/load-balancer-internet-overview.md) para a Internet e uma sub-rede separada para um [balanceador de carga interno](../../load-balancer/load-balancer-internal-overview.md). |
| Criar uma rede virtual |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Testar um nome de domínio exclusivo |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>Você pode especificar um nome de domínio DNS para um [recurso IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), que cria um mapeamento para DomainName.Location.cloudapp.Azure.com para o endereço IP público nos servidores DNS gerenciados pelo Azure. O nome só pode conter letras, números e hífenes. O primeiro e o último caractere devem ser uma letra ou um número e o nome de domínio deve ser exclusivo em seu local do Azure. Se **true** for retornado, o nome proposto será globalmente exclusivo. |
| Crie um endereço IP público |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>O endereço IP público usa o nome de domínio que você testou anteriormente e é usado pela configuração de front-end do balanceador de carga. |
| Criar uma configuração de IP de front-end |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>A configuração de front-end inclui o endereço IP público que você criou anteriormente para o tráfego de rede de entrada. |
| Criar um conjunto de endereços de back-end |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Fornece endereços internos para o back-end do balanceador de carga que são acessados por meio de uma interface de rede. |
| Criar uma investigação |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Contém investigações de integridade usadas para verificar a disponibilidade de instâncias de máquinas virtuais no pool de endereços de back-end. |
| Criar uma regra de balanceamento de carga |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Contém regras que atribuem uma porta pública no balanceador de carga a uma porta no pool de endereços de back-end. |
| Criar uma regra NAT de entrada |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Contém regras que mapeiam uma porta pública no balanceador de carga para uma porta para uma máquina virtual específica no pool de endereços de back-end. |
| Criar um balanceador de carga |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $MyResourceGroup-Name "myLoadBalancer"-local $Location-FrontendIpConfiguration $FrontendIP-InboundNatRule $InboundNATRule-LoadBalancingRule $LbRule-BackendAddressPool $BeAddressPool-Probe $healthProbe |
| Criar uma interface de rede |$nic 1 = [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $MyResourceGroup-Name "myNIC" – Location $Location-PrivateIpAddress XX. X. X. X-SUBNET $subnet 2-LoadBalancerBackendAddressPool $loadBalancer. BackendAddressPools [0]-LoadBalancerInboundNatRule $loadBalancer. InboundNatRules [0]<BR><BR>Crie uma interface de rede usando o endereço IP público e a sub-rede de rede virtual que você criou anteriormente. |

## <a name="get-information-about-network-resources"></a>Obter informações sobre recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Listar redes virtuais |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Lista todas as redes virtuais no grupo de recursos. |
| Obter informações sobre uma rede virtual |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Listar sub-redes em uma rede virtual |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Obter informações sobre uma sub-rede |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1"-VirtualNetwork $vnet<BR><BR>Obtém informações sobre a sub-rede na rede virtual especificada. O valor de $vnet representa o objeto retornado por Get-AzVirtualNetwork. |
| Listar endereços IP |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Lista os endereços IP públicos no grupo de recursos. |
| Listar balanceadores de carga |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Lista todos os balanceadores de carga no grupo de recursos. |
| Listar interfaces de rede |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Lista todas as interfaces de rede no grupo de recursos. |
| Obter informações sobre uma interface de rede |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Obtém informações sobre uma interface de rede específica. |
| Obter a configuração de IP de uma interface de rede |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP"-NetworkInterface $NIC<BR><BR>Obtém informações sobre a configuração de IP da interface de rede especificada. O valor de $nic representa o objeto retornado por Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Gerir recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Adicionar uma sub-rede a uma rede virtual |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX. X. X. X/XX-Name "mySubnet1"-VirtualNetwork $vnet<BR><BR>Adiciona uma sub-rede a uma rede virtual existente. O valor de $vnet representa o objeto retornado por Get-AzVirtualNetwork. |
| Excluir uma rede virtual |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Remove a rede virtual especificada do grupo de recursos. |
| Excluir uma interface de rede |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC"-ResourceGroupName $myResourceGroup<BR><BR>Remove a interface de rede especificada do grupo de recursos. |
| Eliminar um balanceador de carga |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Remove o balanceador de carga especificado do grupo de recursos. |
| Excluir um endereço IP público |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Remove o endereço IP público especificado do grupo de recursos. |

## <a name="next-steps"></a>Passos Seguintes
* Use a interface de rede que você acabou de criar ao [criar uma VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Saiba mais sobre como você pode [criar uma VM com várias interfaces de rede](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

