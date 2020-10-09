---
title: 'Gateway Azure VPN: Criar e gerir ligações VPN S2S: Tutorial'
description: Tutorial - Criar e Gerir ligações VPN S2S com o módulo Azure PowerShell
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 09/02/2020
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 9b712c1b2e8ac33ec189542bf6f60929ac59858d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89420562"
---
# <a name="tutorial-create-and-manage-s2s-vpn-connections-using-powershell"></a>Tutorial: Criar e gerir ligações S2S VPN utilizando PowerShell

As ligações VPN S2S do Azure fornecem conectividade segura em vários locais entre as instalações do cliente e o Azure. Este tutorial explica os ciclos de vida das ligações VPN S2S IPsec, como criar e gerir uma ligação VPN S2S. Saiba como:

> [!div class="checklist"]
> * Criar uma ligação VPN S2S
> * Atualizar a propriedade de ligação: chave pré-partilhada, BGP, política IPsec/IKE
> * Adicionar mais ligações VPN
> * Eliminar uma ligação VPN

O diagrama seguinte mostra a topologia para este tutorial:

![Diagrama de ligação de rede de VPNs](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

### <a name="working-with-azure-cloud-shell-and-azure-powershell"></a>Trabalhando com Azure Cloud Shell e Azure PowerShell

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="requirements"></a>Requisitos

Complete o primeiro tutorial: [Criar gateway VPN com Azure PowerShell](vpn-gateway-tutorial-create-gateway-powershell.md) para criar os seguintes recursos:

1. Grupo de recursos (TestRG1), rede virtual (VNet1) e gatewaySubnet
2. Gateway de VPN (VNet1GW)

Os valores dos parâmetros de rede virtual são listados abaixo. Note os valores adicionais para o gateway de rede local que representa a sua rede no local. Altere os valores abaixo com base no seu ambiente e configuração de rede, em seguida, copie e cole para definir as variáveis para este tutorial. Se a sessão cloud Shell tiver tempo para fora, ou se precisar de utilizar uma janela PowerShell diferente, copie e cole as variáveis para a sua nova sessão e continue o tutorial.

>[!NOTE]
> Se estiver a utilizar isto para fazer uma ligação, não se esqueça de alterar os valores para corresponder à sua rede no local. Se está apenas a executar estes passos como tutorial, não precisa de fazer alterações, mas a ligação não vai funcionar.
>

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network - LNGIP1 is the VPN device public IP address
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "5.4.3.2"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

O fluxo de trabalho para criar uma ligação VPN S2S é simples:

1. Criar um gateway de rede local para representar a sua rede no local
2. Criar uma ligação entre o gateway de VPN do Azure e o gateway de rede local

## <a name="create-a-local-network-gateway"></a>Criar um gateway de rede local

Um gateway de rede local representa a sua rede no local. Pode especificar as propriedades da sua rede no local no gateway de rede local, incluindo:

* Endereço IP público do seu dispositivo VPN
* Espaço de endereços no local
* (Opcional) Atributos BGP (endereço IP do elemento de rede BGP e número de AS)

Crie uma porta de entrada de rede local com o comando [New-AzLocalNetworkGateway.](https://docs.microsoft.com/powershell/module/az.network/new-azlocalnetworkgateway)

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location $Location1 -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>Criar uma ligação VPN S2S

Em seguida, crie uma ligação VPN site-to-site entre o seu gateway de rede virtual e o seu dispositivo VPN com a [New-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection). Repare que o “-ConnectionType” relativo a Rede de VPNs é *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3" -ConnectionProtocol IKEv2
```

Adicione a propriedade "**-EnableBGP $True**" opcional para ativar o BGP para a ligação se estiver a utilizar o BGP. Está desativado por predefinição. O parâmetro 'ConnectionProtocol' é opcional com o IKEv2 como padrão. Pode criar a ligação com os protocolos IKEv1 especificando **-ConnectionProtocol IKEv1**.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>Atualizar a chave pré-partilhada, o BGP e a política IPsec/IKE da ligação VPN

### <a name="view-and-update-your-pre-shared-key"></a>Ver e atualizar a chave pré-partilhada

A ligação VPN S2S do Azure utiliza uma chave pré-partilhada (segredo) para fazer a autenticação entre o dispositivo VPN no local e o gateway de VPN do Azure. Pode visualizar e atualizar a chave pré-partilhada para uma ligação com [o Get-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworkgatewayconnectionsharedkey) e [Set-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnectionsharedkey).

> [!IMPORTANT]
> A chave pré-partilhada é uma cadeia de **carateres ASCII imprimíveis** com um máximo de 128 carateres.

Este comando mostra a chave pré-partilhada para a ligação:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

A saída será "**Azure \@ !b2C3**" seguindo o exemplo acima. Utilize o comando abaixo para alterar o valor da chave pré-partilhada para "**Azure \@ !_b2=C3**":

```azurepowershell-interactive
Set-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>Ativar o BGP na ligação VPN

O gateway de VPN do Azure suporta o protocolo de encaminhamento dinâmico BGP. Pode ativar o BGP em cada ligação individual, dependendo se está a utilizar o BGP nos seus dispositivos e redes no local. Especifique as seguintes propriedades do BGP antes de ativar o BGP na ligação:

* ASN (Número de Sistema Autónomo) da VPN do Azure
* ASN do gateway de rede local no local
* Endereço IP do elemento de rede BGP do gateway de rede local no local

Se não tiver configurado as propriedades BGP, os seguintes comandos adicionam estas propriedades ao seu gateway VPN e gateway de rede local: [Set-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgateway) e [Set-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azlocalnetworkgateway).

Utilize o seguinte exemplo para configurar propriedades BGP:

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Ativar o BGP com [Set-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnection).

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

Pode desativar o BGP, alterando o valor da propriedade "-EnableBGP" para **$False**. Consulte o artigo [BGP nos gateways de VPN do Azure](vpn-gateway-bgp-resource-manager-ps.md) para obter uma explicação mais detalhada do BGP nos gateways de VPN do Azure.

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Aplicar uma política IPsec/IKE personalizada na ligação

Pode aplicar uma política IPsec/IKE opcional para especificar a combinação exata de algoritmos criptográficos IPsec/IKE e forças de chave na ligação, em vez de utilizar as [propostas predefinidas](vpn-gateway-about-vpn-devices.md#ipsec). O script de exemplo seguinte cria uma política IPsec/IKE diferente com os seguintes parâmetros e algoritmos:

* IKEv2: AES256, SHA256, DHGroup14
* IPsec: AES128, SHA1, PFS14, Duração de SA 14.400 segundos e 102.400.000 KB

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Consulte o artigo [Política IPsec/IKE para ligações S2S ou VNet a VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) para ver uma lista completa de algoritmos e instruções.

## <a name="add-another-s2s-vpn-connection"></a>Adicionar outra ligação VPN S2S

Adicione uma ligação S2S VPN adicional ao mesmo gateway VPN, crie outro gateway de rede local e crie uma nova ligação entre o novo gateway de rede local e o gateway VPN. Use os seguintes exemplos, certificando-se de modificar as variáveis para refletir a sua própria configuração de rede.

```azurepowershell-interactive
# On-premises network - LNGIP2 is the VPN device public IP address
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "4.3.2.1"
$Connection2 = "VNet1ToSite2"

New-AzLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

Agora, existem duas ligações VPN S2S para o seu gateway de VPN do Azure.

![Ligações VPN Multilocal](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>Eliminar uma ligação VPN S2S

Eliminar uma ligação S2S VPN com [Remove-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetworkgatewayconnection).

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Elimine o gateway de rede local se já não precisar dele. Não é possível eliminar um gateway de rede local se existirem outras ligações associadas ao mesmo.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se esta configuração fizer parte de um protótipo, teste ou implementação de prova de conceito, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, o gateway VPN e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar e gerir ligações VPN S2S, como, por exemplo, a:

> [!div class="checklist"]
> * Criar uma ligação VPN S2S
> * Atualizar a propriedade de ligação: chave pré-partilhada, BGP, política IPsec/IKE
> * Adicionar mais ligações VPN
> * Eliminar uma ligação VPN

Avance para os tutoriais seguintes para saber mais sobre ligações S2S, VNet a VNet e P2S.

> [!div class="nextstepaction"]
> * [Criar ligações VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Criar ligações P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
