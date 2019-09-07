---
title: Criar uma tabela de rota do Hub virtual de WAN virtual do Azure para direcionar para NVA | Microsoft Docs
description: Tabela de rotas do Hub virtual WAN virtual para direcionar o tráfego para uma solução de virtualização de rede.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: 18af56f6924484c6267871cf3fed34f80a8f12a4
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744702"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Criar uma tabela de rotas do Hub virtual para direcionar o tráfego para uma solução de virtualização de rede

Este artigo mostra como direcionar o tráfego de um hub virtual para uma solução de virtualização de rede. 

![Diagrama da WAN Virtual](./media/virtual-wan-route-table/vwanroute.png)

Neste artigo, vai aprender a:

* Criar uma WAN
* Criar um hub
* Criar conexões de rede virtual do Hub
* Criar uma rota de Hub
* Criar uma tabela de rotas
* Aplicar a tabela de rotas

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Verifique se você atendeu aos seguintes critérios:

1. Você tem uma solução de virtualização de rede (NVA). Esse é um software de terceiros de sua escolha que normalmente é provisionado do Azure Marketplace em uma rede virtual.
2. Você tem um IP privado atribuído à interface de rede NVA. 
3. O NVA não pode ser implantado no Hub virtual. Ele deve ser implantado em uma VNet separada. Para este artigo, a VNet NVA é conhecida como ' DMZ VNet '.
4. A ' rede virtual DMZ ' pode ter uma ou várias redes virtuais conectadas a ela. Neste artigo, essa VNet é chamada de "VNet de spoke indireto". Esses VNets podem ser conectados à VNet da DMZ usando o emparelhamento VNet.
5. Verifique se você tem 2 VNets já criados. Eles serão usados como spoke VNets. Para este artigo, os espaços de endereço do spoke da VNet são 10.0.2.0/24 e 10.0.3.0/24. Se você precisar de informações sobre como criar uma VNet, consulte [criar uma rede virtual usando o PowerShell](../virtual-network/quick-create-powershell.md).
6. Verifique se não há nenhum gateway de rede virtual em nenhum VNets.

## <a name="signin"></a>1. Iniciar sessão

Certifique-se de instalar a versão mais recente dos cmdlets do PowerShell do Gerenciador de recursos. Para obter mais informações sobre como instalar os cmdlets PowerShell, consulte [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-az-ps). Isto é importante porque as versões anteriores dos cmdlets não contêm os valores atuais de que precisa para este exercício.

1. Abra o console do PowerShell com privilégios elevados e entre em sua conta do Azure. Esse cmdlet solicita as credenciais de entrada. Depois de entrar, ele baixa suas configurações de conta para que elas estejam disponíveis para Azure PowerShell.

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
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24" -Location "West US"
   ```

## <a name="connections"></a>3. Criar conexões

Crie conexões de rede virtual de Hub de VNet de spoke indireto e da rede virtual DMZ para o Hub virtual.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="route"></a>4. Criar uma rota de Hub virtual

Para este artigo, os espaços de endereço de VNet de spoke indireto são 10.0.2.0/24 e 10.0.3.0/24, e o endereço IP privado da interface de rede NVA DMZ é 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="applyroute"></a>5. Criar uma tabela de rotas do Hub virtual

Crie uma tabela de rotas do Hub virtual e aplique a rota criada a ela.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="commit"></a>6. Confirmar as alterações

Confirme as alterações no Hub virtual.

```powershell
Update-AzVirtualHub -VirtualWanId $virtualWan.Id -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
