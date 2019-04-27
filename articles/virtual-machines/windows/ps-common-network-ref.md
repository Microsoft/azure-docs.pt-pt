---
title: Comandos comuns do PowerShell para redes virtuais do Azure | Documentos da Microsoft
description: Comandos comuns do PowerShell para ajudá-lo à criação de uma rede virtual e respetivos recursos associados para as VMs.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 020f2a4171a5bd656e53c91e59edb16931b20d0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597666"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Comandos comuns do PowerShell para redes virtuais do Azure

Se quiser criar uma máquina virtual, tem de criar uma [rede virtual](../../virtual-network/virtual-networks-overview.md) ou saber sobre a rede virtual existente no qual a VM pode ser adicionada. Normalmente, quando cria uma VM, terá também de considerar a criação dos recursos descritos neste artigo.

Veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a subscrição que quer utilizar e iniciar sessão na sua conta do Azure.

Algumas variáveis podem ser útil para, se executar mais de um dos comandos neste artigo:

- $location - a localização dos recursos de rede. Pode usar [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) para localizar um [região geográfica](https://azure.microsoft.com/regions/) que funciona melhor para si.
- $myResourceGroup - o nome do grupo de recursos onde estão localizados os recursos de rede.

## <a name="create-network-resources"></a>Criar recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Criar configurações de sub-rede |$subnet1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>Uma rede típica pode ter uma sub-rede para um [internet Balanceador de carga com acesso à](../../load-balancer/load-balancer-internet-overview.md) e uma sub-rede separada para uma [Balanceador de carga interno](../../load-balancer/load-balancer-internal-overview.md). |
| Criar uma rede virtual |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Teste para um nome de domínio exclusivo |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>Pode especificar um nome de domínio DNS para um [recurso de IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), que cria um mapeamento para domainname.location.cloudapp.azure.com para o endereço IP público nos servidores DNS geridos pelo Azure. O nome só pode conter letras, números e hífenes. O primeiro e último caráter tem de ser uma letra ou número e o nome de domínio tem de ser exclusivos dentro da respetiva localização do Azure. Se **True** é devolvido o nome proposto é exclusivo globalmente. |
| Crie um endereço IP público |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>O endereço IP público utiliza o nome de domínio que tiver testado e é utilizado pela configuração de front-end do Balanceador de carga. |
| Criar uma configuração de IP de front-end |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>A configuração de front-end inclui o endereço IP público que criou anteriormente para o tráfego de rede recebido. |
| Criar um conjunto de endereços de back-end |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Fornece endereços internos para o back-end de Balanceador de carga que são acedidos através de uma interface de rede. |
| Criar uma sonda |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Contém sondas utilizadas para verificar a disponibilidade de instâncias de máquinas virtuais no conjunto de endereços de back-end. |
| Criar uma regra de balanceamento de carga |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Contém regras que atribuir uma porta pública no balanceador de carga para uma porta no conjunto de endereços de back-end. |
| Criar uma regra NAT de entrada |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Contém regras que mapeiam uma porta pública no balanceador de carga para uma porta de uma máquina virtual específica no conjunto de endereços de back-end. |
| Criar um balanceador de carga |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) - ResourceGroupName $myResourceGroup-Name "myLoadBalancer"-localização $location - FrontendIpConfiguration $frontendIP - InboundNatRule $inboundNATRule - LoadBalancingRule $lbRule - BackendAddressPool $beAddressPool-$healthProbe de sonda |
| Criar uma interface de rede |US $nic1 = [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) - ResourceGroupName $myResourceGroup-Name "myNIC" - localização $location - PrivateIpAddress XX. X.X.X-sub-rede us $subnet2 - LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] - LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Crie uma interface de rede com o endereço IP público e a sub-rede de rede virtual que criou anteriormente. |

## <a name="get-information-about-network-resources"></a>Obter informações sobre recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Lista de redes virtuais |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Apresenta uma lista de todas as redes virtuais no grupo de recursos. |
| Obter informações sobre uma rede virtual |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Lista de sub-redes numa rede virtual |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Obter informações sobre uma sub-rede |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Obtém informações sobre a sub-rede na rede virtual especificada. O valor de $vnet representa o objeto devolvido pelo Get-AzVirtualNetwork. |
| Endereços IP da lista |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Lista os endereços IP públicos no grupo de recursos. |
| Balanceadores de carga de lista |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Apresenta uma lista de todos os balanceadores de carga no grupo de recursos. |
| Interfaces de rede de lista |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Apresenta uma lista de todas as interfaces de rede no grupo de recursos. |
| Obter informações sobre uma interface de rede |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Obtém informações sobre uma interface de rede específicas. |
| Obter a configuração de IP de uma interface de rede |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>Obtém informações sobre a configuração de IP da interface de rede especificado. O valor de $nic representa o objeto devolvido pelo Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Gerir recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Adicionar uma sub-rede a uma rede virtual |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Adiciona uma sub-rede para uma rede virtual existente. O valor de $vnet representa o objeto devolvido pelo Get-AzVirtualNetwork. |
| Eliminar uma rede virtual |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Remove a rede virtual especificada do grupo de recursos. |
| Eliminar uma interface de rede |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Remove a interface de rede especificada do grupo de recursos. |
| Eliminar um balanceador de carga |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Remove o grupo de recursos do Balanceador de carga especificado. |
| Eliminar um endereço IP público |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Remove o endereço IP público especificado do grupo de recursos. |

## <a name="next-steps"></a>Próximos Passos
* Utilizar a interface de rede que acabou de criar quando [criar uma VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Saiba mais sobre como pode [criar uma VM com várias interfaces de rede](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

