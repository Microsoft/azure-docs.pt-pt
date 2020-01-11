---
title: Configurar conexões de gateway de VPN do Azure S2S ativo-ativo
description: Este artigo orienta você pela configuração de conexões ativas-ativas com gateways de VPN do Azure usando o Azure Resource Manager e o PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/24/2018
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: dce05c558423400d095c83800cdcaf85e174e081
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860440"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Configurar conexões VPN S2S ativas-ativas com gateways de VPN do Azure

Este artigo orienta você pelas etapas para criar conexões entre locais e VNet para VNet ativas usando o modelo de implantação do Gerenciador de recursos e o PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-highly-available-cross-premises-connections"></a>Sobre conexões altamente disponíveis entre locais
Para obter alta disponibilidade para conectividade entre locais e VNet a VNet, você deve implantar vários gateways de VPN e estabelecer várias conexões paralelas entre suas redes e o Azure. Consulte [conectividade altamente disponível entre locais e vnet para vnet](vpn-gateway-highlyavailable.md) para obter uma visão geral das opções de conectividade e da topologia.

Este artigo fornece as instruções para configurar uma conexão VPN entre locais ativo-ativo e uma conexão ativa-ativa entre duas redes virtuais.

* [Parte 1-criar e configurar seu gateway de VPN do Azure no modo ativo-ativo](#aagateway)
* [Parte 2-estabelecer conexões entre locais ativo-ativo](#aacrossprem)
* [Parte 3-estabelecer conexões de VNet para VNet de ativo-ativo](#aav2v)

Se você já tiver um gateway de VPN, poderá:
* [Atualizar um gateway de VPN existente de ativo-em espera para ativo-ativo ou vice-versa](#aaupdate)

Você pode combiná-los para criar uma topologia de rede mais complexa e altamente disponível que atenda às suas necessidades.

> [!IMPORTANT]
> O modo ativo-ativo usa apenas as seguintes SKUs: 
>   * VpnGw1, VpnGw2, VpnGw3
>   * HighPerformance (para SKUs antigos herdados)

## <a name ="aagateway"></a>Parte 1-criar e configurar gateways de VPN ativo-ativo
As etapas a seguir configurarão o gateway de VPN do Azure nos modos ativo-ativo. As principais diferenças entre os gateways ativo-ativo e ativo-em espera:

* Você precisa criar duas configurações de IP de gateway com dois endereços IP públicos
* Você precisa definir o sinalizador EnableActiveActiveFeature
* O SKU de gateway deve ser VpnGw1, VpnGw2, VpnGw3 ou HighPerformance (SKU herdado).

As outras propriedades são as mesmas que os gateways não ativos-ativos. 

### <a name="before-you-begin"></a>Antes de começar
* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Terá de instalar os cmdlets PowerShell do Azure Resource Manager. Consulte [visão geral do Azure PowerShell](/powershell/azure/overview) para obter mais informações sobre como instalar os cmdlets do PowerShell.

### <a name="step-1---create-and-configure-vnet1"></a>Etapa 1-criar e configurar o VNet1
#### <a name="1-declare-your-variables"></a>1. declare suas variáveis
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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Conecte-se à sua assinatura e crie um novo grupo de recursos
Confirme que muda para o modo do PowerShell para utilizar os cmdlets do Resource Manager. Para obter mais informações, veja [Utilizar o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

Abra a consola do PowerShell e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar na ligação:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. criar TestVNet1
O exemplo abaixo cria uma rede virtual denominada TestVNet1 e três sub-redes: GatewaySubnet, Front-end e Back-end. Quando estiver a substituir os valores, é importante que dê sempre à sub-rede do gateway o nome específico GatewaySubnet. Se der outro nome, a criação da gateway falha.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Etapa 2 – criar o gateway de VPN para TestVNet1 com o modo ativo-ativo
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. criar os endereços IP públicos e as configurações de IP do gateway
Solicite dois endereços IP públicos a serem alocados para o gateway que você criará para sua VNet. Você também definirá a sub-rede e as configurações de IP necessárias.

```powershell
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. criar o gateway de VPN com a configuração ativa-ativa
Crie o gateway de rede virtual para TestVNet1. Observe que há duas entradas GatewayIpConfig e o sinalizador EnableActiveActiveFeature é definido. A criação de um gateway pode demorar algum tempo (45 minutos ou mais).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. obter os endereços IP públicos do gateway e o endereço IP do par de BGP
Depois que o gateway for criado, você precisará obter o endereço IP do par de BGP no gateway de VPN do Azure. Esse endereço é necessário para configurar o gateway de VPN do Azure como um par de BGP para seus dispositivos VPN locais.

```powershell
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Use os cmdlets a seguir para mostrar os dois endereços IP públicos alocados para seu gateway de VPN e seus endereços IP de par de BGP correspondentes para cada instância de gateway:

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

A ordem dos endereços IP públicos para as instâncias de gateway e os endereços de emparelhamento BGP correspondentes são os mesmos. Neste exemplo, a VM de gateway com o IP público de 40.112.190.5 usará 10.12.255.4 como seu endereço de emparelhamento de BGP e o gateway com 138.91.156.129 usará 10.12.255.5. Essas informações são necessárias quando você configura seus dispositivos VPN locais conectando-se ao gateway ativo-ativo. O gateway é mostrado no diagrama abaixo com todos os endereços:

![Gateway ativo-ativo](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Depois que o gateway é criado, você pode usar esse gateway para estabelecer uma conexão entre locais ou VNet para VNet ativa-ativa. As seções a seguir percorrem as etapas para concluir o exercício.

## <a name ="aacrossprem"></a>Parte 2-estabelecer uma conexão entre locais ativo-ativo
Para estabelecer uma conexão entre locais, você precisa criar um gateway de rede local para representar o dispositivo VPN local e uma conexão para conectar o gateway de VPN do Azure com o gateway de rede local. Neste exemplo, o gateway de VPN do Azure está no modo ativo-ativo. Como resultado, embora haja apenas um dispositivo VPN local (gateway de rede locais) e um recurso de conexão, ambas as instâncias de gateway de VPN do Azure estabelecerão túneis VPN S2S com o dispositivo local.

Antes de continuar, verifique se você concluiu a [parte 1](#aagateway) deste exercício.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Etapa 1-criar e configurar o gateway de rede local
#### <a name="1-declare-your-variables"></a>1. declare suas variáveis
Este exercício continuará a criar a configuração mostrada no diagrama. Confirme que substitui os valores pelos que pretende utilizar para a configuração.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Algumas coisas a serem observadas em relação aos parâmetros do gateway de rede local:

* O gateway de rede local pode estar no mesmo local ou em um grupo de recursos diferente do gateway de VPN. Este exemplo os mostra em grupos de recursos diferentes, mas no mesmo local do Azure.
* Se houver apenas um dispositivo VPN local, como mostrado acima, a conexão ativa-ativa pode funcionar com ou sem o protocolo BGP. Este exemplo usa o BGP para a conexão entre locais.
* Se o BGP estiver habilitado, o prefixo que você precisará declarar para o gateway de rede local é o endereço de host do seu endereço IP de par de BGP em seu dispositivo VPN. Nesse caso, é um prefixo/32 de "10.52.255.253/32".
* Como lembrete, você deve usar diferentes ASNs BGP entre suas redes locais e a VNet do Azure. Se eles forem os mesmos, você precisará alterar seu ASN de VNet se o dispositivo VPN local já usa o ASN para emparelhar com outros vizinhos de BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. criar o gateway de rede local para site5
Antes de continuar, verifique se ainda está ligado à Subscrição 1. Crie o grupo de recursos se ele ainda não tiver sido criado.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Etapa 2 – conectar o gateway de VNet e o gateway de rede local
#### <a name="1-get-the-two-gateways"></a>1. obter os dois gateways

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. criar o TestVNet1 para a conexão site5
Nesta etapa, você cria a conexão de TestVNet1 para Site5_1 com "EnableBGP" definido como $True.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. parâmetros de VPN e BGP para seu dispositivo VPN local
O exemplo a seguir lista os parâmetros que você vai inserir na seção de configuração do BGP no dispositivo VPN local para este exercício:

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

A conexão deve ser estabelecida após alguns minutos, e a sessão de emparelhamento via protocolo BGP será iniciada quando a conexão IPsec for estabelecida. Até agora, este exemplo configurou apenas um dispositivo VPN local, resultando no diagrama mostrado abaixo:

![active-active-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Etapa 3 – conectar dois dispositivos VPN locais ao gateway de VPN ativo-ativo
Se você tiver dois dispositivos VPN na mesma rede local, poderá obter redundância dupla conectando o gateway de VPN do Azure ao segundo dispositivo VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. criar o segundo gateway de rede local para site5
O endereço IP do gateway, o prefixo de endereço e o endereço de emparelhamento via protocolo BGP para o segundo gateway de rede local não devem se sobrepor ao gateway de rede local anterior para a mesma rede no local.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Conecte o gateway de VNet e o segundo gateway de rede local
Crie a conexão de TestVNet1 para Site5_2 com "EnableBGP" definido como $True

```powershell
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. parâmetros de VPN e BGP para seu segundo dispositivo VPN local
Da mesma forma, abaixo lista os parâmetros que você vai inserir no segundo dispositivo VPN:

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

Depois que a conexão (túneis) for estabelecida, você terá dispositivos VPN redundantes duplos e túneis conectando sua rede local e o Azure:

![redundância dupla-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>Parte 3-estabelecer uma conexão de VNet para VNet ativa-ativa
Esta seção cria uma conexão VNet a VNet ativa-ativa com BGP. 

As instruções abaixo são a continuação dos passos anteriores listados acima. Você deve concluir a [parte 1](#aagateway) para criar e configurar o TestVNet1 e o gateway de VPN com BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Etapa 1-criar TestVNet2 e o gateway de VPN
É importante certificar-se de que o espaço de endereço IP da nova rede virtual, TestVNet2, não se sobreponha a nenhum dos seus intervalos de VNet.

Neste exemplo, as redes virtuais pertencem à mesma assinatura. Você pode configurar conexões de VNet para VNet entre assinaturas diferentes; consulte [Configurar uma conexão de vnet para vnet](vpn-gateway-vnet-vnet-rm-ps.md) para saber mais detalhes. Certifique-se de adicionar o "-EnableBgp $True" ao criar as conexões para habilitar o BGP.

#### <a name="1-declare-your-variables"></a>1. declare suas variáveis
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

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. criar TestVNet2 no novo grupo de recursos

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. criar o gateway de VPN ativo-ativo para TestVNet2
Solicite dois endereços IP públicos a serem alocados para o gateway que você criará para sua VNet. Você também definirá a sub-rede e as configurações de IP necessárias.

```powershell
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Crie o gateway de VPN com o número as e o sinalizador "EnableActiveActiveFeature". Observe que você deve substituir o ASN padrão em seus gateways de VPN do Azure. O ASNs para o VNets conectado deve ser diferente para habilitar o BGP e o roteamento de trânsito.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
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
Nesta etapa, você criará a conexão de TestVNet1 para TestVNet2 e a conexão de TestVNet2 para TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Certifique-se de habilitar o BGP para ambas as conexões.
> 
> 

Depois de concluir essas etapas, a conexão será estabelecida em alguns minutos e a sessão de emparelhamento via protocolo BGP será ativada quando a conexão VNet a VNet for concluída com redundância dupla:

![active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Atualizar um gateway de VPN existente

Esta seção ajuda a alterar um gateway de VPN do Azure existente do modo ativo-em espera para ativo-ativo ou vice-versa.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Alterar um gateway ativo-em espera para um gateway ativo-ativo

O exemplo a seguir converte um gateway ativo-em espera em um gateway ativo-ativo. Quando você altera um gateway ativo-em espera para ativo-ativo, você cria outro endereço IP público e, em seguida, adiciona uma configuração de IP do segundo gateway.

#### <a name="1-declare-your-variables"></a>1. declare suas variáveis

Substitua os seguintes parâmetros usados para os exemplos com as configurações necessárias para sua própria configuração e, em seguida, declare essas variáveis.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Depois de declarar as variáveis, você pode copiar e colar este exemplo em seu console do PowerShell.

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Crie o endereço IP público e, em seguida, adicione a configuração de IP do segundo gateway

```powershell
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. habilitar o modo ativo-ativo e atualizar o gateway

Nesta etapa, você habilita o modo ativo-ativo e atualiza o gateway. No exemplo, o gateway de VPN está usando atualmente um SKU padrão herdado. No entanto, Active-Active não oferece suporte ao SKU Standard. Para redimensionar o SKU herdado para um que tenha suporte (nesse caso, HighPerformance), basta especificar o SKU herdado com suporte que você deseja usar.

* Você não pode alterar um SKU herdado para um dos novos SKUs usando essa etapa. Você só pode redimensionar um SKU herdado para outro SKU herdado com suporte. Por exemplo, você não pode alterar a SKU de Standard para VpnGw1 (mesmo que VpnGw1 tenha suporte para ativo-ativo) porque Standard é um SKU herdado e VpnGw1 é uma SKU atual. Para obter mais informações sobre como redimensionar e migrar SKUs, consulte [SKUs de gateway](vpn-gateway-about-vpngateways.md#gwsku).

* Se você quiser redimensionar um SKU atual, por exemplo, VpnGw1 para VpnGw3, poderá fazer isso usando esta etapa porque os SKUs estão na mesma família de SKU. Para fazer isso, você usaria o valor: ```-GatewaySku VpnGw3```

Quando você estiver usando isso em seu ambiente, se não precisar redimensionar o gateway, você não precisará especificar o-GatewaySku. Observe que nesta etapa, você deve definir o objeto de gateway no PowerShell para disparar a atualização real. Essa atualização pode levar de 30 a 45 minutos, mesmo se você não estiver redimensionando seu gateway.

```powershell
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Alterar um gateway ativo-ativo para um gateway ativo-em espera
#### <a name="1-declare-your-variables"></a>1. declare suas variáveis

Substitua os seguintes parâmetros usados para os exemplos com as configurações necessárias para sua própria configuração e, em seguida, declare essas variáveis.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Depois de declarar as variáveis, obtenha o nome da configuração de IP que você deseja remover.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. remover a configuração de IP do gateway e desabilitar o modo ativo-ativo

Use este exemplo para remover a configuração de IP do gateway e desabilitar o modo ativo-ativo. Observe que você deve definir o objeto de gateway no PowerShell para disparar a atualização real.

```powershell
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Esta atualização pode levar até 30 a 45 minutos.

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter os passos.
