---
title: Comandos Common PowerShell para Redes Virtuais Azure
description: O Common PowerShell ordena que você começa a criar uma rede virtual e os seus recursos associados para VMs.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: b4d6b20e63c42616aad0f8776fae159a0f2aa455
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87088381"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Comandos Common PowerShell para Redes Virtuais Azure

Se pretender criar uma máquina virtual, precisa de criar uma [rede virtual](../../virtual-network/virtual-networks-overview.md) ou de conhecer uma rede virtual existente na qual o VM pode ser adicionado. Normalmente, quando se cria um VM, também é necessário considerar a criação dos recursos descritos neste artigo.

Veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a subscrição que quer utilizar e iniciar sessão na sua conta do Azure.

Algumas variáveis podem ser úteis para si se executar mais do que um dos comandos neste artigo:

- $location - A localização dos recursos da rede. Você pode usar [a Get-AzLocation](/powershell/module/az.resources/get-azlocation) para encontrar uma [região geográfica](https://azure.microsoft.com/regions/) que funcione para si.
- $myResourceGroup - O nome do grupo de recursos onde se encontram os recursos da rede.

## <a name="create-network-resources"></a>Criar recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Criar configurações de sub-rede |$subnet 1 = [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet 2 = New-AzVirtualNetworkSubnetConfig -Nome "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>Uma rede típica pode ter uma sub-rede para um [equilibrador de carga virado para a internet](../../load-balancer/load-balancer-overview.md) e uma sub-rede separada para um [equilibrador de carga interno](../../load-balancer/load-balancer-overview.md). |
| Criar uma rede virtual |$vnet = [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) -Nome "myVNet" -ResourceGroupName $myResourceGroup -Localização $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet 1, $subnet 2 |
| Teste para um nome de domínio único |[Test-AzDnsAvailability](/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Localização $location<BR><BR>Pode especificar um nome de domínio DNS para um [recurso IP público,](../../virtual-network/public-ip-addresses.md)que cria um mapeamento para domainname.location.cloudapp.azure.com para o endereço IP público nos servidores DNS geridos pelo Azure. O nome só pode conter letras, números e hífenes. O primeiro e último caracteres devem ser uma letra ou número e o nome de domínio deve ser único dentro da sua localização Azure. Se **true** for devolvido, o seu nome proposto é globalmente único. |
| Crie um endereço IP público |$pip = [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) -Nome "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Localização $location -AllocationMethod Dynamic<BR><BR>O endereço IP público utiliza o nome de domínio que testou anteriormente e é utilizado pela configuração frontal do balançador de carga. |
| Criar uma configuração IP frontend |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Nome "myFrontendIP" - PublicIpAddress $pip<BR><BR>A configuração frontend inclui o endereço IP público que criou anteriormente para o tráfego de rede de entrada. |
| Criar um conjunto de endereços de back-end |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Fornece endereços internos para o backend do equilibrador de carga que são acedidos através de uma interface de rede. |
| Criar uma sonda |$healthProbe = [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) -Nome "myProbe" -RequestPath 'HealthProbe.aspx' -Protocolo http -Porta 80 -IntervalInSeconds 15 -SondaCount 2<BR><BR>Contém sondas de saúde usadas para verificar a disponibilidade de casos de máquinas virtuais no pool de endereços backend. |
| Criar uma regra de equilíbrio de carga |$lbRule = [New-AzLoadBalanceruleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Sonda $healthProbe -Protocolo Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Contém regras que atribuem uma porta pública no balançador de carga a uma porta na piscina de endereços de backend. |
| Criar uma regra NAT de entrada |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Contém regras que mapeam uma porta pública no equilibrador de carga para uma porta para uma máquina virtual específica na piscina de endereços de backend. |
| Criar um balanceador de carga |$loadBalancer = [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Localização $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Sonda $healthProbe |
| Criar uma interface de rede |$nic 1= [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Localização $location -PrivateIpAddress XX.X.X -Subnet $subnet 2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Crie uma interface de rede utilizando o endereço IP público e a sub-rede de rede virtual que criou anteriormente. |

## <a name="get-information-about-network-resources"></a>Obtenha informações sobre recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Listar redes virtuais |[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Lista todas as redes virtuais do grupo de recursos. |
| Obtenha informações sobre uma rede virtual |Get-AzVirtualNetwork -Nome "myVNet" - Nome do Grupo de Recursos $myResourceGroup |
| Listar sub-redes numa rede virtual |Get-AzVirtualNetwork -Nome "myVNet" -ResourceGroupName $myResourceGroup &#124; Selecione Sub-redes |
| Obtenha informações sobre uma sub-rede |[Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Nome "mySubnet1" - VirtualNetwork $vnet<BR><BR>Obtém informações sobre a sub-rede na rede virtual especificada. O valor $vnet representa o objeto devolvido pela Get-AzVirtualNetwork. |
| Lista de endereços IP |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Lista os endereços IP públicos no grupo de recursos. |
| Listar equilibradores de carga |[Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Lista todos os equilibradores de carga do grupo de recursos. |
| Listar interfaces de rede |[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Lista todas as interfaces de rede do grupo de recursos. |
| Obtenha informações sobre uma interface de rede |Get-AzNetworkInterface -Nome "myNIC" - ResourceGroupName $myResourceGroup<BR><BR>Obtém informações sobre uma interface de rede específica. |
| Obtenha a configuração IP de uma interface de rede |[Get-AzNetworkInterfaceIPConfig](/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Nome "myNICIP" -NetworkInterface $nic<BR><BR>Obtém informações sobre a configuração IP da interface de rede especificada. O valor $nic representa o objeto devolvido pela Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Gerir recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Adicione uma sub-rede a uma rede virtual |[Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX.X.X/XX -Nome "mySubnet1" - VirtualNetwork $vnet<BR><BR>Adiciona uma sub-rede a uma rede virtual existente. O valor $vnet representa o objeto devolvido pela Get-AzVirtualNetwork. |
| Eliminar uma rede virtual |[Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork) -Nome "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Remove a rede virtual especificada do grupo de recursos. |
| Eliminar uma interface de rede |[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface) -Nome "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Remove a interface de rede especificada do grupo de recursos. |
| Eliminar um balanceador de carga |[Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer) -Nome "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Remove o equilibrador de carga especificado do grupo de recursos. |
| Eliminar um endereço IP público |[Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress)-Nome "myIPAddress" - Nome do Grupo de Recursos $myResourceGroup<BR><BR>Remove o endereço IP público especificado do grupo de recursos. |

## <a name="next-steps"></a>Passos Seguintes
Utilize a interface de rede que acabou de criar quando [cria um VM](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json).
