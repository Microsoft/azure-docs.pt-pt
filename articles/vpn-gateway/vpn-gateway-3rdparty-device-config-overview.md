---
title: Configurações de dispositivos VPN parceiros para ligação aos gateways Azure VPN
description: Este artigo fornece uma visão geral das configurações do dispositivo VPN parceiro para a ligação aos gateways Azure VPN.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 484dad65310efaa60e8744b2f122b5e44ae13565
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880157"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Visão geral das configurações do dispositivo VPN parceiro
Este artigo fornece uma visão geral da configuração de dispositivos VPN no local para a ligação aos gateways Azure VPN. Uma amostra Azure virtual network e configuração de gateway VPN é usada para mostrar-lhe como conectar-se a diferentes configurações de dispositivos VPN no local usando os mesmos parâmetros.



## <a name="device-requirements"></a>Requisitos dos dispositivos
Os gateways Azure VPN utilizam suítes de protocolo IPsec/IKE padrão para túneis VPN locais(S2S). Para obter uma lista de parâmetros IPsec/IKE e algoritmos criptográficos para gateways Azure VPN, consulte [sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md). Também pode especificar os algoritmos exatos e os pontos fortes para uma ligação específica, conforme descrito nos [requisitos criptográficos](vpn-gateway-about-compliance-crypto.md).

## <a name="single-vpn-tunnel"></a><a name ="singletunnel"></a>Túnel VPN único
A primeira configuração da amostra consiste num único túnel S2S VPN entre um gateway Azure VPN e um dispositivo VPN no local. Pode configurar opcionalmente o Protocolo de [Gateway fronteiriço (BGP) através do túnel VPN.](#bgp)

![Diagrama de um único túnel S2S VPN](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Para obter instruções passo a passo para configurar um único túnel VPN, consulte [configurar uma ligação local-local](./tutorial-site-to-site-portal.md). As seguintes secções especificam os parâmetros de ligação para a configuração da amostra e fornecem um script PowerShell para ajudá-lo a começar.

### <a name="connection-parameters"></a>Parâmetros da ligação
Esta secção lista os parâmetros para os exemplos descritos nas secções anteriores.

| **Parâmetro**                | **Valor**                    |
| ---                          | ---                          |
| Prefixos de endereço de rede virtual        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN gateway IP         | Azure VPN Gateway IP         |
| Prefixos de endereço no local | 10.51.0.0/16<br>10.52.0.0/16 |
| IP dispositivo VPN no local    | IP dispositivo VPN no local    |
| * Rede virtual BGP ASN                | 65010                        |
| * Azure BGP peer IP           | 10.12.255.30                 |
| * No local BGP ASN         | 65050                        |
| * No local BGP peer IP     | 10.52.255.254                |

\* Parâmetro opcional apenas para BGP.

### <a name="sample-powershell-script"></a>Guião PowerShell de amostra
Esta secção fornece um guião de amostra para começar. Para obter instruções detalhadas, consulte [Criar uma ligação S2S VPN utilizando o PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subscription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name="optional-use-custom-ipsecike-policy-with-usepolicybasedtrafficselectors"></a><a name ="policybased"></a>(Opcional) Use a política personalizada IPsec/IKE com usePolicyBasedTrafficSelectors
Se os seus dispositivos VPN não suportam nenhum seletor de tráfego, como configurações baseadas em rotas ou baseadas em VTI, crie uma política IPsec/IKE personalizada com a opção [UsePolicyBasedTrafficSelectors.](vpn-gateway-connect-multiple-policybased-rm-ps.md)

> [!IMPORTANT]
> Tem de criar uma política IPsec/IKE para ativar a opção **UsePolicyBasedTrafficSelectors** na ligação.


O script da amostra cria uma política IPsec/IKE com os seguintes algoritmos e parâmetros:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, SA Lifetime 7.200 segundos e 20.480.000 KB (20 GB)

O script aplica a política IPsec/IKE e permite a opção **UsePolicyBasedTrafficSelectors** na ligação.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name="optional-use-bgp-on-s2s-vpn-connection"></a><a name ="bgp"></a>(Opcional) Utilizar BGP na ligação S2S VPN
Ao criar a ligação S2S VPN, pode utilizar opcionalmente [o BGP para o gateway VPN](vpn-gateway-bgp-resource-manager-ps.md). Esta abordagem tem duas diferenças:

* Os prefixos de endereço no local podem ser um único endereço de anfitrião. O endereço IP peer peer do BGP no local é especificado da seguinte forma:

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* Quando criar a ligação, deve definir a opção **-EnableBGP** para $True:

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Passos seguintes
Para obter instruções passo a passo para a configuração de gateways VPN ativas ativas, consulte [a configuração de gateways VPN ativas ativas para as instalações cruzadas e ligações VNet-to-VNet](vpn-gateway-activeactive-rm-powershell.md).