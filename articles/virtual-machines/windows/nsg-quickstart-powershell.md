---
title: Abrir portas para um VM usando o Azure PowerShell
description: Saiba como abrir uma porta / criar um ponto final para o seu Windows VM utilizando o modo de implementação do gestor de recursos Azure e o Azure PowerShell
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
ms.openlocfilehash: 547ca9c98d77b2aaa6d3630bff4b2ec10dcc5be0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754165"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Como abrir portas e pontos finais para um VM em Azure usando powerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandos rápidos
Para criar um Grupo de Segurança de Rede e regras ACL, precisa [da versão mais recente do Azure PowerShell instalada](/powershell/azureps-cmdlets-docs). Também pode [executar estes passos utilizando o portal Azure.](nsg-quickstart-portal.md)

Inicie sessão na sua conta do Azure:

```powershell
Connect-AzAccount
```

Nos seguintes exemplos, substitua os nomes dos parâmetros pelos seus próprios valores. Exemplo nomes de parâmetros incluem *myResourceGroup,* *myNetworkSecurityGroup*, e *myVnet*.

Crie uma regra com [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig). O exemplo seguinte cria uma regra chamada *myNetworkSecurityGroupRule* para permitir o tráfego *de TCP* na porta *80*:

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

Em seguida, crie o seu grupo de Segurança de Rede com [o New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) e atribua a regra HTTP que acabou de criar da seguinte forma. O exemplo seguinte cria um Grupo de Segurança de Rede chamado *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Agora vamos atribuir o seu Grupo de Segurança de Rede a uma sub-rede. O exemplo seguinte atribui uma rede virtual existente chamada *myVnet* à variável *$vnet* com [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork)

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associe o seu Grupo de Segurança de Rede com a sua subnet com [o Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig). O exemplo seguinte associa a subnet chamada *mySubnet* ao seu Grupo de Segurança de Rede:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Por fim, atualize a sua rede virtual com [set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) para que as suas alterações entrem em vigor:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Mais informações sobre grupos de segurança da rede
Os comandos rápidos aqui permitem-lhe levantar-se e correr com o tráfego fluindo para o seu VM. Os Grupos de Segurança da Rede fornecem muitas funcionalidades e granularidade para controlar o acesso aos seus recursos. Pode ler mais sobre [a criação](tutorial-virtual-network.md#secure-network-traffic)de um Grupo de Segurança de Rede e regras ACL aqui .

Para aplicações web altamente disponíveis, deve colocar os seus VMs atrás de um Equilíbrio de Carga Azure. O equilibrador de carga distribui tráfego para VMs, com um Grupo de Segurança de Rede que fornece filtragem de tráfego. Para mais informações, consulte Como carregar o equilíbrio das [máquinas virtuais Linux em Azure para criar uma aplicação altamente disponível.](tutorial-load-balancer.md)

## <a name="next-steps"></a>Passos seguintes
Neste exemplo, criou uma regra simples para permitir o tráfego HTTP. Pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:

* [Visão geral do Gestor de Recursos Azure](../../azure-resource-manager/management/overview.md)
* [O que é um grupo de segurança da rede?](../../virtual-network/security-overview.md)
* [Visão geral do equilíbrio de carga azure](../../load-balancer/load-balancer-overview.md)

