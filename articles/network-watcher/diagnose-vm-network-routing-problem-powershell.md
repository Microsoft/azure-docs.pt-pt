---
title: Diagnosticar um problema de roteamento de rede VM-Azure PowerShell
titleSuffix: Azure Network Watcher
description: Neste artigo, você aprenderá a diagnosticar um problema de roteamento de rede de máquina virtual usando o recurso de próximo salto do observador de rede do Azure.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 81e2af329661d485b2d189e9a1f70b50bd6d4b7d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276118"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Diagnosticar um problema de roteamento de rede de máquina virtual-Azure PowerShell

Neste artigo, você implanta uma VM (máquina virtual) e, em seguida, verifica as comunicações para um endereço IP e uma URL. Determine a causa de uma falha de comunicação e como pode resolvê-la.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo Azure PowerShell `Az`. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.



## <a name="create-a-vm"></a>Criar uma VM

Antes de criar uma VM, tem de criar um grupo de recursos para conter a VM. Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Crie a VM com [New-AzVM](/powershell/module/az.compute/new-azvm). Ao executar este passo, serão pedidas credenciais. Os valores que introduzir são configurados, como o nome de utilizador e a palavra-passe para a VM.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

A criação da VM demora alguns minutos. Não continue com os restantes passos até que a VM seja criada e o PowerShell devolva um resultado.

## <a name="test-network-communication"></a>Testar a comunicação de rede

Para testar a comunicação de rede com o observador de rede, você deve primeiro habilitar um observador de rede na região em que a VM que você deseja testar está e, em seguida, usar o recurso de próximo salto do observador de rede para testar a comunicação.

## <a name="enable-network-watcher"></a>Ativar o observador de rede

Se você já tiver um observador de rede habilitado na região leste dos EUA, use [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) para recuperar o observador de rede. O seguinte exemplo obtém um observador de rede existente com o nome *NetworkWatcher_eastus* que se encontra no grupo de recursos *NetworkWatcherRG*:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Se você ainda não tiver um observador de rede habilitado na região leste dos EUA, use [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) para criar um observador de rede na região leste dos EUA:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Utilizar o próximo salto

O Azure cria automaticamente rotas para destinos predefinidos. Pode criar rotas personalizadas que substituem as rotas predefinidas. Por vezes, as rotas personalizadas podem causar falhas na comunicação. Para testar o roteamento de uma VM, use o comando [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) para determinar o próximo salto de roteamento quando o tráfego for destinado a um endereço específico.

Teste a comunicação de saída a partir da VM para um dos endereços IP para www.bing.com:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Depois de alguns segundos, a saída informa que o **NextHopType** é a **Internet**e que o **RouteTableId** é a rota do **sistema**. Esse resultado permite que você saiba que há uma rota válida para o destino.

Teste a comunicação de saída da VM para 172.31.0.100:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

A saída retornada informa que **nenhum** é o **NextHopType**, e que o **RouteTableId** também é a rota do **sistema**. Este resultado permite-lhe saber que, embora exista uma rota de sistema válida para o destino, não há um próximo salto para encaminhar o tráfego para o destino.

## <a name="view-details-of-a-route"></a>Ver detalhes de uma rota

Para analisar ainda mais o roteamento, examine as rotas efetivas para a interface de rede com o comando [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) :

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

A saída que inclui o seguinte texto é retornada:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Como você pode ver na saída anterior, a rota com **AddressPrefix** de **0.0.0.0/0** roteia todo o tráfego não destinado a endereços dentro dos prefixos de endereço de outra rota com um próximo salto da **Internet**. Como você também pode ver na saída, embora haja uma rota padrão para o prefixo 172.16.0.0/12, que inclui o endereço 172.31.0.100, o **nextHopType** é **nenhum**. O Azure cria uma rota predefinida para 172.16.0.0/12, mas não especifica um tipo de próximo salto até que haja um motivo para isso. Se, por exemplo, você adicionou o intervalo de endereços 172.16.0.0/12 ao espaço de endereço da rede virtual, o Azure altera o **nextHopType** para a **rede virtual** para a rota. Uma verificação, em seguida, mostraria **rede virtual** como o **nextHopType**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você criou uma VM e um roteamento de rede diagnosticado da VM. Aprendeu que o Azure cria várias rotas predefinidas e testa o encaminhamento para dois destinos diferentes. Saiba mais sobre o [encaminhamento no Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e como [criar rotas personalizadas](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Para conexões de VM de saída, você também pode determinar a latência e o tráfego de rede permitido e negado entre a VM e um ponto de extremidade usando a funcionalidade de [solução de problemas de conexão](network-watcher-connectivity-powershell.md) do observador de rede. Você pode monitorar a comunicação entre uma VM e um ponto de extremidade, como um endereço IP ou URL, ao longo do tempo usando o recurso de monitor de conexão do observador de rede. Para saber como, consulte [monitorar uma conexão de rede](connection-monitor.md).
