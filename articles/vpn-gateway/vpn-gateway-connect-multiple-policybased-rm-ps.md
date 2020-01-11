---
title: 'Gateway de VPN do Azure: conectar gateways a vários dispositivos VPN baseados em políticas locais'
description: Configure um gateway de VPN baseado em rota do Azure para vários dispositivos VPN com base em políticas usando o Azure Resource Manager e o PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: 97db6af7233a8cc0e1feac75734225815282131a
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896158"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Conectar gateways de VPN do Azure a vários dispositivos VPN com base em políticas locais usando o PowerShell

Este artigo ajuda você a configurar um gateway de VPN baseado em rota do Azure para se conectar a vários dispositivos VPN baseados em políticas locais utilizando políticas de IPsec/IKE personalizadas em conexões VPN S2S.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about"></a>Sobre gateways de VPN baseados em políticas e em rotas

Políticas- *vs.* os dispositivos VPN baseados em rota diferem em como os seletores de tráfego IPSec são definidos em uma conexão:

* **Baseado em políticas** Os dispositivos VPN usam as combinações de prefixos de ambas as redes para definir como o tráfego é criptografado/descriptografado por meio de túneis IPsec. Normalmente, ele é criado em dispositivos de firewall que executam a filtragem de pacotes. A encriptação e desencriptação de túnel IPsec são adicionadas à filtragem de pacotes e ao motor de processamento.
* **Baseado em rota** Os dispositivos VPN usam seletores de tráfego de qualquer para qualquer (curinga) e permitem que as tabelas de roteamento/encaminhamento direcionem o tráfego para túneis IPsec diferentes. Normalmente, ele é criado em plataformas de roteador onde cada túnel IPsec é modelado como uma interface de rede ou VTI (interface de túnel virtual).

Os seguintes diagramas destacam os dois modelos:

### <a name="policy-based-vpn-example"></a>Exemplo de VPN baseada em políticas
![baseado em políticas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Exemplo de VPN baseada em rota
![baseado em rota](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Suporte do Azure para VPN baseada em política
Atualmente, o Azure dá suporte a ambos os modos de gateways de VPN: gateways de VPN baseados em rotas e gateways de VPN baseados em políticas. Eles são criados em diferentes plataformas internas, o que resulta em especificações diferentes:

|                          | **Gateway de VPN PolicyBased** | **Gateway de VPN RouteBased**       |**Gateway de VPN RouteBased**                          |
| ---                      | ---                         | ---                              |---                                                 |
| **SKU do gateway do Azure**    | Basic                       | Basic                            | Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3  |
| **Versão do IKE**          | IKEv1                       | IKEv2                            | IKEv1 e IKEv2                                    |
| **Maximizar. Conexões S2S** | **1**                       | 10                               |Padrão: 10<br> Outros SKUs: 30                     |
|                          |                             |                                  |                                                    |

Com a política personalizada de IPsec/IKE, agora você pode configurar gateways de VPN baseados em rotas do Azure para usar seletores de tráfego baseados em prefixo com a opção "**PolicyBasedTrafficSelectors**" para se conectar a dispositivos VPN baseados em políticas locais. Esse recurso permite que você se conecte de uma rede virtual do Azure e de um gateway de VPN a vários dispositivos VPN/firewall baseados em políticas locais, removendo o limite de conexão única dos gateways de VPN baseados em políticas atuais do Azure.

> [!IMPORTANT]
> 1. Para habilitar essa conectividade, os dispositivos VPN baseados em políticas locais devem dar suporte a **IKEv2** para se conectar aos gateways de VPN baseados em rotas do Azure. Verifique as especificações do seu dispositivo VPN.
> 2. As redes locais que se conectam por meio de dispositivos VPN com base em políticas com esse mecanismo só podem se conectar à rede virtual do Azure; **eles não podem transitar para outras redes locais ou redes virtuais por meio do mesmo gateway de VPN do Azure**.
> 3. A opção de configuração faz parte da política de conexão IPsec/IKE personalizada. Se você habilitar a opção de seletor de tráfego baseado em políticas, deverá especificar a política completa (algoritmos de integridade e criptografia de IPsec/IKE, forças de chave e tempos de vida de SA).

O diagrama a seguir mostra por que o roteamento de trânsito por meio do gateway de VPN do Azure não funciona com a opção baseada em políticas:

![trânsito baseado em políticas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Conforme mostrado no diagrama, o gateway de VPN do Azure tem seletores de tráfego da rede virtual para cada um dos prefixos de rede local, mas não os prefixos de conexão cruzada. Por exemplo, site local 2, site 3 e site 4 podem se comunicar com o VNet1, respectivamente, mas não podem se conectar por meio do gateway de VPN do Azure entre si. O diagrama mostra os seletores de tráfego de conexão cruzada que não estão disponíveis no gateway de VPN do Azure nessa configuração.

## <a name="configurepolicybased"></a>Configurar seletores de tráfego com base em políticas em uma conexão

As instruções neste artigo seguem o mesmo exemplo, conforme descrito em [Configurar a política de IPSec/IKE para conexões S2S ou vnet para vnet](vpn-gateway-ipsecikepolicy-rm-powershell.md) para estabelecer uma conexão VPN S2S. Isso é mostrado no diagrama a seguir:

![política S2S](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

O fluxo de trabalho para habilitar essa conectividade:
1. Criar a rede virtual, o gateway de VPN e o gateway de rede local para sua conexão entre locais
2. Criar uma política de IPsec/IKE
3. Aplicar a política ao criar uma conexão S2S ou VNet a VNet e **habilitar os seletores de tráfego com base em políticas** na conexão
4. Se a conexão já tiver sido criada, você poderá aplicar ou atualizar a política para uma conexão existente

## <a name="before-you-begin"></a>Antes de começar

Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enablepolicybased"></a>Habilitar seletores de tráfego com base em políticas em uma conexão

Verifique se você concluiu [a parte 3 do artigo configurar a política de IPSec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) para esta seção. O exemplo a seguir usa os mesmos parâmetros e etapas:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Etapa 1-criar a rede virtual, o gateway de VPN e o gateway de rede local

#### <a name="1-connect-to-your-subscription-and-declare-your-variables"></a>1. Conecte-se à sua assinatura e declare suas variáveis

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

Declarar as variáveis. Para este exercício, usamos as seguintes variáveis:

```azurepowershell-interactive
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
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
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. criar a rede virtual, o gateway de VPN e o gateway de rede local

Crie um grupo de recursos.

```azurepowershell-interactive
New-AzResourceGroup -Name $RG1 -Location $Location1
```

Use o exemplo a seguir para criar a rede virtual TestVNet1 com três sub-redes e o gateway de VPN. Se você quiser substituir valores, é importante que você sempre Nomeie sua sub-rede de gateway especificamente como ' GatewaySubnet '. Se der outro nome, a criação da gateway falha.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Etapa 2-criar uma conexão VPN S2S com uma política de IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. criar uma política de IPsec/IKE

> [!IMPORTANT]
> Você precisa criar uma política de IPsec/IKE para habilitar a opção "UsePolicyBasedTrafficSelectors" na conexão.

O exemplo a seguir cria uma política de IPsec/IKE com estes algoritmos e parâmetros:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS nenhum, tempo de vida de SA de 14400 segundos & 102400000 KB

```azurepowershell-interactive
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. criar a conexão VPN S2S com seletores de tráfego com base em políticas e política de IPsec/IKE
Crie uma conexão VPN S2S e aplique a política de IPsec/IKE criada na etapa anterior. Lembre-se do parâmetro adicional "-UsePolicyBasedTrafficSelectors $True", que habilita os seletores de tráfego baseados em políticas na conexão.

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Depois de concluir as etapas, a conexão VPN S2S usará a política de IPsec/IKE definida e habilitará os seletores de tráfego com base em políticas na conexão. Você pode repetir as mesmas etapas para adicionar mais conexões a dispositivos VPN baseados em políticas locais adicionais do mesmo gateway de VPN do Azure.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Atualizar seletores de tráfego com base em políticas para uma conexão
A última seção mostra como atualizar a opção de seletores de tráfego com base em políticas para uma conexão VPN S2S existente.

### <a name="1-get-the-connection"></a>1. obter a conexão
Obter o recurso de conexão.

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Verifique a opção de seletores de tráfego com base em políticas
A linha a seguir mostra se os seletores de tráfego baseados em políticas são usados para a conexão:

```azurepowershell-interactive
$connection6.UsePolicyBasedTrafficSelectors
```

Se a linha retornar "**true**", os seletores de tráfego baseados em políticas serão configurados na conexão; caso contrário, ele retornará "**false**".

### <a name="3-enabledisable-the-policy-based-traffic-selectors-on-a-connection"></a>3. habilitar/desabilitar os seletores de tráfego com base em políticas em uma conexão
Depois de obter o recurso de conexão, você pode habilitar ou desabilitar a opção.

#### <a name="to-enable-usepolicybasedtrafficselectors"></a>Para habilitar UsePolicyBasedTrafficSelectors
O exemplo a seguir habilita a opção de seletores de tráfego com base em políticas, mas deixa a política de IPsec/IKE inalterada:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

#### <a name="to-disable-usepolicybasedtrafficselectors"></a>Para desabilitar o UsePolicyBasedTrafficSelectors
O exemplo a seguir desabilita a opção de seletores de tráfego com base em políticas, mas deixa a política de IPsec/IKE inalterada:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter os passos.

Examine também [Configurar política de IPSec/IKE para conexões VPN S2S ou vnet para vnet](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter mais detalhes sobre as políticas de IPSec/IKE personalizadas.
