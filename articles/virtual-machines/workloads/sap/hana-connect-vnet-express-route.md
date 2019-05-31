---
title: Conectividade de configurar a partir da rede virtual para o SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Conectividade configure a partir da rede virtual para utilizar o SAP HANA no Azure (instâncias grandes).
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
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9cd290f9e5e7819f3dffa2dd1efea9cd0028fc2c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239473"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Ligar uma rede virtual para instâncias grandes do HANA

Depois de criar uma rede virtual do Azure, pode ligar dessa rede ao SAP HANA nas instâncias grandes do Azure. Crie um gateway do ExpressRoute do Azure na rede virtual. Este gateway permite-lhe ligar a rede virtual para o circuito do ExpressRoute que se liga ao inquilino de cliente no carimbo a instância grande do HANA.

> [!NOTE] 
> Este passo pode demorar até 30 minutos a concluir. O novo gateway é criado na subscrição do Azure designada e, em seguida, ligado à rede virtual do Azure especificada.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Se já existir um gateway, verifique se é um gateway do ExpressRoute ou não. Se não for um gateway do ExpressRoute, elimine o gateway e recriá-la como um gateway do ExpressRoute. Se já estiver estabelecido um gateway do ExpressRoute, consulte a secção seguinte deste artigo, "Redes virtuais de ligação." 

- Utilizar o [portal do Azure](https://portal.azure.com/) ou PowerShell para criar um gateway de VPN do ExpressRoute ligado à sua rede virtual.
  - Se utilizar o portal do Azure, adicione uma nova **Gateway de rede Virtual**e, em seguida, selecione **ExpressRoute** como o tipo de gateway.
  - Se utilizar o PowerShell, primeiro de transferir e utilizar a versão mais recente [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). 
 
Os comandos seguintes criam um gateway do ExpressRoute. Os textos precedidos por um _$_ são variáveis definidas pelo utilizador que devem ser atualizadas com as suas informações específicas.

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

A rede virtual do Azure tem agora um gateway do ExpressRoute. Utilize as informações de autorização fornecidas pela Microsoft para ligar o gateway do ExpressRoute para o circuito do ExpressRoute de instâncias grandes do SAP HANA. Pode ligar através do portal do Azure ou o PowerShell. Seguem-se as instruções do PowerShell. 

Execute os seguintes comandos para cada gateway do ExpressRoute com um AuthGUID diferente para cada ligação. As duas primeiras entradas mostradas no seguinte script provenientes as informações fornecidas pela Microsoft. Além disso, o AuthGUID é específico para cada rede virtual e o seu gateway. Se pretender adicionar outra rede virtual do Azure, terá de obter AuthID outro para o seu circuito do ExpressRoute que se liga instâncias grandes do HANA no Azure da Microsoft. 

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
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> O último parâmetro no comando New-AzVirtualNetworkGatewayConnection **ExpressRouteGatewayBypass** é um novo parâmetro permite que o caminho rápido do ExpressRoute. Uma funcionalidade que reduz a latência de rede entre as suas unidades de instância grande do HANA e VMs do Azure. A funcionalidade foi adicionada em Maio de 2019. Para obter mais detalhes, consulte o artigo [arquitetura de rede do SAP HANA (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Certifique-se de que estão a executar a versão mais recente dos cmdlets do PowerShell antes de executar os comandos.

Para ligar o gateway a mais do que um circuito do ExpressRoute associado à subscrição, poderá ter de executar este passo de mais de uma vez. Por exemplo, provavelmente vai ligar o mesmo gateway de rede virtual para o circuito do ExpressRoute que se liga a rede virtual à sua rede no local.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Aplicar o caminho rápido de ExpressRoute para circuitos do ExpressRoute de instância grande do HANA existentes
A documentação até agora explicou como ligar um novo circuito ExpressRoute criado com uma implementação de instância grande do HANA para um gateway do ExpressRoute do Azure de uma das suas redes virtuais do Azure. Mas muitos clientes já tiverem sua configuração de circuitos do ExpressRoute e das suas redes virtuais ligou para instâncias grandes do HANA já. Como o novo ExpressRoute rápida caminho é reduzir a latência de rede, recomenda-se que aplique a alteração para utilizar esta funcionalidade. Os comandos para ligar um novo circuito ExpreesRoute e para alterar um circuito do ExpressRoute existente são os mesmos. Como resultado tem de executar esta sequência de comandos do PowerShell para alterar um circuito existente para utilizar 

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
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

É importante que adicione o último parâmetro tal como apresentado acima para ativar a funcionalidade de caminho rápido do ExpressRoute


## <a name="expressroute-global-reach"></a>Alcance Global do ExpressRoute
À medida que pretende ativar o alcance Global de mensagens em fila para um ou ambos dos dois cenários:

 - HANA System Replication sem firewalls ou proxies adicionais
 - Copiar as cópias de segurança entre unidades de instância grande do HANA em duas regiões diferentes para efetuar cópias de sistema ou atualizações de sistema

precisa considerar que:

- Tem de fornecer um intervalo de espaço de endereços de/29 espaço de endereços. Que intervalo de endereços não pode ser sobrepostos com qualquer um dos outros intervalos de endereços espaço que utilizou até agora ligar instâncias grandes do HANA para o Azure e não podem ser sobrepostos com qualquer um dos seus intervalos de endereços IP utilizar lugar no Azure ou no local.
- Há uma limitação aos ASNs (número de sistema autónomo), que pode ser usado para anunciar rotas no local seu instâncias grandes do HANA. No local não deve anunciar quaisquer rotas com ASNs privados no intervalo de 65000 – 65020 ou 65515. 
- Para o cenário de ligação no local acesso direto a instâncias grandes do HANA, precisa calcular uma taxa para o circuito que liga-o para o Azure. Para os preços, consulte os preços para [suplemento alcance Global](https://azure.microsoft.com/pricing/details/expressroute/).

Para obter um ou ambos os cenários aplicados à sua implementação, abrir uma mensagem de suporte com o Azure conforme descrito em [abrir um pedido de suporte para instâncias grandes do HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Os dados que é necessária e palavras-chave que tem de utilizar para a Microsoft para conseguir encaminhar e executar no seu pedido, é semelhante a:

- Serviço: Instância Grande do SAP HANA
- Tipo de problema: Configuração e Definições
- Subtipo de problema: O meu problema não está indicado acima
- O assunto 'Modificar minha rede - adicionar o alcance Global'
- Detalhes: "Adicionar alcance Global para a instância grande do HANA para o inquilino de instância grande do HANA ou ' Adicionar alcance Global a no local para o inquilino de instância grande do HANA.
- Detalhes adicionais para a instância grande do HANA para instâncias grandes do HANA inquilino caso: É necessário definir o **duas regiões do Azure** onde estão localizados os dois inquilinos ligar **AND** tem de submeter o   **/29 intervalo de endereços IP**
- Detalhes adicionais para o local para o caso de inquilino de instância grande do HANA: É necessário definir o **região do Azure** em que o inquilino de instância grande do HANA é implementado deseja se conectar diretamente. Além disso, tem de fornecer a **Auth GUID** e **Circuit ID de ponto a ponto** que recebeu quando estabelecido o seu circuito do ExpressRoute entre no local e o Azure. Além disso, precisa nomear seus **ASN**. O último resultado final é um **/29 intervalo de endereços IP** para alcance Global do ExpressRoute.

> [!NOTE]
> Se quiser ter ambos os casos manipulados, tem de fornecer dois diferentes/29 intervalos de endereços IP que não se sobrepõem com qualquer outro IP endereço intervalo utilizado até agora. 




## <a name="next-steps"></a>Passos Seguintes

- [Requisitos de rede adicionais para HLI](hana-additional-network-requirements.md)
