---
title: Configurações de dispositivo VPN do parceiro para se conectar a gateways de VPN do Azure
description: Este artigo fornece uma visão geral das configurações de dispositivo VPN do parceiro para se conectar a gateways de VPN do Azure.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: 733add5aa86ebd7faaaab78bb301ba9469433fdd
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75778027"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Visão geral das configurações de dispositivo VPN do parceiro
Este artigo fornece uma visão geral da configuração de dispositivos VPN locais para conexão com gateways de VPN do Azure. Um exemplo de configuração de rede virtual do Azure e gateway de VPN é usado para mostrar como se conectar a diferentes configurações de dispositivo VPN local usando os mesmos parâmetros.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="device-requirements"></a>Requisitos de dispositivos
Os gateways de VPN do Azure usam pacotes de protocolo IPsec/IKE padrão para túneis VPN site a site (S2S). Para obter uma lista de parâmetros de IPsec/IKE e algoritmos criptográficos para gateways de VPN do Azure, consulte [sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md). Você também pode especificar os algoritmos exatos e as forças de chave para uma conexão específica, conforme descrito em [sobre os requisitos de criptografia](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>Túnel de VPN único
A primeira configuração no exemplo consiste em um único túnel VPN S2S entre um gateway de VPN do Azure e um dispositivo VPN local. Opcionalmente, você pode configurar o [Border Gateway Protocol (BGP) pelo túnel VPN](#bgp).

![Diagrama de um único túnel VPN S2S](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Para obter instruções detalhadas sobre como configurar um único túnel VPN, consulte [Configurar uma conexão site a site](vpn-gateway-howto-site-to-site-resource-manager-portal.md). As seções a seguir especificam os parâmetros de conexão para a configuração de exemplo e fornecem um script do PowerShell para ajudá-lo a começar.

### <a name="connection-parameters"></a>Parâmetros da ligação
Esta seção lista os parâmetros para os exemplos descritos nas seções anteriores.

| **Parâmetro**                | **Valor**                    |
| ---                          | ---                          |
| Prefixos de endereço de rede virtual        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP do gateway de VPN do Azure         | IP do gateway de VPN do Azure         |
| Prefixos de endereço local | 10.51.0.0/16<br>10.52.0.0/16 |
| IP do dispositivo VPN local    | IP do dispositivo VPN local    |
| * ASN de BGP de rede virtual                | 65010                        |
| * IP do par do Azure BGP           | 10.12.255.30                 |
| * ASN do BGP local         | 65050                        |
| * IP do par de BGP local     | 10.52.255.254                |

\* parâmetro opcional somente para BGP.

### <a name="sample-powershell-script"></a>Exemplo de script do PowerShell
Esta seção fornece um script de exemplo para você começar. Para obter instruções detalhadas, consulte [criar uma conexão VPN S2S usando o PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

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

### <a name ="policybased"></a>Adicional Usar política de IPsec/IKE personalizada com UsePolicyBasedTrafficSelectors
Se os dispositivos VPN não oferecerem suporte a seletores de tráfego de qualquer para qualquer, como configurações baseadas em rota ou VTI, crie uma política personalizada de IPsec/IKE com a opção [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) .

> [!IMPORTANT]
> Você deve criar uma política de IPsec/IKE para habilitar a opção **UsePolicyBasedTrafficSelectors** na conexão.


O script de exemplo cria uma política de IPsec/IKE com os seguintes algoritmos e parâmetros:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, SA tempo de vida de 7.200 segundos e 20.480.000 KB (20 GB)

O script aplica a política de IPsec/IKE e habilita a opção **UsePolicyBasedTrafficSelectors** na conexão.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>Adicional Usar BGP na conexão VPN S2S
Ao criar a conexão VPN S2S, você pode opcionalmente usar o [BGP para o gateway de VPN](vpn-gateway-bgp-resource-manager-ps.md). Essa abordagem tem duas diferenças:

* Os prefixos de endereço local podem ser um único endereço de host. O endereço IP do par de BGP local é especificado da seguinte maneira:

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* Ao criar a conexão, você deve definir a opção **-EnableBGP** como $true:

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Passos seguintes
Para obter as instruções passo a passo para configurar gateways de VPN ativos-ativos, consulte [Configurando gateways de VPN ativo-ativo para conexões entre locais e vnet para vnet](vpn-gateway-activeactive-rm-powershell.md).

