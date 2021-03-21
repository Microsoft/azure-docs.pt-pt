---
title: 'Gateway Azure VPN: Configure BGP: PowerShell'
description: Este artigo acompanha-o através da configuração do BGP com gateways Azure VPN usando Azure Resource Manager e PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8573d9e55299382392927b532966a6e6fdd8c439
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659765"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>Como configurar o BGP em Gateways Azure VPN usando PowerShell
Este artigo percorre os passos para permitir o BGP numa ligação VPN site-to-site (S2S) transversal e uma ligação VNet-to-VNet utilizando o modelo de implementação do Gestor de Recursos e powerShell.



## <a name="about-bgp"></a>Sobre o BGP
O BGP é o protocolo de encaminhamento padrão utilizado normalmente na Internet para trocar informações de encaminhamento e acessibilidade entre duas ou mais redes. O BGP permite que os Gateways de VPN do Azure e os seus dispositivos VPN no local, conhecidos como elementos de rede ou vizinhos BGP, troquem “rotas” que informarão ambos os gateways da disponibilidade e acessibilidade para esses prefixos percorrerem os gateways ou routers envolvidos. O BGP também pode permitir o encaminhamento de tráfego entre várias redes ao propagar rotas para todos os outros elementos de rede BGP, que um gateway BGP aprende de um elemento de rede BGP.

Consulte [a visão geral do BGP com a Azure VPN Gateways](vpn-gateway-bgp-overview.md) para mais discussão sobre os benefícios do BGP e para compreender os requisitos técnicos e considerações da utilização do BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Começar com bGP em gateways Azure VPN

Este artigo acompanha-o através dos passos para fazer as seguintes tarefas:

* [Parte 1 - Ativar o BGP no seu gateway Azure VPN](#enablebgp)
* Parte 2 - Estabelecer uma ligação transversal com o BGP
* [Parte 3 - Estabelecer uma ligação VNet-vNet com bGP](#v2vbgp)

Cada parte das instruções forma um bloco básico de construção para permitir o BGP na sua conectividade de rede. Se completar as três partes, constrói a topologia como mostrado no seguinte diagrama:

![Topologia BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Você pode combinar peças juntas para construir uma rede de trânsito mais complexa, multi-hop, que atenda às suas necessidades.

## <a name="part-1---configure-bgp-on-the-azure-vpn-gateway"></a><a name ="enablebgp"></a>Parte 1 - Configure BGP no Gateway Azure VPN
Os passos de configuração configuram os parâmetros BGP do gateway Azure VPN, como mostrado no diagrama seguinte:

![Porta de entrada BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Antes de começar
* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Instale os cmdlets PowerShell do Gestor de Recursos Azure. Para obter mais informações sobre como instalar os cmdlets do PowerShell, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/) . 

### <a name="step-1---create-and-configure-vnet1"></a>Passo 1 - Criar e configurar vNet1
#### <a name="1-declare-your-variables"></a>1. Declare as suas variáveis
Para este exercício, começamos por declarar as nossas variáveis. O exemplo a seguir declara as variáveis utilizando os valores para este exercício. Verifique se substitui os valores pelos seus quando configurar para produção. Pode utilizar estas variáveis se estiver a executar os passos para se familiarizar com este tipo de configuração. Modifique as variáveis e, em seguida, copie e cole a consola do PowerShell.

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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Ligue-se à sua subscrição e crie um novo grupo de recursos
Para utilizar os cmdlets do Gestor de Recursos, certifique-se de que muda para o modo PowerShell. Para obter mais informações, veja [Utilizar o Windows PowerShell com o Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

Abra a consola do PowerShell e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar na ligação:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Criar TestVNet1
A amostra a seguir cria uma rede virtual chamada TestVNet1 e três sub-redes, uma chamada GatewaySubnet, uma chamada FrontEnd, e outra chamada Backend. Quando estiver a substituir os valores, é importante que dê sempre à sub-rede do gateway o nome específico GatewaySubnet. Se der outro nome, a criação da gateway falha.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Passo 2 - Criar o Gateway VPN para testVNet1 com parâmetros BGP
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Criar as configurações IP e sub-rede
Peça para que seja atribuído um endereço IP público ao gateway que vai criar para a VNet. Também definirá as configurações de sub-redes e IP necessárias.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Criar o portal VPN com o número AS
Crie o gateway de rede virtual para TestVNet1. O BGP requer uma Route-Based gateway VPN, e também o parâmetro de adição, -Asn, para definir o ASN (Número AS) para o TestVNet1. Se não definir o parâmetro ASN, é atribuído o ASN 65515. A criação de um gateway pode demorar algum tempo (30 minutos ou mais).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Obtenha o endereço IP Azure BGP Peer
Uma vez criado o gateway, é necessário obter o endereço IP BGP Peer no Gateway Azure VPN. Este endereço é necessário para configurar o Azure VPN Gateway como um BGP Peer para os seus dispositivos VPN no local.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

O último comando mostra as configurações BGP correspondentes no Gateway Azure VPN; Por exemplo:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Uma vez criado o gateway, pode utilizar este gateway para estabelecer a ligação entre instalações ou a ligação VNet-to-VNet com o BGP. As secções seguintes percorrem os degraus para completar o exercício.

## <a name="part-2---establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembbgp"></a>Parte 2 - Estabelecer uma ligação transversal com o BGP

Para estabelecer uma ligação transversal, é necessário criar um Gateway de rede local para representar o seu dispositivo VPN no local e uma Ligação para ligar o gateway VPN com o gateway de rede local. Embora existam artigos que o percorrem através destes passos, este artigo contém as propriedades adicionais necessárias para especificar os parâmetros de configuração do BGP.

![BGP para Cross-Premis](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Antes de prosseguir, certifique-se de ter concluído [a Parte 1](#enablebgp) deste exercício.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Passo 1 - Criar e configurar o portal de rede local

#### <a name="1-declare-your-variables"></a>1. Declare as suas variáveis

Este exercício continua a construir a configuração mostrada no diagrama. Confirme que substitui os valores pelos que pretende utilizar para a configuração.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Algumas coisas a notar sobre os parâmetros de gateway de rede local:

* O gateway de rede local pode estar na mesma localização ou grupo de recursos diferentes que o gateway VPN. Este exemplo mostra-os em diferentes grupos de recursos em diferentes locais.
* O prefixo que precisa de declarar para o gateway de rede local é o endereço de anfitrião do seu endereço IP BGP Peer no seu dispositivo VPN. Neste caso, é um prefixo /32 de "10.52.255.254/32".
* Como lembrete, deve utilizar diferentes ASNs BGP entre as suas redes no local e Azure VNet. Se forem iguais, tem de alterar o seu VNet ASN se o seu dispositivo VPN no local já utilizar a ASN para espreitar com outros vizinhos do BGP.

Antes de continuar, verifique se ainda está ligado à Subscrição 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Criar a porta de entrada de rede local para o Site5

Certifique-se de criar o grupo de recursos se não for criado, antes de criar o gateway de rede local. Note os dois parâmetros adicionais para o gateway de rede local: Asn e BgpPeerAddress.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Passo 2 - Ligue o gateway VNet e o gateway de rede local

#### <a name="1-get-the-two-gateways"></a>1. Obter os dois portais

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Criar a ligação TestVNet1 ao Site5

Neste passo, cria-se a ligação do TestVNet1 ao Site5. Tem de especificar "EnableBGP $True" para ativar o BGP para esta ligação. Tal como discutido anteriormente, é possível ter ligações BGP e não BGP para o mesmo Gateway Azure VPN. A menos que o BGP esteja ativado na propriedade de ligação, o Azure não permitirá o BGP para esta ligação, mesmo que os parâmetros BGP já estejam configurados em ambos os gateways.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

O exemplo a seguir enumera os parâmetros introduzidos na secção de configuração BGP no dispositivo VPN no local para este exercício:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

A ligação é estabelecida após alguns minutos, e a sessão de observação BGP começa assim que a ligação IPsec é estabelecida.

## <a name="part-3---establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Parte 3 - Estabelecer uma ligação VNet-vNet com bGP

Esta secção adiciona uma ligação VNet-to-VNet com BGP, como mostra o seguinte diagrama:

![Diagrama que mostra uma ligação V Net a V Net.](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

As seguintes instruções continuam a partir dos passos anteriores. Tem de completar a [Parte I](#enablebgp) para criar e configurar o TestVNet1 e o Gateway VPN com BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Passo 1 - Criar TestVNet2 e o gateway VPN

É importante garantir que o espaço de endereço IP da nova rede virtual, TestVNet2, não se sobreponha a nenhuma das suas gamas VNet.

Neste exemplo, as redes virtuais pertencem à mesma subscrição. Pode configurar ligações VNet-vNet entre diferentes subscrições. Para obter mais informações, consulte [configurar uma ligação VNet-to-VNet](vpn-gateway-vnet-vnet-rm-ps.md). Certifique-se de que adiciona o "EnableBgp $True" ao criar as ligações para ativar o BGP.

#### <a name="1-declare-your-variables"></a>1. Declare as suas variáveis

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

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Criar TestVNet2 no novo grupo de recursos

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. Criar o gateway VPN para TestVNet2 com parâmetros BGP

Solicite um endereço IP público para ser atribuído ao gateway que irá criar para o seu VNet e defina as configurações de sub-redes e IP necessárias.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Crie o gateway VPN com o número AS. Tem de anular a ASN predefinida nas suas portas Azure VPN. As ASNs para os VNets ligados devem ser diferentes para permitir o encaminhamento de BGP e de trânsito.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Passo 2 - Ligue os gateways TestVNet1 e TestVNet2

Neste exemplo, ambos os gateways estão na mesma subscrição. Pode completar este passo na mesma sessão PowerShell.

#### <a name="1-get-both-gateways"></a>1. Obtenha ambos os gateways

Confirme que inicia sessão e se liga à Subscrição 1.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Criar ambas as ligações

Neste passo, cria-se a ligação de TestVNet1 a TestVNet2 e a ligação do TestVNet2 ao TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Certifique-se de que ativa o BGP para ambas as ligações.
> 
> 

Após completar estes passos, a ligação é estabelecida após alguns minutos. A sessão de observação BGP termina assim que a ligação VNet-vNet estiver concluída.

Se completou as três partes deste exercício, estabeleceu a seguinte topologia de rede:

![BGP para VNet-to-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Passos seguintes

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/windows/quick-create-portal.md) para obter os passos.