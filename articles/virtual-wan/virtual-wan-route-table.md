---
title: Criar uma tabela de rotas de virtual hub WAN Virtual do Azure para conduzir a NVA | Documentos da Microsoft
description: Tabela de rotas de Virtual WAN virtual hub para conduzir o tráfego para uma aplicação virtual de rede.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: ac1384827ceede0f66fd08c6c08fa8e934b1ae42
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58076161"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Criar uma tabela de rotas de Virtual Hub para conduzir o tráfego para uma aplicação Virtual de rede

Este artigo mostra-lhe como conduzir o tráfego a partir de um Hub Virtual para uma aplicação Virtual de rede. 

![Diagrama da WAN Virtual](./media/virtual-wan-route-table/vwanroute.png)

Neste artigo, vai aprender a:

* Criar uma WAN
* Criar um hub
* Criar um hub de ligações de rede virtual
* Criar uma rota de hub
* Criar uma tabela de rotas
* Aplicam-se a tabela de rotas

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Certifique-se de que cumpriu os seguintes critérios:

1. Tem uma aplicação Virtual de rede (NVA) é um software de terceiros à sua escolha, que normalmente é aprovisionado do Azure Marketplace (ligação) numa rede virtual.
2. Tem um IP privado atribuído à interface de rede NVA. 
3. NVA não pode ser implementada no virtual hub. Tem de ser implementada numa VNet separada. Neste artigo, a VNet NVA é referida como "VNet de rede de Perímetro".
4. A "rede de Perímetro VNet' pode ter uma ou várias redes virtuais ligadas à mesma. Neste artigo, nesta VNet é referida como 'VNet spoke Indireta'. Nestas VNets podem ser ligadas à vnet de rede de Perímetro, utilização de VNet peering.
5. Certifique-se de que tem 2 VNets já criadas. Serão utilizados como spoke VNets. Neste artigo, os espaços de endereços do VNet spoke são 10.0.2.0/24 e 10.0.3.0/24. Se precisar de informações sobre como criar uma VNet, veja [criar uma rede virtual com o PowerShell](../virtual-network/quick-create-powershell.md).
6. Certifique-se de que não há nenhum gateways de rede virtual em qualquer VNets.

## <a name="signin"></a>1. Iniciar sessão

Certifique-se de que instalar a versão mais recente dos cmdlets do PowerShell do Resource Manager. Para obter mais informações sobre como instalar os cmdlets PowerShell, consulte [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-az-ps). Isto é importante porque as versões anteriores dos cmdlets não contêm os valores atuais de que precisa para este exercício.

1. Abra a consola do PowerShell com privilégios elevados e inicie sessão na sua conta do Azure. Este cmdlet pede-lhe as credenciais de início de sessão. Depois de iniciar sessão, são transferidas as definições de conta para que fiquem disponíveis para o Azure PowerShell.

   ```powershell
   Connect-AzAccount
   ```
2. Obtenha uma lista das suas subscrições do Azure.

   ```powershell
   Get-AzSubscription
   ```
3. Especifique a subscrição que pretende utilizar.

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="rg"></a>2. Criar recursos

1. Crie um grupo de recursos.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Crie uma WAN virtual.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Crie um hub virtual.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24"
   ```

## <a name="connections"></a>3. Criar ligações

Crie hub de ligações de rede virtual da VNet Spoke Indiretos e a VNet de rede de Perímetro para o hub virtual.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="route"></a>4. Criar uma rota de virtual hub

Neste artigo, os espaços de endereços da VNet Spoke Indireta são 10.0.2.0/24 e 10.0.3.0/24 e o endereço IP privado do DMZ NVA rede interface é 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="applyroute"></a>5. Criar uma tabela de rotas de virtual hub

Criar uma tabela de rotas de virtual hub e, em seguida, aplicar a rota criada à mesma.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="commit"></a>6. Confirmar as alterações

Confirme as alterações para o hub virtual.

```powershell
Update-AzVirtualHub -VirtualWanId $virtualWan.Id -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="cleanup"></a>Limpar recursos

Quando já não precisa destes recursos, pode utilizar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos nele contidos. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
