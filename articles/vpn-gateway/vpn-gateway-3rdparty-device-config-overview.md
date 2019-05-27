---
title: Configurações de dispositivos VPN para ligar aos gateways de VPN do Azure de parceiros | Documentos da Microsoft
description: Este artigo fornece uma descrição geral das configurações de dispositivos VPN de parceiros para ligar aos gateways de VPN do Azure.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: ''
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: 7d3a32b5f2b2742a36716bac9747f20c47c98858
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66150195"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Descrição geral das configurações de dispositivos VPN do parceiro
Este artigo fornece uma descrição geral da configuração de dispositivos VPN no local para ligar aos gateways de VPN do Azure. A rede virtual do Azure de exemplo e configuração do gateway VPN é utilizada para mostrar como ligar a configurações de dispositivos VPN diferentes no local com os mesmos parâmetros.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="device-requirements"></a>Requisitos de dispositivos
Os gateways VPN do Azure utilizam conjuntos de protocolo de IPsec/IKE standard para VPN site a site (S2S) túneis. Para obter uma lista dos parâmetros de IPsec/IKE e algoritmos criptográficos para gateways de VPN do Azure, consulte [acerca dos dispositivos VPN](vpn-gateway-about-vpn-devices.md). Também pode especificar os algoritmos exatos e os principais pontos fortes para uma ligação específica, tal como descrito em [sobre os requisitos de criptografia](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>Único túnel VPN
A configuração do primeiro no exemplo consiste num único túnel de S2S VPN entre um gateway de VPN do Azure e um dispositivo VPN no local. Opcionalmente, pode configurar o [protocolo BGP (Border Gateway) através do túnel VPN](#bgp).

![Diagrama de um único túnel de S2S VPN](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Para obter instruções passo a passo configurar um único túnel VPN, consulte [configurar uma ligação site a site](vpn-gateway-howto-site-to-site-resource-manager-portal.md). As secções seguintes especificam os parâmetros de ligação para a configuração de exemplo e fornecem um script do PowerShell para o ajudar a começar a utilizar.

### <a name="connection-parameters"></a>Parâmetros de ligação
Esta secção lista os parâmetros para os exemplos que são descritos nas secções anteriores.

| **Parâmetro**                | **Valor**                    |
| ---                          | ---                          |
| Prefixos de endereços de rede virtual        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP do gateway VPN do Azure         | Gateway de VPN do Azure IP         |
| Prefixos de endereço no local | 10.51.0.0/16<br>10.52.0.0/16 |
| IP do dispositivo VPN no local    | IP do dispositivo VPN no local    |
| * Rede virtual BGP ASN                | 65010                        |
| * O azure IP do elemento BGP           | 10.12.255.30                 |
| * Locais BGP ASN         | 65050                        |
| * IP do elemento BGP no local     | 10.52.255.254                |

\* Parâmetro opcional para BGP apenas.

### <a name="sample-powershell-script"></a>Script do PowerShell de exemplo
Esta seção fornece um script de exemplo para começar. Para obter instruções detalhadas, consulte [criar uma ligação S2S VPN com o PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

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

### <a name ="policybased"></a>(Opcional) Utilizar a política de IPsec/IKE personalizada com UsePolicyBasedTrafficSelectors
Se os dispositivos VPN não suportarem a seletores de tráfego de qualquer para qualquer, tais como configurações com base em VTI ou baseado na rota, criar uma política de IPsec/IKE personalizada com o [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) opção.

> [!IMPORTANT]
> Tem de criar uma política de IPsec/IKE para ativar a **UsePolicyBasedTrafficSelectors** opção na ligação.


O script de exemplo cria uma política de IPsec/IKE com os parâmetros e algoritmos seguintes:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, segundos de 7.200 de duração de SA e 20,480,000 KB (20 GB)

O script aplica-se a política de IPsec/IKE e permite que o **UsePolicyBasedTrafficSelectors** opção na ligação.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>(Opcional) Utilizar o BGP na ligação S2S VPN
Ao criar a ligação S2S VPN, opcionalmente, pode utilizar [BGP para o gateway VPN](vpn-gateway-bgp-resource-manager-ps.md). Essa abordagem tem duas diferenças:

* Os prefixos de endereço no local podem ser um endereço de anfitrião único. O endereço IP do elemento de rede BGP no local é especificado da seguinte forma:

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* Quando criar a ligação, tem de definir o **- EnableBGP** opção para $True:

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Passos Seguintes
Para obter instruções passo a passo configurar gateways VPN ativos-ativos, consulte [configurar gateways VPN ativos-ativos para em vários locais e ligações de VNet a VNet](vpn-gateway-activeactive-rm-powershell.md).

