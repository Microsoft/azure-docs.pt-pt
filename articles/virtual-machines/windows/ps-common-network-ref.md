---
title: Comandos Common PowerShell para redes virtuais Azure
description: O Common PowerShell ordena que você começou a criar uma rede virtual e seus recursos associados para VMs.
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
ms.openlocfilehash: b9debe04759772ef51946dc99943ec4eff6f61dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77911836"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Comandos Common PowerShell para redes virtuais Azure

Se quiser criar uma máquina virtual, precisa de criar uma [rede virtual](../../virtual-network/virtual-networks-overview.md) ou saber sobre uma rede virtual existente na qual o VM pode ser adicionado. Normalmente, quando se cria um VM, também é necessário considerar a criação dos recursos descritos neste artigo.

Veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a subscrição que quer utilizar e iniciar sessão na sua conta do Azure.

Algumas variáveis podem ser úteis para si se executar mais do que um dos comandos neste artigo:

- $location - A localização dos recursos da rede. Você pode usar [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) para encontrar uma [região geográfica](https://azure.microsoft.com/regions/) que funcione para si.
- $myResourceGroup - O nome do grupo de recursos onde estão localizados os recursos da rede.

## <a name="create-network-resources"></a>Criar recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Criar configurações de sub-rede |$subnet1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX. X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX. X.X.X/XX<BR><BR>Uma rede típica pode ter uma sub-rede para um [equilibrador de carga virado](../../load-balancer/load-balancer-internet-overview.md) para a Internet e uma sub-rede separada para um [equilibrador de carga interno](../../load-balancer/load-balancer-internal-overview.md). |
| Criar uma rede virtual |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Localização $location -AddressPrefix XX. X.X.X/XX -Subnet $subnet1, $subnet2 |
| Teste para um nome de domínio único |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameNameLabel "myDNS" -Localização $location<BR><BR>Pode especificar um nome de domínio DNS para um [recurso IP público,](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)que cria um mapeamento para domainname.location.cloudapp.azure.com para o endereço IP público nos servidores DNS geridos pelo Azure. O nome só pode conter letras, números e hífenes. O primeiro e último caracteres deve ser uma letra ou número e o nome de domínio deve ser único dentro da sua localização Azure. Se **true** for devolvido, o seu nome proposto é globalmente único. |
| Crie um endereço IP público |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>O endereço IP público utiliza o nome de domínio que testou anteriormente e é utilizado pela configuração frontal do equilibrador de carga. |
| Criar uma configuração IP frontal |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>A configuração frontal inclui o endereço IP público que criou anteriormente para o tráfego de rede de entrada. |
| Criar um conjunto de endereços de back-end |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Fornece endereços internos para o backend do equilibrador de carga que são acedidos através de uma interface de rede. |
| Criar uma sonda |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Porta 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Contém sondas de saúde usadas para verificar a disponibilidade de máquinas virtuais casos na piscina de endereços de backend. |
| Criar uma regra de equilíbrio de carga |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Contém regras que atribuem uma porta pública no equilibrador de carga a uma porta na piscina de endereços de backend. |
| Criar uma regra nat de entrada |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Nome "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Contém regras que mapeiam uma porta pública no equilibrador de carga para uma porta para uma máquina virtual específica na piscina de endereços traseiros. |
| Criar um balanceador de carga |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Localização $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Sonda $healthProbe |
| Criar uma interface de rede |$nic1= [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Location $location -PrivateIpAddress XX. X.X.X -Subnet $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Crie uma interface de rede utilizando o endereço IP público e a subnet de rede virtual que criou anteriormente. |

## <a name="get-information-about-network-resources"></a>Obtenha informações sobre recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Listar redes virtuais |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Lista todas as redes virtuais do grupo de recursos. |
| Obtenha informações sobre uma rede virtual |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Listar subredes numa rede virtual |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Obtenha informações sobre uma sub-rede |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Obtém informações sobre a sub-rede na rede virtual especificada. O valor $vnet representa o objeto devolvido pela Get-AzVirtualNetwork. |
| Lista de endereços IP |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Lista os endereços IP públicos no grupo de recursos. |
| Lista de equilibradores de carga |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Lista todos os equilibradores de carga no grupo de recursos. |
| Lista de interfaces de rede |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Lista todas as interfaces de rede no grupo de recursos. |
| Obtenha informações sobre uma interface de rede |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Obtém informações sobre uma interface de rede específica. |
| Obtenha a configuração IP de uma interface de rede |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Nome "myNICIP" -NetworkInterface $nic<BR><BR>Obtém informações sobre a configuração IP da interface de rede especificada. O valor $nic representa o objeto devolvido pela Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Gerir recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Adicione uma subnet a uma rede virtual |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX. X.X.X/XX -Nome "mySubnet1" -Rede Virtual $vnet<BR><BR>Adiciona uma sub-rede a uma rede virtual existente. O valor $vnet representa o objeto devolvido pela Get-AzVirtualNetwork. |
| Eliminar uma rede virtual |[Remover-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Nome "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Remove a rede virtual especificada do grupo de recursos. |
| Eliminar uma interface de rede |[Remover-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Nome "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Remove a interface de rede especificada do grupo de recursos. |
| Eliminar um balanceador de carga |[Remover -AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Nome "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Remove o equilibrador de carga especificado do grupo de recursos. |
| Apagar um endereço IP público |[Remover-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Nome "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Remove o endereço IP público especificado do grupo de recursos. |

## <a name="next-steps"></a>Passos Seguintes
Utilize a interface de rede que acabou de criar quando [cria um VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


