---
title: Conectividade criada de rede virtual para SAP HANA em Azure (grandes instâncias) | Microsoft Docs
description: Conectividade configurada a partir de rede virtual para usar SAP HANA em Azure (grandes instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 218862b2df959fc0a39baa3c2934fc05f252af32
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672970"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Ligue uma rede virtual a grandes instâncias HANA

Depois de criar uma rede virtual Azure, pode ligar essa rede ao SAP HANA em grandes instâncias do Azure. Crie uma porta de entrada Azure ExpressRoute na rede virtual. Este gateway permite-lhe ligar a rede virtual ao circuito ExpressRoute que se conecta ao cliente no carimbo HANA Large Instance.

> [!NOTE] 
> Este passo pode levar até 30 minutos para ser concluído. O novo gateway é criado na subscrição designada Azure, e depois ligado à rede virtual Azure especificada.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Se já existe um portal, verifique se é ou não um portal ExpressRoute. Se não for um gateway ExpressRoute, apague o gateway e re-crie-o como porta de entrada ExpressRoute. Se já estiver estabelecido um gateway ExpressRoute, consulte a seguinte secção deste artigo, "Link redes virtuais". 

- Utilize o [portal Azure](https://portal.azure.com/) ou o PowerShell para criar um gateway VPN ExpressRoute ligado à sua rede virtual.
  - Se utilizar o portal Azure, adicione um novo **Gateway de Rede Virtual** e, em seguida, selecione **ExpressRoute** como o tipo gateway.
  - Se utilizar o PowerShell, faça o primeiro download e utilize o mais recente [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). 
 
Os seguintes comandos criam uma porta de entrada ExpressRoute. Os textos precedidos por um _$_ são variáveis definidas pelo utilizador que devem ser atualizadas com as suas informações específicas.

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

Neste exemplo, foi utilizado o gateway HighPerformance SKU. As suas opções são HighPerformance ou UltraPerformance como os únicos SKUs de gateway que são suportados para SAP HANA em Azure (grandes instâncias).

> [!IMPORTANT]
> Para grandes instâncias da classe HANA SKU, deve utilizar o SKU de Gateway De UltraPerformance.

## <a name="link-virtual-networks"></a>Ligue redes virtuais

A rede virtual Azure tem agora um gateway ExpressRoute. Utilize as informações de autorização fornecidas pela Microsoft para ligar o gateway ExpressRoute ao circuito SAP HANA Large Instances ExpressRoute. Pode ligar-se utilizando o portal Azure ou o PowerShell. As instruções powerShell são as seguintes. 

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
> O último parâmetro no comando New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** é um novo parâmetro que permite o Caminho Rápido ExpressRoute. Uma funcionalidade que reduz a latência da rede entre as suas unidades HANA Large Instance e Azure VMs. A funcionalidade foi adicionada em maio de 2019. Para mais detalhes, consulte a arquitetura da [rede SAP HANA (Grandes Instâncias).](./hana-network-architecture.md) Certifique-se de que está a executar a versão mais recente dos cmdlets PowerShell antes de executar os comandos.

Para ligar o gateway a mais de um circuito ExpressRoute associado à sua subscrição, poderá ter de executar este passo mais de uma vez. Por exemplo, é provável que ligue a mesma porta de entrada de rede virtual ao circuito ExpressRoute que liga a rede virtual à sua rede no local.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Aplicação do Caminho Rápido ExpressRoute aos circuitos expressRoute de grande instância existentes
A documentação até agora explica como ligar um novo circuito ExpressRoute que foi criado com uma implantação HANA Large Instance para uma porta de entrada Azure ExpressRoute de uma das suas redes virtuais Azure. Mas muitos clientes já têm os seus circuitos ExpressRoute configurados e já têm as suas redes virtuais ligadas à HANA Large Instances. Como o novo Caminho Rápido ExpressRoute está a reduzir a latência da rede, recomenda-se que aplique a alteração para utilizar esta funcionalidade. Os comandos para ligar um novo circuito ExpreesRoute e para alterar um Circuito ExpressRoute existente são os mesmos. Como resultado, você precisa executar esta sequência de comandos PowerShell para alterar um circuito existente para usar 

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

É importante que adicione o último parâmetro como mostrado acima para ativar a funcionalidade ExpressRoute Fast Path


## <a name="expressroute-global-reach"></a>Alcance Global do ExpressRoute
Como pretende ativar o Global Reach para um ou ambos os cenários:

 - Replicação do sistema HANA sem proxies ou firewalls adicionais
 - Cópia de cópias de segurança entre unidades HANA Large Instance em duas regiões diferentes para executar cópias do sistema ou atualizações do sistema

tem de considerar isso:

- Você precisa fornecer uma gama de espaço de endereço de um espaço de endereço /29. Essa gama de endereços pode não se sobrepor a nenhuma das outras gamas de espaços de endereço que usou até agora ligando HANA Large Instances a Azure e pode não se sobrepor a nenhuma das suas gamas de endereços IP que usou em outro lugar em Azure ou no local.
- Existe uma limitação nas ASNs (Número de Sistema Autónomo) que pode ser usada para anunciar as suas rotas no local para HANA Large Instances. Os seus locais não devem anunciar quaisquer rotas com ASNs privadas na gama de 65000 - 65020 ou 65515. 
- Para o cenário de ligar acesso direto ao HANA Grandes instâncias, é necessário calcular uma taxa para o circuito que o liga ao Azure. Para obter preços, verifique os preços do [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/).

Para obter um ou ambos os cenários aplicados à sua implantação, abra uma mensagem de apoio com a Azure, conforme descrito no Open um pedido de [apoio para HANA grandes Instâncias](./hana-li-portal.md#open-a-support-request-for-hana-large-instances)

Os dados necessários e as palavras-chave que precisa de utilizar para a Microsoft poder fazer a rota e executar a seu pedido, parecem:

- Serviço: SAP HANA Grande Instância
- Tipo de problema: Configuração e Configuração
- Subtipo de problema: O meu problema não está listado acima
- Assunto 'Modificar a minha Rede - adicionar Alcance Global'
- Detalhes: 'Adicionar Alcance Global à HANA Large Instance ao inquilino HANA Large Instance ou 'Adicionar Alcance Global aos locais ao inquilino HANA Large Instance.
- Detalhes adicionais para o caso do inquilino HANA Large Instance para HANA Large Instance: Você precisa definir as **duas regiões Azure** onde os dois inquilinos para se conectar **estão** localizados E você precisa submeter a **gama de endereços IP /29**
- Detalhes adicionais para o caso do inquilino HANA Large Instance: Você precisa definir a **Região de Azure** onde o inquilino HANA Large Instance está implantado você quer se conectar diretamente. Além disso, precisa de fornecer o **ID Auth GUID** e **Circuit Peer** que recebeu quando estabeleceu o seu circuito ExpressRoute entre as instalações e o Azure. Além disso, precisa de nomear o seu **ASN**. A última entrega é uma **gama de endereços IP /29** para ExpressRoute Global Reach.

> [!NOTE]
> Se pretender ter ambos os casos tratados, tem de fornecer dois intervalos de endereço IP diferentes /29 que não se sobreponham a qualquer outro intervalo de endereços IP utilizado até agora. 




## <a name="next-steps"></a>Passos seguintes

- [Requisitos adicionais de rede para o HLI](hana-additional-network-requirements.md)
