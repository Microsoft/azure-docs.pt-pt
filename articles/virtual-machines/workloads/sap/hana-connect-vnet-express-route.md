---
title: Configuração de conectividade de rede virtual para o SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Configuração de conectividade de rede virtual para utilizar o SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cb14d0784ecb87c85b02952880e9eb5744d205a2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58850660"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Ligar uma rede virtual para instâncias grandes do HANA

Depois de criar uma rede virtual do Azure, pode ligar dessa rede ao SAP HANA nas instâncias grandes do Azure. Crie um gateway do ExpressRoute do Azure na rede virtual. Este gateway permite-lhe ligar a rede virtual para o circuito do ExpressRoute que se liga ao inquilino de cliente no carimbo a instância grande.

> [!NOTE] 
> Este passo pode demorar até 30 minutos a concluir. O novo gateway é criado na subscrição do Azure designada e, em seguida, ligado à rede virtual do Azure especificada.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Se já existir um gateway, verifique se é um gateway do ExpressRoute ou não. Caso contrário, elimine o gateway e recriá-la como um gateway do ExpressRoute. Se já estiver estabelecido um gateway do ExpressRoute, consulte a secção seguinte deste artigo, "Redes virtuais de ligação." 

- Utilizar o [portal do Azure](https://portal.azure.com/) ou PowerShell para criar um gateway de VPN do ExpressRoute ligado à sua rede virtual.
  - Se utilizar o portal do Azure, adicione uma nova **Gateway de rede Virtual**e, em seguida, selecione **ExpressRoute** como o tipo de gateway.
  - Se utilizar o PowerShell, primeiro de transferir e utilizar a versão mais recente [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). Os comandos seguintes criam um gateway do ExpressRoute. Os textos precedidos por um _$_ são variáveis definidas pelo utilizador que devem ser atualizadas com as suas informações específicas.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Neste exemplo, foi utilizado o SKU de gateway de alto desempenho. As opções são HighPerformance ou de UltraPerformance como os SKUs de gateway só são suportados para SAP HANA no Azure (instâncias grandes).

> [!IMPORTANT]
> Para instâncias grandes do HANA da classe de tipo II SKU, tem de utilizar o SKU de Gateway de UltraPerformance.

## <a name="link-virtual-networks"></a>Ligação de redes virtuais

A rede virtual do Azure tem agora um gateway do ExpressRoute. Utilize as informações de autorização fornecidas pela Microsoft para ligar o gateway do ExpressRoute para o SAP HANA no circuito do ExpressRoute do Azure (instâncias grandes). Pode ligar através do portal do Azure ou o PowerShell. O portal é recomendado, mas se pretender utilizar o PowerShell, as instruções são os seguintes. 

Execute os seguintes comandos para cada gateway de rede virtual com um AuthGUID diferente para cada ligação. As duas primeiras entradas mostradas no seguinte script provenientes as informações fornecidas pela Microsoft. Além disso, o AuthGUID é específico para cada rede virtual e o seu gateway. Se pretender adicionar outra rede virtual do Azure, terá de obter AuthID outro para o seu circuito do ExpressRoute que se liga instâncias grandes do HANA no Azure. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Para ligar o gateway a mais do que um circuito do ExpressRoute associado à subscrição, poderá ter de executar este passo de mais de uma vez. Por exemplo, provavelmente vai ligar o mesmo gateway de rede virtual para o circuito do ExpressRoute que se liga a rede virtual à sua rede no local.

## <a name="next-steps"></a>Passos Seguintes

- [Requisitos de rede adicionais para HLI](hana-additional-network-requirements.md)
