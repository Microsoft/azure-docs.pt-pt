---
title: Diagnosticar um máquina virtual rede problema de encaminhamento - Azure PowerShell | Documentos da Microsoft
description: Neste artigo, saiba como diagnosticar um problema encaminhamento de rede de máquina virtual com a capacidade de salto seguinte do observador de rede do Azure.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
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
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f793a201b3fbf57ac2f420c4f4e57a230bc11468
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299029"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Diagnosticar um máquina virtual rede problema de encaminhamento - Azure PowerShell

Neste artigo, implementar uma máquina virtual (VM) e, em seguida, verifique as comunicações para um endereço IP e o URL. Vai determinar a causa de uma falha de comunicação e aprender a resolvê-la.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 do módulo AzureRM PowerShell ou posterior. Para localizar a versão instalada, execute `Get-Module -ListAvailable AzureRM`. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Login-AzureRmAccount` para criar uma ligação com o Azure.

## <a name="create-a-vm"></a>Criar uma VM

Antes de criar uma VM, tem de criar um grupo de recursos para conter a VM. Crie um grupo de recursos com [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Crie uma VM com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Ao executar este passo, serão pedidas credenciais. Os valores que introduzir são configurados, como o nome de utilizador e a palavra-passe para a VM.

```azurepowershell-interactive
$vM = New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

A criação da VM demora alguns minutos. Não continue com os restantes passos até que a VM seja criada e o PowerShell devolva um resultado.

## <a name="test-network-communication"></a>Testar a comunicação de rede

Para testar a comunicação de rede com o observador de rede, tem de ativar um observador de rede na região que a VM que pretende testar está no primeiro e, em seguida, utilize as capacidades de salto do observador de rede seguinte para testar a comunicação entre.

## <a name="enable-network-watcher"></a>Ativar o observador de rede

Se já tiver um observador de rede ativado na região E.U.A. Leste, utilize [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher) para obter o observador de rede. O seguinte exemplo obtém um observador de rede existente com o nome *NetworkWatcher_eastus* que se encontra no grupo de recursos *NetworkWatcherRG*:

```azurepowershell-interactive
$networkWatcher = Get-AzureRmNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Se ainda não tiver um observador de rede ativado na região E.U.A. Leste, utilize [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) para criar um observador de rede na região E.U.A Leste:

```azurepowershell-interactive
$networkWatcher = New-AzureRmNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Utilizar o próximo salto

O Azure cria automaticamente rotas para destinos predefinidos. Pode criar rotas personalizadas que substituem as rotas predefinidas. Por vezes, as rotas personalizadas podem causar falhas na comunicação. Para testar o encaminhamento de uma VM, utilize o [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) comando para determinar o próximo salto de encaminhamento quando o tráfego é destinado para um endereço específico.

Teste a comunicação de saída da VM para um dos endereços IP para www.bing.com:

```azurepowershell-interactive
Get-AzureRmNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Após alguns segundos, a saída informa que o **NextHopType** é **Internet**e que o **RouteTableId** é **rota de sistema**. Este resultado permite-lhe saber que existe uma rota válida para o destino.

Teste a comunicação de saída da VM para 172.31.0.100:

```azurepowershell-interactive
Get-AzureRmNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

Informa-o resultado devolvido que **None** é o **NextHopType**e que o **RouteTableId** também é **rota de sistema**. Este resultado permite-lhe saber que, embora exista uma rota de sistema válida para o destino, não há um próximo salto para encaminhar o tráfego para o destino.

## <a name="view-details-of-a-route"></a>Ver detalhes de uma rota

Para analisar mais de encaminhamento, reveja as rotas efetivas para a interface de rede com o [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable) comando:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

É devolvida a saída que inclui o seguinte texto:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Como pode ver na saída anterior, a rota com o **AaddressPrefix** dos **0.0.0.0/0** encaminha todo o tráfego não destinado a endereços dentro de prefixos de endereço de outra rota com um salto seguinte da **Internet**. Como também pode ver na saída, mas existe uma rota predefinida para o prefixo de 172.16.0.0/12, que inclui o 172.31.0.100 endereço, o **nextHopType** é **nenhum**. O Azure cria uma rota predefinida para 172.16.0.0/12, mas não especifica um tipo de próximo salto até que haja um motivo para isso. Se, por exemplo, tiver adicionado o intervalo de endereços 172.16.0.0/12 ao espaço de endereços da rede virtual, o Azure altera a **nextHopType** ao **rede Virtual** para a rota. Uma verificação, em seguida, mostraria **rede Virtual** como o **nextHopType**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que contém:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criou uma VM e diagnosticados encaminhamento de rede da VM. Aprendeu que o Azure cria várias rotas predefinidas e testa o encaminhamento para dois destinos diferentes. Saiba mais sobre o [encaminhamento no Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e como [criar rotas personalizadas](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Para ligações da VM de saída, também pode determinar a latência e autorizadas e negadas tráfego de rede entre a VM e um ponto final com o observador de rede [resolver problemas de ligação](network-watcher-connectivity-powershell.md) capacidade. Pode monitorizar a comunicação entre uma VM e um ponto de extremidade, como um endereço IP ou o URL, ao longo do tempo com a capacidade de monitor de ligação do observador de rede. Para saber como, veja [monitorizar uma ligação de rede](connection-monitor.md).