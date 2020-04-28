---
title: 'WAN virtual: Criar tabela de rota de hub virtual para NVA: Azure PowerShell'
description: Mesa virtual de rota do hub virtual WAN para orientar o tráfego para um aparelho virtual de rede.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: a55e1453fe7fe4d135286b22dabf58d434762581
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645111"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Crie uma tabela de rota do Hub Virtual para orientar o tráfego para um aparelho virtual da rede

Este artigo mostra-lhe como orientar o tráfego de um Hub Virtual para um Aparelho Virtual de Rede. 

![Diagrama da WAN Virtual](./media/virtual-wan-route-table/vwanroute.png)

Neste artigo, saiba como:

* Criar uma WAN
* Criar um hub
* Criar ligações de rede virtual hub
* Criar uma rota de hub
* Criar uma tabela de rotas
* Aplicar a tabela de rotas

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Verifique se preencheu os seguintes critérios:

1. Tem um Aparelho Virtual rede (NVA). Este é um software de terceiros à sua escolha que é normalmente abastecido a partir do Azure Marketplace numa rede virtual.
2. Tem um IP privado atribuído à interface de rede NVA. 
3. A NVA não pode ser implantada no centro virtual. Deve ser implantado num VNet separado. Para este artigo, o VNet NVA é referido como o 'DMZ VNet'.
4. O 'DMZ VNet' pode ter uma ou muitas redes virtuais ligadas a ele. Neste artigo, este VNet é referido como "VNet de fala indireta". Estes VNets podem ser ligados ao DMZ VNet utilizando o peering VNet.
5. Verifique se já tem 2 VNets já criados. Estes serão usados como VNets falados. Para este artigo, os espaços de endereços de endereço saldados VNet são 10.0.2.0/24 e 10.0.3.0/24. Se precisar de informações sobre como criar um VNet, consulte [Criar uma rede virtual utilizando o PowerShell](../virtual-network/quick-create-powershell.md).
6. Certifique-se de que não existem gateways de rede virtuais em quaisquer VNets.

## <a name="1-sign-in"></a><a name="signin"></a>1. Iniciar sessão

Certifique-se de que instala a versão mais recente dos cmdlets powerShell do Gestor de Recursos. Para obter mais informações sobre como instalar os cmdlets PowerShell, consulte [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-az-ps). Isto é importante porque as versões anteriores dos cmdlets não contêm os valores atuais de que precisa para este exercício.

1. Abra a sua consola PowerShell com privilégios elevados e inscreva-se na sua conta Azure. Este cmdlet solicita-lhe as credenciais de inscrição. Depois de iniciar sessão, descarrega as definições da sua conta para que estejam disponíveis para o Azure PowerShell.

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

## <a name="2-create-resources"></a><a name="rg"></a>2. Criar recursos

1. Crie um grupo de recursos.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Crie um WAN virtual.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Criar um centro virtual.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24" -Location "West US"
   ```

## <a name="3-create-connections"></a><a name="connections"></a>3. Criar ligações

Crie ligações de rede virtual hub a partir de Indirect Spoke VNet e do DMZ VNet para o centro virtual.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="4-create-a-virtual-hub-route"></a><a name="route"></a>4. Criar uma rota de hub virtual

Para este artigo, os espaços de endereço VNet indiretos de Porta-Voz são 10.0.2.0/24 e 10.0.3.0/24, e o endereço IP privado de interface de rede DMZ NVA é 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="5-create-a-virtual-hub-route-table"></a><a name="applyroute"></a>5. Criar uma tabela de rota de hub virtual

Crie uma tabela de rota de hub virtual e, em seguida, aplique a rota criada para ela.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="6-commit-the-changes"></a><a name="commit"></a>6. Comprometer as alterações

Comprometa as mudanças no centro virtual.

```powershell
Update-AzVirtualHub -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
