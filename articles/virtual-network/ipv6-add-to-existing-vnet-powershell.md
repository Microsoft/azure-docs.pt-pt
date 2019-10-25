---
title: Atualizar um aplicativo IPv4 para IPv6 na rede virtual do Azure-PowerShell
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implantar endereços IPv6 em um aplicativo existente na rede virtual do Azure usando o Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/21/2019
ms.author: kumud
ms.openlocfilehash: 47f73ca8ece8db5fad3f8a7709d8787db42626f4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791189"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell-preview"></a>Atualizar um aplicativo IPv4 para IPv6 na rede virtual do Azure-PowerShell (versão prévia)

Este artigo mostra como adicionar endereços IPv6 a um aplicativo que está usando o endereço IP público IPv4 em uma rede virtual do Azure para um Standard Load Balancer. A atualização in-loco inclui uma rede virtual e uma sub-rede, uma Standard Load Balancer com configurações de front-end IPv4 + IPV6, VMs com NICs que têm configurações IPv4 + IPv6, grupo de segurança de rede e IPs públicos.

> [!Important]
> O suporte a IPv6 para a rede virtual do Azure está atualmente em visualização pública. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o Azure PowerShell módulo versão 6.9.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="register-the-service"></a>Registrar o serviço

Antes de implantar um aplicativo de pilha dupla no Azure, você deve configurar sua assinatura para esse recurso de visualização usando as seguintes Azure PowerShell:

Registre-se da seguinte maneira:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Demora até 30 minutos para que o registro do recurso seja concluído. Você pode verificar o status do Registro executando o seguinte comando de Azure PowerShell: Verifique o registro da seguinte maneira:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Após a conclusão do registro, execute o seguinte comando:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>Criar um Balanceador de Carga Standard
Este artigo pressupõe que você implantou um Standard Load Balancer conforme descrito em [início rápido: criar um Standard Load Balancer-Azure PowerShell](../load-balancer/quickstart-create-standard-load-balancer-powershell.md).

## <a name="retrieve-the-resource-group"></a>Recuperar o grupo de recursos

Para poder criar sua rede virtual de pilha dupla, você deve recuperar o grupo de recursos com [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>Criar endereços IP IPv6

Crie um endereço IPv6 público com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para seu Standard Load Balancer. O exemplo a seguir cria um endereço IP público IPv6 chamado *PublicIP_v6* no grupo de recursos *myResourceGroupSLB* :

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Configurar front-end do balanceador de carga

Recupere a configuração do balanceador de carga existente e configure-a com o novo endereço IP IPv6 usando [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) da seguinte maneira:

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>Configurar pool de back-end do balanceador de carga

Crie o pool de back-end na cópia local da configuração do balanceador de carga e atualize o balanceador de carga em execução com a nova configuração de pool de back-end da seguinte maneira:

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Configurar regras do balanceador de carga
Recupere a configuração do pool de back-end e do balanceador de carga existente e adicione novas regras de balanceamento de carga usando [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig).

```azurepowershell
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

Adicione intervalos de endereços IPv6 à rede virtual e à sub-rede que hospeda o balanceador de carga da seguinte maneira:

```azurepowershell
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 
#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")
#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]
#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")
#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork

```
## <a name="add-ipv6-configuration-to-nic"></a>Adicionar configuração IPv6 à NIC

Configure as duas NICs de VM com um endereço IPv6 usando [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) da seguinte maneira:

```azurepowershell

#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName
#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Exibir rede virtual de pilha dupla IPv6 no portal do Azure
Você pode exibir a rede virtual de pilha dupla IPv6 em portal do Azure da seguinte maneira:
1. Na barra de pesquisa do portal, insira *myVnet*.
2. Quando **myVnet** aparecer nos resultados da pesquisa, selecione-o. Isso inicia a página **visão geral** da rede virtual de pilha dupla chamada *myVNet*. A rede virtual de pilha dupla mostra as três NICs com as configurações de IPv4 e IPv6 localizadas na sub-rede de pilha dupla chamada *mysubnet*.

  ![Rede virtual de pilha dupla IPv6 no Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> O IPv6 para rede virtual do Azure está disponível na portal do Azure em somente leitura para esta versão de visualização.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você atualizou um Standard Load Balancer existente com uma configuração de IP de front-end IPv4 para uma configuração de pilha dupla (IPv4 e IPv6). Você também adicionou configurações de IPv6 às NICs das VMs no pool de back-end. Para saber mais sobre o suporte a IPv6 em redes virtuais do Azure, consulte [o que é IPv6 para a rede virtual do Azure?](ipv6-overview.md)
