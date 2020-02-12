---
title: Configure ligações ativas S2S Azure VPN Gateway
description: Este artigo acompanha-o através da configuração de ligações ativas com gateways Azure VPN utilizando o Azure Resource Manager e o PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/24/2018
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: ec3697208434eb971e47136416f2c2cc541b5cea
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152079"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Configure ligações s2S VPN ativas com Gateways Azure VPN

Este artigo percorre-o através dos passos para criar inter-instalações inter-instalações ativas e ligações VNet-to-VNet utilizando o modelo de implementação do Gestor de Recursos e powerShell.



## <a name="about-highly-available-cross-premises-connections"></a>Sobre ligações transfronteiriças altamente disponíveis
Para obter uma elevada disponibilidade para as instalações cruzadas e conectividade VNet-to-VNet, deverá implementar vários gateways VPN e estabelecer múltiplas ligações paralelas entre as suas redes e o Azure. Consulte as [cross-premissas altamente disponíveis e a conectividade VNet-to-VNet](vpn-gateway-highlyavailable.md) para obter uma visão geral das opções de conectividade e topologia.

Este artigo fornece as instruções para criar uma ligação VPN de instalações cruzadas ativas e ligação ativa entre duas redes virtuais.

* [Parte 1 - Crie e configure o seu gateway Azure VPN em modo ativo ativo](#aagateway)
* [Parte 2 - Estabelecer ligações inter-instalações ativas](#aacrossprem)
* [Parte 3 - Estabelecer ligações VNet-to-VNet ativas](#aav2v)

Se já tem um gateway VPN, pode:
* [Atualizar um gateway VPN existente de standby ativo para ativo-activo, ou vice-versa](#aaupdate)

Você pode combiná-los juntos para construir uma topologia de rede mais complexa e altamente disponível que atenda às suas necessidades.

> [!IMPORTANT]
> O modo ativo utiliza apenas as seguintes SKUs: 
>   * VpnGw1, VpnGw2, VpnGw3
>   * HighPerformance (para antigas SKUs legados)

## <a name ="aagateway"></a>Parte 1 - Criar e configurar gateways VPN ativos
Os seguintes passos configurarão o seu gateway Azure VPN em modos ativos ativos. As principais diferenças entre os gateways ativos ativos e ativos:

* Você precisa criar duas configurações IP Gateway com dois endereços IP públicos
* Precisa definir a bandeira EnableActiveActiveFeature
* O gateway SKU deve ser VpnGw1, VpnGw2, VpnGw3 ou HighPerformance (legacy SKU).

As outras propriedades são as mesmas que os gateways não ativos. 

### <a name="before-you-begin"></a>Antes de começar
* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Terá de instalar os cmdlets PowerShell do Azure Resource Manager. Consulte a [visão geral do Azure PowerShell](/powershell/azure/overview) para obter mais informações sobre a instalação dos cmdlets PowerShell.

### <a name="step-1---create-and-configure-vnet1"></a>Passo 1 - Criar e configurar VNet1
#### <a name="1-declare-your-variables"></a>1. Declare as suas variáveis
Para este exercício, vamos começar por declarar as nossas variáveis. O exemplo abaixo declara as variáveis com os valores deste exercício. Verifique se substitui os valores pelos seus quando configurar para produção. Pode utilizar estas variáveis se estiver a executar os passos para se familiarizar com este tipo de configuração. Modifique as variáveis e, em seguida, copie e cole a consola do PowerShell.

```powershell
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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Ligue-se à sua subscrição e crie um novo grupo de recursos
Confirme que muda para o modo do PowerShell para utilizar os cmdlets do Resource Manager. Para obter mais informações, veja [Utilizar o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

Abra a consola do PowerShell e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar na ligação:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Criar testvnet1
O exemplo abaixo cria uma rede virtual denominada TestVNet1 e três sub-redes: GatewaySubnet, Front-end e Back-end. Quando estiver a substituir os valores, é importante que dê sempre à sub-rede do gateway o nome específico GatewaySubnet. Se der outro nome, a criação da gateway falha.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Passo 2 - Criar a porta de entrada VPN para TestVNet1 com modo ativo
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Criar os endereços IP públicos e as configurações IP gateway
Solicite dois endereços IP públicos para serem atribuídos ao portal que irá criar para o seu VNet. Também definirá as configurações de sub-rede e IP necessárias.

```powershell
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Criar o gateway VPN com configuração ativa
Crie o gateway de rede virtual para TestVNet1. Note que existem duas entradas GatewayIpConfig e a bandeira EnableActiveActiveFeature está definida. A criação de um gateway pode demorar algum tempo (45 minutos ou mais).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Obter os endereços IP públicos de gateway e o endereço IP do BGP Peer
Assim que o portal for criado, terá de obter o endereço IP BGP Peer no Gateway VpN Azure. Este endereço é necessário para configurar o Azure VPN Gateway como um BGP Peer para os seus dispositivos VPN no local.

```powershell
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Utilize os seguintes cmdlets para mostrar os dois endereços IP públicos atribuídos para o seu gateway VPN, e os seus endereços IP bGP correspondentes para cada instância de gateway:

```powershell
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

A ordem dos endereços IP públicos para as instâncias de gateway e os endereços de pares bGP correspondentes são os mesmos. Neste exemplo, o gateway VM com IP público de 40.112.190.5 utilizará 10.12.255.4 como seu Endereço de Peering BGP, e o portal com 138.91.156.129 utilizará 10.12.255.5. Esta informação é necessária quando configura os seus dispositivos VPN no local, ligando-se ao gateway ativo. O gateway é mostrado no diagrama abaixo com todos os endereços:

![gateway ativo-ativo](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Uma vez criado o gateway, pode utilizar esta porta de entrada para estabelecer instalações cruzadas ativas ou ligação VNet-to-VNet. As seguintes secções percorrem os degraus para completar o exercício.

## <a name ="aacrossprem"></a>Parte 2 - Estabelecer uma ligação inter-instalações ativas
Para estabelecer uma ligação transversal às instalações, é necessário criar um Gateway de Rede Local para representar o seu dispositivo VPN no local e uma Ligação para ligar o gateway Azure VPN ao gateway da rede local. Neste exemplo, o gateway Azure VPN encontra-se em modo ativo. Como resultado, apesar de existir apenas um dispositivo VPN no local (gateway de rede local) e um recurso de ligação, ambas as instâncias de gateway VPN Azure estabelecerão túneis S2S VPN com o dispositivo no local.

Antes de prosseguir, certifique-se de que completou a [Parte 1](#aagateway) deste exercício.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Passo 1 - Criar e configurar o portal da rede local
#### <a name="1-declare-your-variables"></a>1. Declare as suas variáveis
Este exercício continuará a construir a configuração mostrada no diagrama. Confirme que substitui os valores pelos que pretende utilizar para a configuração.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Algumas coisas a notar sobre os parâmetros de gateway da rede local:

* O portal de rede local pode estar no mesmo local ou diferente e grupo de recursos que o gateway VPN. Este exemplo mostra-os em diferentes grupos de recursos, mas na mesma localização Azure.
* Se houver apenas um dispositivo VPN no local, como mostrado acima, a ligação ativa pode funcionar com ou sem protocolo BGP. Este exemplo utiliza BGP para a ligação transversal.
* Se o BGP estiver ativado, o prefixo que precisa de declarar para o gateway da rede local é o endereço de anfitrião do seu endereço IP BGP Peer no seu dispositivo VPN. Neste caso, é um prefixo /32 de "10.52.255.253/32".
* Como lembrete, deve utilizar diferentes ASNs BGP entre as suas redes no local e o Azure VNet. Se forem os mesmos, tem de alterar o seu VNet ASN se o seu dispositivo VPN no local já utilizar o ASN para fazer pares com outros vizinhos do BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Criar a porta de entrada da rede local para o Site5
Antes de continuar, verifique se ainda está ligado à Subscrição 1. Crie o grupo de recursos se ainda não estiver criado.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Passo 2 - Ligue o gateway VNet e o gateway da rede local
#### <a name="1-get-the-two-gateways"></a>1. Obtenha os dois portais

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Criar o TestVNet1 para a ligação Site5
Neste passo, cria-se a ligação do TestVNet1 para Site5_1 com o "EnableBGP" definido para $True.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. Parâmetros VPN e BGP para o seu dispositivo VPN no local
O exemplo abaixo lista os parâmetros que introduzirá na secção de configuração BGP no seu dispositivo VPN no local para este exercício:

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

A ligação deve ser estabelecida após alguns minutos, e a sessão de observação do BGP começará assim que a ligação IPsec for estabelecida. Este exemplo até agora configuraa apenas um dispositivo VPN no local, resultando no diagrama abaixo:

![active-active-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Passo 3 - Ligue dois dispositivos VPN no local ao gateway VPN ativo
Se tiver dois dispositivos VPN na mesma rede no local, pode obter uma dupla redundância ligando a porta de entrada Azure VPN ao segundo dispositivo VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Criar a segunda porta de entrada de rede local para o Site5
O endereço IP gateway, o prefixo de endereço e o endereço de observação de BGP para o segundo portal de rede local não devem sobrepor-se à anterior porta de entrada de rede local para a mesma rede no local.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Ligue o gateway VNet e o segundo portal de rede local
Criar a ligação do TestVNet1 para Site5_2 com "EnableBGP" definido para $True

```powershell
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
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

Uma vez estabelecida a ligação (túneis), terá dois dispositivos VPN redundantes e túneis que ligam a sua rede no local e o Azure:

![dupla-redundância-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>Parte 3 - Estabelecer uma ligação VNet-to-VNet ativa
Esta secção cria uma ligação VNet-to-VNet ativa com BGP. 

As instruções abaixo são a continuação dos passos anteriores listados acima. Tem de completar a [Parte 1](#aagateway) para criar e configurar o TestVNet1 e o VpN Gateway com BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Passo 1 - Criar TestVNet2 e o gateway VPN
É importante certificar-se de que o espaço de endereço IP da nova rede virtual, testVNet2, não se sobrepõe a nenhuma das suas gamas VNet.

Neste exemplo, as redes virtuais pertencem à mesma subscrição. Pode configurar ligações VNet-to-VNet entre diferentes subscrições; por favor, consulte a [Configuração de uma ligação VNet-to-VNet](vpn-gateway-vnet-vnet-rm-ps.md) para saber mais detalhes. Certifique-se de que adiciona o "-EnableBgp $True" ao criar as ligações para ativar o BGP.

#### <a name="1-declare-your-variables"></a>1. Declare as suas variáveis
Confirme que substitui os valores pelos que pretende utilizar para a configuração.

```powershell
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

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Criar testVNet2 no novo grupo de recursos

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Criar a porta de entrada VPN ativa para testVNet2
Solicite dois endereços IP públicos para serem atribuídos ao portal que irá criar para o seu VNet. Também definirá as configurações de sub-rede e IP necessárias.

```powershell
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Crie o gateway VPN com o número AS e a bandeira "EnableActiveActiveFeature". Note que deve anular o ASN padrão nos seus gateways VpN Azure. As ASNs para os VNets ligados devem ser diferentes para permitir o encaminhamento de BGP e trânsito.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Passo 2 - Ligue os gateways TestVNet1 e TestVNet2
Neste exemplo, ambos os gateways estão na mesma subscrição. Pode completar este passo na mesma sessão powerShell.

#### <a name="1-get-both-gateways"></a>1. Obter ambos os gateways
Confirme que inicia sessão e se liga à Subscrição 1.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Criar ambas as ligações
Neste passo, irá criar a ligação de TestVNet1 a TestVNet2, e a ligação de TestVNet2 a TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Certifique-se de que ativa o BGP para ambas as ligações.
> 
> 

Após completar estes passos, a ligação será estabelecido em poucos minutos, e a sessão de peering bGP será completa da doada assim que a ligação VNet-to-VNet for concluída com dupla redundância:

![active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Atualizar um gateway VPN existente

Esta secção ajuda-o a alterar um gateway Azure VPN existente de standby ativo para modo ativo ativo, ou vice-versa.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Mude uma porta de entrada ativa para um portal ativo ativo

O exemplo seguinte converte uma porta de entrada ativa em um portal ativo. Quando muda uma porta de entrada ativa para ativa, cria outro endereço IP público e, em seguida, adiciona uma segunda configuração IP gateway.

#### <a name="1-declare-your-variables"></a>1. Declare as suas variáveis

Substitua os seguintes parâmetros utilizados para os exemplos com as definições que necessita para a sua própria configuração e, em seguida, declare estas variáveis.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Depois de declarar as variáveis, pode copiar e colar este exemplo à sua consola PowerShell.

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Crie o endereço IP público e, em seguida, adicione a segunda configuração IP gateway

```powershell
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Ativar o modo ativo e atualizar o gateway

Neste passo, ativa o modo ativo e atualiza o gateway. No exemplo, o gateway VPN está atualmente a usar um legado Standard SKU. No entanto, o ativo ativo não suporta o SKU Padrão. Para redimensionar o legado SKU para um que é suportado (neste caso, HighPerformance), simplesmente especifica o legado suportado SKU que pretende utilizar.

* Não pode mudar um Legado SKU para um dos novos SKUs usando este passo. Você só pode redimensionar um legado SKU para outro legado apoiado SKU. Por exemplo, não pode mudar o SKU de Standard para VpnGw1 (embora vpnGw1 seja suportado para ativo ativo) porque a Standard é um legado SKU e VpnGw1 é um SKU atual. Para obter mais informações sobre redimensionamento e migração de SKUs, consulte [Gateway SKUs](vpn-gateway-about-vpngateways.md#gwsku).

* Se quiser redimensionar um SKU atual, por exemplo VpnGw1 para VpnGw3, pode fazê-lo usando este passo porque as SKUs estão na mesma família SKU. Para isso, usaria o valor: ```-GatewaySku VpnGw3```

Quando estiver a usar isto no seu ambiente, se não precisar de redimensionar o portal, não precisará especificar o GatewaySku. Note que neste passo, deve definir o objeto de gateway no PowerShell para desencadear a atualização real. Esta atualização pode demorar entre 30 a 45 minutos, mesmo que não esteja a redimensionar o seu portal.

```powershell
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Mude uma porta de entrada ativa para um gateway de standby ativo
#### <a name="1-declare-your-variables"></a>1. Declare as suas variáveis

Substitua os seguintes parâmetros utilizados para os exemplos com as definições que necessita para a sua própria configuração e, em seguida, declare estas variáveis.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Depois de declarar as variáveis, obtenha o nome da configuração IP que pretende remover.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Remova a configuração IP do gateway e desative o modo ativo

Utilize este exemplo para remover a configuração IP do gateway e desativar o modo ativo. Note que deve definir o objeto de gateway no PowerShell para desencadear a atualização real.

```powershell
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Esta atualização pode demorar entre 30 a 45 minutos.

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter os passos.
