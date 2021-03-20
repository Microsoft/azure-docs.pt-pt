---
title: Configure ligações ativas S2S Azure VPN Gateway
description: Este artigo acompanha-o através da configuração de ligações ativas ativas com gateways Azure VPN usando Azure Resource Manager e PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: 022ccaab0b210cd2d656b69f505791d1a2aa963f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89440784"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Configure ligações S2S VPN ativas ativas com gateways Azure VPN

Este artigo percorre os passos para criar instalações cruzadas ativas e ligações VNet-to-VNet utilizando o modelo de implementação do Gestor de Recursos e o PowerShell. Também pode configurar uma porta de entrada ativa no portal Azure.

## <a name="about-highly-available-cross-premises-connections"></a>Sobre ligações transversais altamente disponíveis
Para obter uma elevada disponibilidade para as instalações cruzadas e conectividade VNet-to-VNet, deverá implantar vários gateways VPN e estabelecer múltiplas ligações paralelas entre as suas redes e o Azure. Consulte [as instalações cruzadas altamente disponíveis e a conectividade VNet-to-VNet](vpn-gateway-highlyavailable.md) para uma visão geral das opções de conectividade e topologia.

Este artigo fornece as instruções para a criação de uma ligação VPN ativa e ativa entre duas redes virtuais.

* [Parte 1 - Crie e configuure o seu gateway Azure VPN em modo ativo](#aagateway)
* [Parte 2 - Estabelecer ligações cruzadas ativas](#aacrossprem)
* [Parte 3 - Estabelecer ligações VNet-to-VNet ativas ativas](#aav2v)

Se já tem uma porta VPN, pode:
* [Atualizar uma porta VPN existente de standby ativo para ativo, ou vice-versa](#aaupdate)

Você pode combinar estes para construir uma topologia de rede mais complexa e altamente disponível que atenda às suas necessidades.

> [!IMPORTANT]
> O modo ative está disponível para todos os SKUs, exceto basic.

## <a name="part-1---create-and-configure-active-active-vpn-gateways"></a><a name ="aagateway"></a>Parte 1 - Criar e configurar gateways VPN ativos ativos
Os seguintes passos configurarão o seu gateway Azure VPN em modos ativos. As principais diferenças entre os gateways ativos e ativos:

* Você precisa criar duas configurações GATEWAY IP com dois endereços IP públicos
* Precisa definir a bandeira EnableActiveActiveFeature
* O gateway SKU deve ser VpnGw1, VpnGw2, VpnGw3 ou HighPerformance (legado SKU).

As outras propriedades são as mesmas que os gateways não ativos. 

### <a name="before-you-begin"></a>Antes de começar
* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Terá de instalar os cmdlets PowerShell do Azure Resource Manager se não quiser utilizar o CloudShell no seu navegador. Consulte [a visão geral do Azure PowerShell](/powershell/azure/) para obter mais informações sobre a instalação dos cmdlets PowerShell.

### <a name="step-1---create-and-configure-vnet1"></a>Passo 1 - Criar e configurar vNet1
#### <a name="1-declare-your-variables"></a>1. Declare as suas variáveis

Para este exercício, vamos começar por declarar as nossas variáveis. Se utilizar a Cloud Shell "Try It", irá ligar-se automaticamente à sua conta. Se utilizar o PowerShell localmente, utilize o seguinte exemplo para o ajudar a ligar:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
```

O exemplo abaixo declara as variáveis com os valores deste exercício. Verifique se substitui os valores pelos seus quando configurar para produção. Pode utilizar estas variáveis se estiver a executar os passos para se familiarizar com este tipo de configuração. Modifique as variáveis e, em seguida, copie e cole a consola do PowerShell.

```azurepowershell-interactive
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
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
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-create-a-new-resource-group"></a>2. Criar um novo grupo de recursos

Use o exemplo abaixo para criar um novo grupo de recursos:

```azurepowershell-interactive
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Criar TestVNet1
O exemplo abaixo cria uma rede virtual denominada TestVNet1 e três sub-redes: GatewaySubnet, Front-end e Back-end. Quando estiver a substituir os valores, é importante que dê sempre à sub-rede do gateway o nome específico GatewaySubnet. Se der outro nome, a criação da gateway falha.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Passo 2 - Criar o gateway VPN para o TestVNet1 com modo ativo
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Criar os endereços IP públicos e as configurações IP gateway
Solicite dois endereços IP públicos a serem atribuídos ao portal que irá criar para o seu VNet. Também definirá as configurações de sub-rede e IP necessárias.

```azurepowershell-interactive
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Criar a porta de entrada VPN com configuração ativa
Crie o gateway de rede virtual para TestVNet1. Note que existem duas entradas GatewayIpConfig, e a bandeira EnableActiveActiveFeature está definida. A criação de um gateway pode demorar algum tempo (45 minutos ou mais).

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Obtenha os endereços IP públicos gateway e o endereço IP do BGP Peer
Uma vez criado o gateway, terá de obter o endereço IP BGP Peer no Gateway Azure VPN. Este endereço é necessário para configurar o Azure VPN Gateway como um BGP Peer para os seus dispositivos VPN no local.

```azurepowershell-interactive
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Utilize os seguintes cmdlets para mostrar os dois endereços IP públicos atribuídos para o seu gateway VPN e os respetivos endereços IP peer BGP para cada instância de gateway:

```azurepowershell-interactive
PS D:\> $gw1pip1.IpAddress
40.112.190.5

PS D:\> $gw1pip2.IpAddress
138.91.156.129

PS D:\> $vnet1gw.BgpSettingsText
{
  "Asn": 65010,
  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
  "PeerWeight": 0
}
```

A ordem dos endereços IP públicos para as instâncias de gateway e os endereços de peering BGP correspondentes são os mesmos. Neste exemplo, o gateway VM com IP público de 40.112.190.5 utilizará 10.12.255.4 como endereço de peering BGP, e o gateway com 138.91.156.129 utilizará 10.12.255,5. Estas informações são necessárias quando configurar os seus dispositivos VPN nas instalações que se conectam ao gateway ativo. O gateway é mostrado no diagrama abaixo com todos os endereços:

![gateway ativo](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Uma vez criado o gateway, pode utilizar este gateway para estabelecer premissas cruzadas ativas ou ligação VNet-to-VNet. As secções seguintes percorrem os degraus para completar o exercício.

## <a name="part-2---establish-an-active-active-cross-premises-connection"></a><a name ="aacrossprem"></a>Parte 2 - Estabelecer uma ligação transversal ativa
Para estabelecer uma ligação transversal, é necessário criar um Gateway de rede local para representar o seu dispositivo VPN no local e uma Ligação para ligar o gateway VPN Azure com o gateway de rede local. Neste exemplo, o gateway Azure VPN encontra-se em modo ativo. Como resultado, embora exista apenas um dispositivo VPN no local (gateway de rede local) e um recurso de conexão, ambas as instâncias de gateway Azure VPN estabelecerão túneis VPN S2S com o dispositivo no local.

Antes de prosseguir, certifique-se de ter concluído [a Parte 1](#aagateway) deste exercício.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Passo 1 - Criar e configurar o portal de rede local
#### <a name="1-declare-your-variables"></a>1. Declare as suas variáveis
Este exercício continuará a construir a configuração mostrada no diagrama. Confirme que substitui os valores pelos que pretende utilizar para a configuração.

```azurepowershell-interactive
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Algumas coisas a notar sobre os parâmetros de gateway de rede local:

* O gateway de rede local pode estar na mesma localização ou grupo de recursos diferentes que o gateway VPN. Este exemplo mostra-os em diferentes grupos de recursos, mas na mesma localização Azure.
* Se existir apenas um dispositivo VPN no local, como mostrado acima, a ligação ativa ativa pode funcionar com ou sem protocolo BGP. Este exemplo utiliza BGP para a ligação entre instalações.
* Se o BGP estiver ativado, o prefixo que precisa de declarar para o gateway de rede local é o endereço de anfitrião do seu endereço IP BGP Peer no seu dispositivo VPN. Neste caso, é um prefixo /32 de "10.52.255.253/32".
* Como lembrete, deve utilizar diferentes ASNs BGP entre as suas redes no local e Azure VNet. Se forem iguais, tem de alterar o seu VNet ASN se o seu dispositivo VPN no local já utilizar a ASN para espreitar com outros vizinhos do BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Criar a porta de entrada de rede local para o Site5
Antes de continuar, verifique se ainda está ligado à Subscrição 1. Crie o grupo de recursos se ainda não estiver criado.

```azurepowershell-interactive
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Passo 2 - Ligue o gateway VNet e o gateway de rede local
#### <a name="1-get-the-two-gateways"></a>1. Obter os dois portais

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Criar a ligação TestVNet1 ao Site5
Neste passo, cria-se a ligação de TestVNet1 a Site5_1 com o conjunto "EnableBGP" definido para $True.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. Parâmetros VPN e BGP para o seu dispositivo VPN no local
O exemplo abaixo lista os parâmetros que irá introduzir na secção de configuração BGP no seu dispositivo VPN no local para este exercício:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.253
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

A ligação deve ser estabelecida após alguns minutos, e a sessão de observação do BGP começará assim que a ligação IPsec estiver estabelecida. Até agora, este exemplo configurava apenas um dispositivo VPN no local, resultando no diagrama apresentado abaixo:

![activa-activa-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Passo 3 - Ligue dois dispositivos VPN no local à porta de VPN ativa ativa
Se tiver dois dispositivos VPN na mesma rede no local, pode obter uma dupla redundância ligando a porta de entrada Azure VPN ao segundo dispositivo VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Criar a segunda porta de entrada de rede local para o Site5
O endereço IP gateway, o prefixo de endereço e o endereço de observação BGP para o segundo gateway de rede local não devem sobrepor-se à anterior porta de entrada de rede local para a mesma rede no local.

```azurepowershell-interactive
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Ligue o portal VNet e o segundo portal de rede local
Crie a ligação de TestVNet1 a Site5_2 com "EnableBGP" definido para $True

```azurepowershell-interactive
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. Parâmetros VPN e BGP para o seu segundo dispositivo VPN no local
Da mesma forma, abaixo lista os parâmetros que introduzirá no segundo dispositivo VPN:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Assim que a ligação (túneis) estiver estabelecida, terá dois dispositivos e túneis VPN redundantes que ligam a sua rede no local e a Azure:

![dupla redundância-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name="part-3---establish-an-active-active-vnet-to-vnet-connection"></a><a name ="aav2v"></a>Parte 3 - Estabelecer uma ligação VNet-to-VNet ativa ativa
Esta secção cria uma ligação VNet-vNet ativa ativa com o BGP. 

As instruções abaixo são a continuação dos passos anteriores listados acima. Tem de completar a [Parte 1](#aagateway) para criar e configurar o TestVNet1 e o Gateway VPN com bGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Passo 1 - Criar TestVNet2 e o gateway VPN
É importante garantir que o espaço de endereço IP da nova rede virtual, TestVNet2, não se sobreponha a nenhuma das suas gamas VNet.

Neste exemplo, as redes virtuais pertencem à mesma subscrição. Pode configurar ligações VNet-vNet entre diferentes subscrições; consulte a [Configure uma ligação VNet-to-VNet](vpn-gateway-vnet-vnet-rm-ps.md) para saber mais detalhes. Certifique-se de que adiciona o "EnableBgp $True" ao criar as ligações para ativar o BGP.

#### <a name="1-declare-your-variables"></a>1. Declare as suas variáveis
Confirme que substitui os valores pelos que pretende utilizar para a configuração.

```azurepowershell-interactive
$RG2 = "TestAARG2"
$Location2 = "East US"
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
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Criar TestVNet2 no novo grupo de recursos

```azurepowershell-interactive
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Criar o portal VPN ativo para o TestVNet2
Solicite dois endereços IP públicos a serem atribuídos ao portal que irá criar para o seu VNet. Também definirá as configurações de sub-rede e IP necessárias.

```azurepowershell-interactive
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Crie o gateway VPN com o número AS e a bandeira "EnableActiveActiveFeature". Note que deve anular a ASN padrão nas suas portas Azure VPN. As ASNs para os VNets ligados devem ser diferentes para permitir o encaminhamento de BGP e de trânsito.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Passo 2 - Ligue os gateways TestVNet1 e TestVNet2
Neste exemplo, ambos os gateways estão na mesma subscrição. Pode completar este passo na mesma sessão PowerShell.

#### <a name="1-get-both-gateways"></a>1. Obtenha ambos os gateways
Confirme que inicia sessão e se liga à Subscrição 1.

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Criar ambas as ligações
Neste passo, irá criar a ligação de TestVNet1 a TestVNet2, e a ligação de TestVNet2 a TestVNet1.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Certifique-se de que ativa o BGP para ambas as ligações.
> 
> 

Após completar estes passos, a ligação será criada em poucos minutos, e a sessão de observação BGP estará de pé assim que a ligação VNet-vNet estiver concluída com dupla redundância:

![active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name="update-an-existing-vpn-gateway"></a><a name ="aaupdate"></a>Atualize um portal VPN existente

Quando altera uma porta de entrada de espera ativa para ativa, cria outro endereço IP público e, em seguida, adiciona uma segunda configuração IP gateway. Esta secção ajuda-o a alterar uma porta de entrada Azure VPN existente de modo ativo para ativo, ou vice-versa usando o PowerShell. Também pode alterar um gateway no portal Azure na página **Configuração** para o seu gateway de rede virtual.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Mude uma porta de entrada de espera ativa para um gateway ativo

O exemplo seguinte converte uma porta de entrada ativa em um gateway ativo. 

#### <a name="1-declare-your-variables"></a>1. Declare as suas variáveis

Substitua os seguintes parâmetros utilizados para os exemplos com as definições que necessita para a sua própria configuração e, em seguida, declare estas variáveis.

```azurepowershell-interactive
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Depois de declarar as variáveis, pode copiar e colar este exemplo à sua consola PowerShell.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Criar o endereço IP público e, em seguida, adicionar a segunda configuração IP gateway

```azurepowershell-interactive
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Ativar o modo ativo e atualizar o gateway

Neste passo, ativa o modo ativo e atualiza o gateway. No exemplo, o gateway VPN está atualmente a usar um SKU standard legado. No entanto, o ativo ativo não suporta o SKU Standard. Para redimensionar o legado SKU para um que é suportado (neste caso, HighPerformance), basta especificar o legado suportado SKU que pretende utilizar.

* Não podes mudar um SKU legado para um dos novos SKUs usando este passo. Só pode redimensionar um legado SKU para outro legado apoiado SKU. Por exemplo, não é possível alterar o SKU de Standard para VpnGw1 (mesmo que o VpnGw1 seja suportado para active-active) porque o Standard é um legado SKU e VpnGw1 é um SKU atual. Para obter mais informações sobre o redimensionamento e migração de SKUs, consulte [gateway SKUs](vpn-gateway-about-vpngateways.md#gwsku).

* Se quiser redimensionar um SKU atual, por exemplo VpnGw1 para VpnGw3, pode fazê-lo usando este passo porque os SKUs estão na mesma família SKU. Para tal, utilizaria o valor: ```-GatewaySku VpnGw3```

Quando estiver a usar isto no seu ambiente, se não precisar de redimensionar o gateway, não precisará de especificar o -GatewaySku. Note que neste passo, deve definir o objeto gateway no PowerShell para ativar a atualização real. Esta atualização pode demorar 30 a 45 minutos, mesmo que não esteja a redimensionar o seu gateway.

```azurepowershell-interactive
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Mude uma porta de entrada ativa para um portal de espera ativo
#### <a name="1-declare-your-variables"></a>1. Declare as suas variáveis

Substitua os seguintes parâmetros utilizados para os exemplos com as definições que necessita para a sua própria configuração e, em seguida, declare estas variáveis.

```azurepowershell-interactive
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Depois de declarar as variáveis, obtenha o nome da configuração IP que pretende remover.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Remova a configuração IP do gateway e desative o modo ativo

Utilize este exemplo para remover a configuração IP do gateway e desativar o modo ativo. Note que tem de definir o objeto gateway no PowerShell para ativar a atualização real.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Esta atualização pode demorar até 30 a 45 minutos.

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter os passos.
