---
title: Conectividade criada desde a rede virtual até SAP HANA no Azure (grandes instâncias) / Microsoft Docs
description: Conectividade criada a partir de rede virtual para usar SAP HANA no Azure (grandes instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f7ac8e69c4e149fdd0f365e19f7a0282a547af43
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617192"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Ligue uma rede virtual a grandes instâncias HANA

Depois de criar uma rede virtual Azure, pode ligar essa rede ao SAP HANA em grandes instâncias do Azure. Crie um portal Azure ExpressRoute na rede virtual. Este portal permite-lhe ligar a rede virtual ao circuito ExpressRoute que se liga ao inquilino do cliente no carimbo HANA Large Instance.

> [!NOTE] 
> Este passo pode levar até 30 minutos para ser concluído. O novo portal é criado na subscrição azure designada e, em seguida, ligado à rede virtual Azure especificada.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Se já existe um portal, verifique se é ou não um gateway ExpressRoute. Se não for um gateway ExpressRoute, elimine o portal e recrie-o como um gateway ExpressRoute. Se já estiver estabelecido um gateway ExpressRoute, consulte a seguinte secção deste artigo, "Link redes virtuais". 

- Utilize o [portal Azure](https://portal.azure.com/) ou o PowerShell para criar um gateway VPN ExpressRoute ligado à sua rede virtual.
  - Se utilizar o portal Azure, adicione um novo Portal de **Rede Virtual,** e, em seguida, selecione **ExpressRoute** como o tipo de gateway.
  - Se utilizar o PowerShell, primeiro descarregue e utilize o mais recente [SDK Azure PowerShell](https://azure.microsoft.com/downloads/). 
 
Os seguintes comandos criam um gateway ExpressRoute. Os textos precedidos por um _$_ são variáveis definidas pelo utilizador que devem ser atualizadas com as suas informações específicas.

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

Neste exemplo, foi utilizado o gateway HighPerformance SKU. As suas opções são HighPerformance ou UltraPerformance como o único Portal de Entrada SKUs que são suportados para SAP HANA em Azure (grandes instâncias).

> [!IMPORTANT]
> Para grandes instâncias HANA da classe Tipo II SKU, você deve usar o UltraPerformance Gateway SKU.

## <a name="link-virtual-networks"></a>Ligar redes virtuais

A rede virtual Azure tem agora um portal ExpressRoute. Utilize as informações de autorização fornecidas pela Microsoft para ligar a porta de entrada ExpressRoute ao circuito SAP HANA Large Instances ExpressRoute. Pode ligar-se utilizando o portal Azure ou powerShell. As instruções powerShell são as seguintes. 

Executar os seguintes comandos para cada gateway ExpressRoute utilizando um AuthGUID diferente para cada ligação. As duas primeiras entradas mostradas no seguinte script provêm das informações fornecidas pela Microsoft. Além disso, o AuthGUID é específico para cada rede virtual e sua porta de entrada. Se quiser adicionar outra rede virtual Azure, precisa de obter outro AuthID para o seu circuito ExpressRoute que ligue grandes instâncias HANA ao Azure da Microsoft. 

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
> O último parâmetro no comando New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** é um novo parâmetro que permite o Caminho Rápido ExpressRoute. Uma funcionalidade que reduz a latência da rede entre as suas unidades HANA Large Instance e Os VMs Azure. A funcionalidade foi adicionada em maio de 2019. Para mais detalhes, consulte a arquitetura da [rede SAP HANA (Grandes Instâncias).](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture) Certifique-se de que está a executar a versão mais recente dos cmdlets PowerShell antes de executar os comandos.

Para ligar a porta de entrada a mais de um circuito ExpressRoute associado à sua subscrição, poderá ter de executar este passo mais de uma vez. Por exemplo, é provável que ligue a mesma porta de entrada de rede virtual ao circuito ExpressRoute que liga a rede virtual à sua rede no local.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Aplicação do Caminho Rápido ExpressRoute aos circuitos existentes hana de grande instância ExpressRoute
A documentação até agora explicacomo ligar um novo circuito ExpressRoute que foi criado com uma implementação de Hana Large Instance para um portal Azure ExpressRoute de uma das suas redes virtuais Azure. Mas muitos clientes já têm os seus circuitos ExpressRoute já e já têm as suas redes virtuais ligadas às Grandes Instâncias HANA. Como o novo Caminho Rápido ExpressRoute está a reduzir a latência da rede, recomenda-se que aplique a alteração para utilizar esta funcionalidade. Os comandos para ligar um novo circuito ExpreesRoute e alterar um circuito expressroute existente são os mesmos. Como resultado, você precisa executar esta sequência de comandos PowerShell para alterar um circuito existente para usar 

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

É importante que adicione o último parâmetro acima apresentado para ativar a funcionalidade ExpressRoute Fast Path


## <a name="expressroute-global-reach"></a>Alcance Global do ExpressRoute
Como pretende permitir o Alcance Global para um ou ambos os cenários:

 - Replicação do sistema HANA sem quaisquer proxies ou firewalls adicionais
 - Copiar cópias de cópias entre unidades HANA Large Instance em duas regiões diferentes para executar cópias do sistema ou atualizações do sistema

precisa considerar que:

- Você precisa fornecer uma gama de endereços espaço de endereço de um espaço de endereço /29. Essa gama de endereços pode não se sobrepor a nenhuma das outras gamas de espaço de endereço que usou até agora ligando as grandes instâncias HANA ao Azure e pode não se sobrepor a nenhuma das suas gamas de endereços IP que utilizou em outro lugar em Azure ou no local.
- Existe uma limitação nas ASNs (Número do Sistema Autónomo) que pode ser usada para anunciar as suas rotas no local para as Grandes Instâncias HANA. As suas instalações não devem anunciar quaisquer rotas com ASNs privadas na faixa dos 65000 – 65020 ou 65515. 
- Para o cenário de ligar o acesso direto às grandes instâncias HANA Large, é necessário calcular uma taxa para o circuito que o liga ao Azure. Para preços, verifique os preços do [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/).

Para obter um ou ambos os cenários aplicados à sua implementação, abra uma mensagem de apoio com azure como descrito em Open um pedido de [apoio para grandes instâncias HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Os dados necessários e as palavras-chave que precisa de utilizar para a Microsoft poderem encaminhar e executar a seu pedido, parecem:

- Serviço: SAP HANA Grande Instância
- Tipo de problema: Configuração e Configuração
- Subtipo de problema: O meu problema não está listado acima
- Assunto 'Modificar a minha Rede - adicionar Alcance Global'
- Detalhes: 'Adicione o Alcance Global à HANA Large Instance ao inquilino hana large instance ou 'Adicione o Alcance Global ao inquilino hana large instância.
- Detalhes adicionais para o caso hana grande instância para hana caso de inquilino de grande instância: Você precisa definir as **duas regiões Azure** onde os dois inquilinos para se conectar **estão** localizados E você precisa submeter a gama de **endereços IP /29**
- Detalhes adicionais para o caso do inquilino HANA Large Instance: Você precisa definir a região de **Azure** onde o inquilino hana large instância é implantado você quer se conectar diretamente. Além disso, precisa de fornecer o **Auth GUID** e **o Circuit Peer ID** que recebeu quando estabeleceu o seu circuito ExpressRoute entre as instalações e o Azure. Além disso, precisa nomear o seu **ASN**. O último deliverable é uma gama de **endereços IP /29** para ExpressRoute Global Reach.

> [!NOTE]
> Se quiser ter ambos os casos tratados, precisa de fornecer duas gamas de endereços IP diferentes /29 que não se sobrepõem a qualquer outra gama de endereços IP utilizada até agora. 




## <a name="next-steps"></a>Passos seguintes

- [Requisitos adicionais de rede para o HLI](hana-additional-network-requirements.md)
