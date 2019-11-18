---
title: 'Gateway de VPN do Azure: configurar BGP: PowerShell'
description: Este artigo orienta você pela configuração do BGP com gateways de VPN do Azure usando Azure Resource Manager e PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 905b11a7-1333-482c-820b-0fd0f44238e5
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: yushwang
ms.openlocfilehash: f4c5e3ddb5a7f5c9a21d29aa24fe28e095123836
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151387"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>Como configurar o BGP em gateways de VPN do Azure usando o PowerShell
Este artigo orienta você pelas etapas para habilitar o BGP em uma conexão VPN S2S (site a site) entre locais e uma conexão VNet a VNet usando o modelo de implantação do Gerenciador de recursos e o PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-bgp"></a>Sobre o BGP
O BGP é o protocolo de encaminhamento padrão utilizado normalmente na Internet para trocar informações de encaminhamento e acessibilidade entre duas ou mais redes. O BGP permite que os Gateways de VPN do Azure e os seus dispositivos VPN no local, conhecidos como elementos de rede ou vizinhos BGP, troquem “rotas” que informarão ambos os gateways da disponibilidade e acessibilidade para esses prefixos percorrerem os gateways ou routers envolvidos. O BGP também pode permitir o encaminhamento de tráfego entre várias redes ao propagar rotas para todos os outros elementos de rede BGP, que um gateway BGP aprende de um elemento de rede BGP.

Consulte [visão geral do BGP com gateways de VPN do Azure](vpn-gateway-bgp-overview.md) para obter mais discussões sobre os benefícios do BGP e para entender os requisitos técnicos e as considerações de como usar o BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Introdução ao BGP em gateways de VPN do Azure

Este artigo orienta você pelas etapas para executar as seguintes tarefas:

* [Parte 1-habilitar o BGP em seu gateway de VPN do Azure](#enablebgp)
* Parte 2-estabelecer uma conexão entre instalações com BGP
* [Parte 3-estabelecer uma conexão de VNet para VNet com BGP](#v2vbgp)

Cada parte das instruções forma um bloco de construção básico para habilitar o BGP em sua conectividade de rede. Se você concluir todas as três partes, crie a topologia conforme mostrado no diagrama a seguir:

![Topologia BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Você pode combinar partes para criar uma rede de trânsito mais complexa e de vários saltos que atenda às suas necessidades.

## <a name ="enablebgp"></a>Parte 1-configurar o BGP no gateway de VPN do Azure
As etapas de configuração configuram os parâmetros de BGP do gateway de VPN do Azure, conforme mostrado no diagrama a seguir:

![Gateway BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Antes de começar
* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Instale os cmdlets do Azure Resource Manager PowerShell. Para obter mais informações sobre como instalar os cmdlets do PowerShell, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview) . 

### <a name="step-1---create-and-configure-vnet1"></a>Etapa 1-criar e configurar o VNet1
#### <a name="1-declare-your-variables"></a>1. declare suas variáveis
Para este exercício, começamos declarando nossas variáveis. O exemplo a seguir declara as variáveis usando os valores para este exercício. Verifique se substitui os valores pelos seus quando configurar para produção. Pode utilizar estas variáveis se estiver a executar os passos para se familiarizar com este tipo de configuração. Modifique as variáveis e, em seguida, copie e cole a consola do PowerShell.

```powershell
$Sub1 = "Replace_With_Your_Subscription_Name"
$RG1 = "TestBGPRG1"
$Location1 = "East US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GWIPName1 = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection12 = "VNet1toVNet2"
$Connection15 = "VNet1toSite5"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Conecte-se à sua assinatura e crie um novo grupo de recursos
Para usar os cmdlets do Gerenciador de recursos, altere para o modo PowerShell. Para obter mais informações, veja [Utilizar o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

Abra a consola do PowerShell e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar na ligação:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. criar TestVNet1
O exemplo a seguir cria uma rede virtual chamada TestVNet1 e três sub-redes, uma chamada GatewaySubnet, uma chamada FrontEnd e outra chamada back-end. Quando estiver a substituir os valores, é importante que dê sempre à sub-rede do gateway o nome específico GatewaySubnet. Se der outro nome, a criação da gateway falha.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Etapa 2 – criar o gateway de VPN para TestVNet1 com parâmetros de BGP
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. criar as configurações de IP e sub-rede
Peça para que seja atribuído um endereço IP público ao gateway que vai criar para a VNet. Você também definirá a sub-rede necessária e as configurações de IP.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. criar o gateway de VPN com o número as
Crie o gateway de rede virtual para TestVNet1. O BGP requer um gateway de VPN baseado em rota e também o parâmetro de adição,-ASN, para definir o ASN (como número) para TestVNet1. Se você não definir o parâmetro ASN, o ASN 65515 será atribuído. A criação de um gateway pode demorar algum tempo (30 minutos ou mais).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. obter o endereço IP do par de BGP do Azure
Depois que o gateway for criado, você precisará obter o endereço IP do par de BGP no gateway de VPN do Azure. Esse endereço é necessário para configurar o gateway de VPN do Azure como um par de BGP para seus dispositivos VPN locais.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

O último comando mostra as configurações de BGP correspondentes no gateway de VPN do Azure; por exemplo:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Depois que o gateway é criado, você pode usar esse gateway para estabelecer conexão entre locais ou conexão VNet a VNet com BGP. As seções a seguir percorrem as etapas para concluir o exercício.

## <a name ="crossprembbgp"></a>Parte 2-estabelecer uma conexão entre instalações com BGP

Para estabelecer uma conexão entre locais, você precisa criar um gateway de rede local para representar o dispositivo VPN local e uma conexão para conectar o gateway de VPN com o gateway de rede local. Embora existam artigos que orientam você durante essas etapas, este artigo contém as propriedades adicionais necessárias para especificar os parâmetros de configuração do BGP.

![BGP para entre instalações](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Antes de continuar, verifique se você concluiu a [parte 1](#enablebgp) deste exercício.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Etapa 1-criar e configurar o gateway de rede local

#### <a name="1-declare-your-variables"></a>1. declare suas variáveis

Este exercício continua a criar a configuração mostrada no diagrama. Confirme que substitui os valores pelos que pretende utilizar para a configuração.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Algumas coisas a serem observadas em relação aos parâmetros do gateway de rede local:

* O gateway de rede local pode estar no mesmo local ou em um grupo de recursos diferente do gateway de VPN. Este exemplo os mostra em diferentes grupos de recursos em locais diferentes.
* O prefixo que você precisa declarar para o gateway de rede local é o endereço de host do seu endereço IP do par de BGP em seu dispositivo VPN. Nesse caso, é um prefixo/32 de "10.52.255.254/32".
* Como lembrete, você deve usar diferentes ASNs BGP entre suas redes locais e a VNet do Azure. Se eles forem os mesmos, você precisará alterar seu ASN de VNet se o dispositivo VPN local já usa o ASN para emparelhar com outros vizinhos de BGP.

Antes de continuar, verifique se ainda está ligado à Subscrição 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. criar o gateway de rede local para site5

Certifique-se de criar o grupo de recursos se ele não for criado, antes de criar o gateway de rede local. Observe os dois parâmetros adicionais para o gateway de rede local: ASN e BgpPeerAddress.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Etapa 2 – conectar o gateway de VNet e o gateway de rede local

#### <a name="1-get-the-two-gateways"></a>1. obter os dois gateways

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. criar o TestVNet1 para a conexão site5

Nesta etapa, você cria a conexão de TestVNet1 para site5. Você deve especificar "-EnableBGP $True" para habilitar o BGP para esta conexão. Conforme discutido anteriormente, é possível ter conexões BGP e não BGP para o mesmo gateway de VPN do Azure. A menos que o BGP esteja habilitado na propriedade de conexão, o Azure não habilitará o BGP para essa conexão, embora os parâmetros BGP já estejam configurados em ambos os gateways.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

O exemplo a seguir lista os parâmetros que você insere na seção de configuração de BGP em seu dispositivo VPN local para este exercício:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

A conexão é estabelecida após alguns minutos, e a sessão de emparelhamento via protocolo BGP é iniciada quando a conexão IPsec é estabelecida.

## <a name ="v2vbgp"></a>Parte 3-estabelecer uma conexão de VNet para VNet com BGP

Esta seção adiciona uma conexão VNet a VNet com BGP, conforme mostrado no diagrama a seguir:

![BGP para vnet a VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

As instruções a seguir continuam das etapas anteriores. Você deve concluir a [parte I](#enablebgp) para criar e configurar o TestVNet1 e o gateway de VPN com BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Etapa 1-criar TestVNet2 e o gateway de VPN

É importante certificar-se de que o espaço de endereço IP da nova rede virtual, TestVNet2, não se sobreponha a nenhum dos seus intervalos de VNet.

Neste exemplo, as redes virtuais pertencem à mesma assinatura. Você pode configurar conexões de VNet para VNet entre assinaturas diferentes. Para obter mais informações, consulte [Configurar uma conexão de vnet para vnet](vpn-gateway-vnet-vnet-rm-ps.md). Certifique-se de adicionar o "-EnableBgp $True" ao criar as conexões para habilitar o BGP.

#### <a name="1-declare-your-variables"></a>1. declare suas variáveis

Confirme que substitui os valores pelos que pretende utilizar para a configuração.

```powershell
$RG2 = "TestBGPRG2"
$Location2 = "West US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GWIPName2 = "VNet2GWIP"
$GWIPconfName2 = "gwipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. criar TestVNet2 no novo grupo de recursos

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. criar o gateway de VPN para TestVNet2 com parâmetros BGP

Solicite um endereço IP público a ser alocado para o gateway que você criará para sua VNet e defina as configurações de sub-rede e IP necessárias.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Crie o gateway de VPN com o número as. Você deve substituir o ASN padrão em seus gateways de VPN do Azure. O ASNs para o VNets conectado deve ser diferente para habilitar o BGP e o roteamento de trânsito.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Etapa 2 – conectar os gateways TestVNet1 e TestVNet2

Neste exemplo, ambos os gateways estão na mesma assinatura. Você pode concluir esta etapa na mesma sessão do PowerShell.

#### <a name="1-get-both-gateways"></a>1. obter ambos os gateways

Confirme que inicia sessão e se liga à Subscrição 1.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. criar ambas as conexões

Nesta etapa, você cria a conexão de TestVNet1 para TestVNet2 e a conexão de TestVNet2 para TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Certifique-se de habilitar o BGP para ambas as conexões.
> 
> 

Depois de concluir essas etapas, a conexão será estabelecida após alguns minutos. A sessão de emparelhamento via protocolo BGP é ativada quando a conexão VNet para VNet é concluída.

Se você concluiu todas as três partes deste exercício, você estabeleceu a seguinte topologia de rede:

![BGP para vnet a VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Passos seguintes

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter os passos.
