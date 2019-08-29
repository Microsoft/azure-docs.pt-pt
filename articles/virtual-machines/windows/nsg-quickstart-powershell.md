---
title: Abrir portas para uma VM usando Azure PowerShell | Microsoft Docs
description: Saiba como abrir uma porta/criar um ponto de extremidade para sua VM do Windows usando o modo de implantação do Azure Resource Manager e Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: cd5aab6934e2f9692411e09046722cd59ad5e6a8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089110"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Como abrir portas e pontos de extremidade para uma VM no Azure usando o PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandos rápidos
Para criar um grupo de segurança de rede e as regras de ACL, você precisa [da versão mais recente do Azure PowerShell instalada](/powershell/azureps-cmdlets-docs). Você também pode [executar essas etapas usando o portal do Azure](nsg-quickstart-portal.md).

Faça logon em sua conta do Azure:

```powershell
Connect-AzAccount
```

Nos exemplos a seguir, substitua os nomes de parâmetro pelos seus próprios valores. Exemplos de nomes deparâmetro incluem MyResource, *myNetworkSecurityGroup*e *myVnet*.

Crie uma regra com [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig). O exemplo a seguir cria uma regra chamada *myNetworkSecurityGroupRule* para permitir o tráfego *tcp* na porta *80*:

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Em seguida, crie seu grupo de segurança de rede com [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) e atribua a regra http que você acabou de criar da seguinte maneira. O exemplo a seguir cria um grupo de segurança de rede chamado *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Agora, vamos atribuir seu grupo de segurança de rede a uma sub-rede. O exemplo a seguir atribui uma rede virtual existente chamada *myVnet* à variável *$vnet* com [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork):

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associe o grupo de segurança de rede à sua sub-rede com [set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig). O exemplo a seguir associa a sub-rede chamada mysubnet ao seu grupo de segurança de rede:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Por fim, atualize sua rede virtual com [set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) para que as alterações entrem em vigor:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Mais informações sobre grupos de segurança de rede
Os comandos rápidos aqui permitem que você comece a executar o tráfego que flui para sua VM. Os grupos de segurança de rede fornecem muitos ótimos recursos e granularidade para controlar o acesso aos seus recursos. Você pode ler mais sobre como [criar um grupo de segurança de rede e regras de ACL aqui](tutorial-virtual-network.md#secure-network-traffic).

Para aplicativos Web altamente disponíveis, você deve posicionar suas VMs por trás de um Azure Load Balancer. O balanceador de carga distribui o tráfego para VMs, com um grupo de segurança de rede que fornece filtragem de tráfego. Para obter mais informações, consulte [como balancear a carga de máquinas virtuais Linux no Azure para criar um aplicativo altamente disponível](tutorial-load-balancer.md).

## <a name="next-steps"></a>Passos Seguintes
Neste exemplo, você criou uma regra simples para permitir o tráfego HTTP. Você pode encontrar informações sobre como criar ambientes mais detalhados nos seguintes artigos:

* [Descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [O que é um grupo de segurança de rede?](../../virtual-network/security-overview.md)
* [Visão geral de Azure Resource Manager para balanceadores de carga](../../load-balancer/load-balancer-arm.md)

