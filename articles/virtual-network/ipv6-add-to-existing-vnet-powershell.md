---
title: Atualize uma aplicação IPv4 para IPv6 em Azure Virtual Network - PowerShell
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implantar endereços IPv6 para uma aplicação existente na rede virtual Azure usando a Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9d2ff583b032ff1f9aa5dbc9706ea6c981fc7265
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2020
ms.locfileid: "96009981"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>Atualize uma aplicação IPv4 para IPv6 na rede virtual Azure - PowerShell

Este artigo mostra-lhe como adicionar conectividade IPv6 a uma aplicação IPv4 existente numa rede virtual Azure com um Balancer de Carga Standard e IP Público. A atualização no local inclui:
- Espaço de endereço IPv6 para a rede virtual e sub-rede
- um Balanceador de Carga Padrão com configurações de frontend IPv4 e IPV6
- VMs com NICs que têm uma configuração IPv4 + IPv6
- IP público IPv6 para que o equilibrista de carga tenha conectividade IPv6 virada para a Internet

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 6.9.0 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que implementou um Balanceador de Carga Standard como descrito no [Quickstart: Criar um Balanceador de Carga Padrão - Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-public-powershell.md).

## <a name="retrieve-the-resource-group"></a>Recuperar o grupo de recursos

Antes de criar a sua rede virtual de dupla pilha, tem de recuperar o grupo de recursos com o [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell-interactive
$rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>Criar endereços IP IPv6

Crie um endereço IPv6 público com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para o seu Balancer de Carga Padrão. O exemplo a seguir cria um endereço IP público IPv6 nomeado *PublicIP_v6* no grupo de recursos *myResourceGroupSLB:*

```azurepowershell-interactive  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Configurar o balanço de carga frontend

Recupere a configuração do balançador de carga existente e adicione o novo endereço IP IPv6 utilizando [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) da seguinte forma:

```azurepowershell-interactive
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"

# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>Configure a reserva do balançador de carga

Crie o pool de backend na cópia local da configuração do balançador de carga e atualize o balançador de carga em funcionamento com a nova configuração do pool de backend da seguinte forma:

```azurepowershell-interactive
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"

# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Configurar as regras do balanceador de carga
Recupere a configuração de bancada frontal e backend existente e adicione novas regras de equilíbrio de carga utilizando [Add-AzLoadBalanceruleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig).

```azurepowershell-interactive
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb

# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>Adicionar intervalos de endereços IPv6

Adicione intervalos de endereços IPv6 à rede virtual e sub-rede que hospedam os VMs da seguinte forma:

```azurepowershell-interactive
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 

#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("fd00:db8:deca::/48")

#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]

#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("fd00:db8:deca::/64")

#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork
```

## <a name="add-ipv6-configuration-to-nic"></a>Adicione a configuração IPv6 ao NIC

Configure todos os NICs VM com um endereço IPv6 utilizando [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) da seguinte forma:

```azurepowershell-interactive
#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName

#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface

#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface

#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Ver rede virtual de pilha dupla IPv6 no portal Azure

Pode ver a rede virtual de pilha dupla IPv6 no portal Azure da seguinte forma:
1. Na barra de pesquisa do portal, insira *o myVnet*.
2. Quando **o myVnet** aparecer nos resultados da pesquisa, selecione-o. Isto lança a página **geral** da rede virtual dual stack chamada *myVNet*. A rede virtual dual stack mostra os três NICs com configurações IPv4 e IPv6 localizadas na sub-rede de pilha dupla chamada *mySubnet*.

  ![IPv6 dual stack rede virtual em Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, atualizou um Balancer de Carga Standard existente com uma configuração IP frontal IPv4 para uma configuração de pilha dupla (IPv4 e IPv6). Também adicionou configurações IPv6 aos NICs dos VMs na piscina de backend e à Rede Virtual que as acolhe. Para saber mais sobre o suporte do IPv6 nas redes virtuais Azure, veja [o que é o IPv6 para a Rede Virtual Azure?](ipv6-overview.md)
