---
title: 'Ligar os gateways de VPN do Azure para dispositivos VPN baseado em políticas vários locais: Azure Resource Manager: PowerShell | Microsoft Docs'
description: Configure um Azure baseado na rota gateway de VPN para múltiplos baseado em políticas dispositivos VPN com o Azure Resource Manager e o PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: yushwang
ms.openlocfilehash: 9085d5ee21b1e955b7d9416a379ee730ba26ad3e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66150133"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Ligar os gateways de VPN do Azure para vários dispositivos no local e baseada em diretivas VPN com o PowerShell

Este artigo ajuda-o a configurar um Azure baseado na rota gateway de VPN para ligar a vários dispositivos no local e baseada em diretivas VPN tirar partido das políticas de IPsec/IKE personalizadas em ligações de S2S VPN.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about"></a>Sobre os gateways VPN baseado em políticas e baseado na rota

Política - *versus* dispositivos VPN baseados em rota diferem em como os seletores de tráfego IPsec são definidos numa conexão:

* **Baseado em políticas** dispositivos VPN utilizam as combinações de prefixos de ambas as redes para definir como o tráfego é encriptado/descriptografados através de túneis IPsec. Baseia-se normalmente em dispositivos de firewall que realizar a filtragem de pacotes. Encriptação de túnel IPsec e desencriptação são adicionadas para o pacote de filtragem e de motor de processamento.
* **Baseado na rota** dispositivos VPN utilizam seletores de tráfego de qualquer a qualquer (universal) e permitir que o reencaminhamento/encaminhamento de tráfego de direto tabelas para diferentes de túneis IPsec. Baseia-se normalmente em plataformas de roteador em que cada túnel IPsec é modelada como uma interface de rede ou VTI (interface de túnel virtual).

Os diagramas seguintes realçam os dois modelos:

### <a name="policy-based-vpn-example"></a>Exemplo VPN baseado em políticas
![baseado em políticas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Exemplo VPN baseado na rota
![baseado na rota](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Suporte do Azure para VPN baseado em políticas
Atualmente, o Azure oferece suporte a ambos os modos de gateways de VPN: baseado na rota gateways de VPN e gateways de VPN baseado em políticas. Eles são feitos em diferentes plataformas internas, que resultam em especificações diferentes:

|                          | **Gateway de PolicyBased VPN** | **Gateway de RouteBased VPN**               |
| ---                      | ---                         | ---                                      |
| **SKU de Gateway do Azure**    | Básico                       | Basic, Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3 |
| **Versão do IKE**          | IKEv1                       | IKEv2                                    |
| **Máx. Ligações S2S** | **1**                       | Básico/Standard: 10<br> HighPerformance: 30 |
|                          |                             |                                          |

Com a política de IPsec/IKE personalizada, pode configurar gateways de VPN baseado na rota do Azure para utilizar os seletores de tráfego com base no prefixo com a opção "**PolicyBasedTrafficSelectors**", para ligar a dispositivos VPN baseado em políticas no local. Esta capacidade permite-lhe ligar a partir de uma rede virtual do Azure e o gateway de VPN para vários dispositivos no local e baseada em diretivas VPN/firewall, remover o limite de ligação único dos atuais do Azure baseada em políticas gateways de VPN.

> [!IMPORTANT]
> 1. Para ativar essa conectividade, os dispositivos VPN baseado em políticas no local tem de suportar **IKEv2** para ligar para os gateways de VPN baseado na rota do Azure. Verifique suas especificações de dispositivo VPN.
> 2. As redes no local, ligar através de dispositivos VPN baseado na política com esse mecanismo podem ligar-se apenas à rede virtual do Azure; **eles não podem transitar para outras redes no local ou redes virtuais através do mesmo gateway de VPN do Azure**.
> 3. A opção de configuração faz parte da política de ligação de IPsec/IKE personalizada. Se ativar a opção de Seletor de tráfego baseado em políticas, tem de especificar a política concluída (algoritmos de criptografia e integridade do IPsec/IKE, principais pontos fortes e durações de SA).

O diagrama seguinte mostra o motivo pelo qual o encaminhamento de trânsito através do gateway de VPN do Azure não funciona com a opção baseado na política:

![trânsito baseada em políticas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Conforme mostrado no diagrama, o gateway de VPN do Azure tem os seletores de tráfego da rede virtual para cada um dos prefixos de rede no local, mas não os prefixos de ligação cruzada. Por exemplo, site local 2, site 3 e 4 de site podem cada comunicar para a VNet1, respetivamente, mas não é possível ligar através do gateway de VPN do Azure entre si. O diagrama mostra os seletores de tráfego de cross-connect que não estão disponíveis no gateway de VPN do Azure sob esta configuração.

## <a name="configurepolicybased"></a>Configurar os seletores de tráfego baseado em políticas numa conexão

As instruções neste artigo seguem o exemplo, conforme descrito em [configurar IPsec/política IKE para ligações S2S ou VNet a VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) para estabelecer uma ligação S2S VPN. Isso é mostrado no diagrama seguinte:

![política de s2s](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

O fluxo de trabalho para ativar essa conectividade:
1. Criar a rede virtual, o gateway de VPN e o gateway de rede local para a sua ligação entre locais
2. Criar uma política de IPsec/IKE
3. Aplicar a política, quando cria uma ligação S2S ou VNet a VNet, e **ativar os seletores de tráfego baseado em políticas** na ligação
4. Se a ligação já está a ser criada, pode aplicar ou atualizar a política para uma ligação existente

## <a name="before-you-begin"></a>Antes de começar

Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enablepolicybased"></a>Ativar seletores de tráfego baseado em políticas numa conexão

Certifique-se de que concluiu [parte 3 do artigo de política de IPsec/IKE configurar](vpn-gateway-ipsecikepolicy-rm-powershell.md) para esta secção. O exemplo seguinte utiliza os mesmos parâmetros e passos:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Passo 1 – criar a rede virtual, o gateway de VPN e o gateway de rede local

#### <a name="1-connect-to-your-subscription-and-declare-your-variables"></a>1. Ligar à sua subscrição e declarar as variáveis

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

Declarar as variáveis. Neste exercício, vamos utilizar as seguintes variáveis:

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

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. Criar a rede virtual, o gateway de VPN e o gateway de rede local

Crie um grupo de recursos.

```azurepowershell-interactive
New-AzResourceGroup -Name $RG1 -Location $Location1
```

Utilize o exemplo a seguir para criar a rede virtual TestVNet1 com três sub-redes e o gateway de VPN. Se quiser substituir valores, é importante que sempre atribua um nome à sub-rede do gateway especificamente "GatewaySubnet". Se der outro nome, a criação da gateway falha.

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

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Passo 2 - criar uma ligação S2S VPN com uma política de IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Criar uma política de IPsec/IKE

> [!IMPORTANT]
> Tem de criar uma política de IPsec/IKE para ativar a opção "UsePolicyBasedTrafficSelectors" na ligação.

O exemplo seguinte cria uma política de IPsec/IKE com esses algoritmos e parâmetros:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS None, 14400 de duração de SA segundos e 102400000KB

```azurepowershell-interactive
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Criar a ligação de S2S VPN com seletores de tráfego baseado em políticas e a política de IPsec/IKE
Criar uma ligação S2S VPN e aplicar a política de IPsec/IKE que criou no passo anterior. Tenha em atenção o parâmetro adicional "-UsePolicyBasedTrafficSelectors $True" que permite que os seletores de tráfego baseado em política na ligação.

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Depois de concluir os passos, a ligação de S2S VPN irá utilizar a política de IPsec/IKE definida e ativar seletores de tráfego baseada na ligação. Pode repetir as mesmas etapas para adicionar mais ligações a dispositivos VPN baseado em políticas no local adicionais a partir do mesmo gateway de VPN do Azure.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Atualizar os seletores de tráfego baseado em políticas para uma ligação
A última seção mostra-lhe como atualizar a opção de seletores de tráfego baseado em políticas para uma ligação S2S VPN existente.

### <a name="1-get-the-connection"></a>1. Obter a ligação
Obtenha o recurso de ligação.

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Marque a opção de seletores de tráfego baseado em políticas
A seguinte linha mostra se os seletores de tráfego baseado em políticas são utilizados para a ligação:

```azurepowershell-interactive
$connection6.UsePolicyBasedTrafficSelectors
```

Se devolver a linha "**True**", em seguida, seletores de tráfego baseado em políticas são configurados na ligação; caso contrário, devolve "**falso**."

### <a name="3-enabledisable-the-policy-based-traffic-selectors-on-a-connection"></a>3. Ativar/desativar os seletores de tráfego baseado em políticas numa conexão
Depois de obter o recurso de ligação, pode ativar ou desativar a opção.

#### <a name="to-enable-usepolicybasedtrafficselectors"></a>Para ativar UsePolicyBasedTrafficSelectors
O exemplo seguinte ativa a opção de seletores de tráfego baseado em políticas, mas deixa a política de IPsec/IKE inalterada:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

#### <a name="to-disable-usepolicybasedtrafficselectors"></a>Para desativar UsePolicyBasedTrafficSelectors
O exemplo seguinte desativa a opção de seletores de tráfego baseado em políticas, mas deixa a política de IPsec/IKE inalterada:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

## <a name="next-steps"></a>Passos Seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter os passos.

Reveja também [configurar IPsec/política IKE para ligações S2S VPN ou VNet a VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter mais detalhes sobre as políticas de IPsec/IKE personalizadas.
