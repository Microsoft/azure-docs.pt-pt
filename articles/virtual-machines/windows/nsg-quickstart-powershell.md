---
title: Abrir portas a um VM usando Azure PowerShell
description: Saiba como abrir uma porta / crie um ponto final para o seu VM utilizando a Azure PowerShell
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a432ce978f6fa9e3a472cb15e9ef9241bc41004d
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891759"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-using-powershell"></a>Como abrir portas e pontos finais para um VM usando PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandos rápidos
Para criar um Grupo de Segurança de Rede e regras ACL, precisa [da versão mais recente do Azure PowerShell instalada](/powershell/azure/). Também pode [executar estes passos utilizando o portal Azure](nsg-quickstart-portal.md).

Inicie sessão na sua conta do Azure:

```powershell
Connect-AzAccount
```

Nos exemplos seguintes, substitua os nomes dos parâmetros pelos seus próprios valores. Os nomes dos parâmetros incluem *myResourceGroup,* *myNetworkSecurityGroup* e *myVnet* .

Crie uma regra com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). O exemplo a seguir cria uma regra chamada *myNetworkSecurityGroupRule* para permitir o tráfego *tcp* na porta *80* :

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

Em seguida, crie o seu grupo de Segurança de Rede com [o New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) e atribua a regra HTTP que acabou de criar da seguinte forma. O exemplo a seguir cria um Grupo de Segurança de Rede chamado *myNetworkSecurityGroup:*

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Agora vamos atribuir o seu Grupo de Segurança de Rede a uma sub-rede. O exemplo a seguir atribui uma rede virtual existente chamada *myVnet* à variável *$vnet* com [a Get-AzVirtualNetwork:](/powershell/module/az.network/get-azvirtualnetwork)

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associe o seu Grupo de Segurança de Rede à sua [sub-rede com o Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig). O exemplo a seguir associa a sub-rede denominada *mySubnet* ao seu Grupo de Segurança de Rede:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Por fim, atualize a sua rede virtual com [a Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) para que as suas alterações entrem em vigor:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Mais informações sobre grupos de segurança de rede
Os comandos rápidos aqui permitem-lhe levantar-se e funcionar com o tráfego a fluir para o seu VM. Os Grupos de Segurança de Rede fornecem muitas funcionalidades e granularidade para controlar o acesso aos seus recursos. Pode ler mais sobre [a criação de regras do Network Security Group e da ACL aqui.](tutorial-virtual-network.md#secure-network-traffic)

Para aplicações web altamente disponíveis, deve colocar os seus VMs atrás de um Balançador de Carga Azure. O equilibrador de carga distribui o tráfego para VMs, com um Grupo de Segurança de Rede que fornece filtragem de tráfego. Para obter mais informações, consulte [como carregar as máquinas virtuais Linux em Azure para criar uma aplicação altamente disponível.](tutorial-load-balancer.md)

## <a name="next-steps"></a>Passos seguintes
Neste exemplo, criou uma regra simples para permitir o tráfego HTTP. Pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:

* [Visão geral do Gestor de Recursos Azure](../../azure-resource-manager/management/overview.md)
* [O que é um grupo de segurança de rede?](../../virtual-network/network-security-groups-overview.md)
* [Visão geral do balançor de carga Azure](../../load-balancer/load-balancer-overview.md)
