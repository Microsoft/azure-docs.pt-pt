---
title: 'Azure VPN Gateway: Ligar gateways a vários dispositivos VPN baseados em políticas no local'
description: Configure uma porta VPN baseada em rotas Azure para vários dispositivos VPN baseados em políticas usando Azure Resource Manager e PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 2a85204fef026940394a19934bef1c631a8e2d21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89418887"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Ligue as portas VPN da Azure a vários dispositivos VPN baseados em políticas no local utilizando o PowerShell

Este artigo ajuda-o a configurar uma porta VPN baseada em rotas Azure para ligar a vários dispositivos VPN baseados em políticas no local, aproveitando as políticas personalizadas IPsec/IKE nas ligações VPN S2S.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a><a name="about"></a>Sobre as portas VPN baseadas em políticas e rotas

Os dispositivos VPN baseados em políticas *vs.* rota diferem na forma como os seletores de tráfego IPsec são definidos numa ligação:

* **Baseada em políticas** Os dispositivos VPN utilizam as combinações de prefixos de ambas as redes para definir como o tráfego é encriptado/desencriptado através de túneis IPsec. É normalmente construído em dispositivos de firewall que executam a filtragem de pacotes. A encriptação e desencriptação de túnel IPsec são adicionadas à filtragem de pacotes e ao motor de processamento.
* **Baseada em rotas** Os dispositivos VPN utilizam todos os seletores de tráfego (wildcard) e permitem que as tabelas de encaminhamento/encaminhamento direcionem o tráfego para diferentes túneis IPsec. É normalmente construído em plataformas de router onde cada túnel IPsec é modelado como interface de rede ou VTI (interface de túnel virtual).

Os seguintes diagramas destacam os dois modelos:

### <a name="policy-based-vpn-example"></a>Exemplo VPN baseado em políticas
![baseada em políticas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Exemplo VPN baseado em rotas
![baseado em rotas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Apoio à VPN baseada em políticas
Atualmente, a Azure suporta ambos os modos de gateways VPN: gateways VPN baseados em rotas e gateways VPN baseados em políticas. São construídas em diferentes plataformas internas, que resultam em diferentes especificações:

| Categoria | Gateway VPN de VPN | RouteBased VPN Gateway | RouteBased VPN Gateway |
| -------- | ----------------------- | ---------------------- | ---------------------- |---                                                 |
| **Azure Gateway SKU**    | Básico                       | Básico                            | VpnGw1, VpnGw2, VpnGw3, VpnGw4, VpnGw5  |
| **Versão IKE**          | IKEv1                       | IKEv2                            | IKEv1 e IKEv2                         |
| **Ligações Max. S2S** | **1**                       | 10                               | 30                     |
|                          |                             |                                  |                                                    |

Com a política personalizada IPsec/IKE, pode agora configurar gateways VPN baseados em rotas Azure para usar seletores de tráfego baseados em prefixos com opção "**PolicyBasedTrafficSelectors**", para ligar a dispositivos VPN baseados em políticas no local. Esta capacidade permite-lhe ligar a partir de uma rede virtual Azure e gateway VPN para vários dispositivos VPN/firewall baseados em políticas no local, removendo o limite de ligação único dos atuais gateways VPN baseados em políticas Azure.

> [!IMPORTANT]
> 1. Para ativar esta conectividade, os seus dispositivos VPN baseados em políticas no local devem suportar **o IKEv2** para se conectar aos gateways VPN baseados na rota Azure. Verifique as especificações do dispositivo VPN.
> 2. As redes no local que se conectam através de dispositivos VPN baseados em políticas com este mecanismo só podem ligar-se à rede virtual Azure; **não podem transitar para outras redes no local ou redes virtuais através do mesmo gateway Azure VPN**.
> 3. A opção de configuração faz parte da política de ligação IPsec/IKE personalizada. Se ativar a opção seletor de tráfego baseada em políticas, deve especificar a política completa (algoritmos de encriptação e integridade IPsec/IKE, pontos fortes e vida útil da SA).

O seguinte diagrama mostra por que razão o encaminhamento de trânsito através do gateway Azure VPN não funciona com a opção baseada em políticas:

![trânsito baseado em políticas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Como mostrado no diagrama, o gateway Azure VPN tem seletores de tráfego da rede virtual para cada um dos prefixos de rede no local, mas não os prefixos de ligação cruzada. Por exemplo, no local 2, local 3 e local 4 podem comunicar com o VNet1 respectivamente, mas não podem ligar-se através da porta de entrada VPN Azure entre si. O diagrama mostra os seletores de tráfego de ligação cruzada que não estão disponíveis no gateway Azure VPN sob esta configuração.

## <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

As instruções deste artigo seguem o mesmo exemplo descrito na [política Configure IPsec/IKE para ligações S2S ou VNet-to-VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) para estabelecer uma ligação S2S VPN. Isto é mostrado no seguinte diagrama:

![s2s política](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

O fluxo de trabalho para permitir esta conectividade:
1. Crie a rede virtual, o gateway VPN e a porta de entrada de rede local para a sua ligação de instalações cruzadas.
2. Criar uma política IPsec/IKE.
3. Aplique a política quando criar uma ligação S2S ou VNet-to-VNet e **ative os seletores de tráfego baseados** em políticas na ligação.
4. Se a ligação já estiver criada, pode aplicar ou atualizar a política para uma ligação existente.

## <a name="before-you-begin"></a>Antes de começar

* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

* [!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enable-policy-based-traffic-selectors"></a><a name="enablepolicybased"></a>Permitir os seletores de tráfego baseados em políticas

Esta secção mostra-lhe como ativar os seletores de tráfego baseados em políticas numa ligação. Certifique-se de que completou [a Parte 3 do artigo de política Configure IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md). Os passos deste artigo usam os mesmos parâmetros.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Passo 1 - Criar a rede virtual, gateway VPN e gateway de rede local

#### <a name="connect-to-your-subscription-and-declare-your-variables"></a>Conecte-se à sua subscrição e declare as suas variáveis

1. Se estiver a executar o PowerShell localmente no seu computador, inscreva-se utilizando o *cmdlet Connect-AzAccount.* Ou, em vez disso, use a Azure Cloud Shell no seu navegador.

2. Declarar as variáveis. Para este exercício, utilizamos as seguintes variáveis:

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

#### <a name="create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Crie a rede virtual, gateway VPN e gateway de rede local

1. Crie um grupo de recursos.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
2. Use o exemplo seguinte para criar a rede virtual TestVNet1 com três sub-redes e o gateway VPN. Se quiser substituir valores, é importante que dê sempre o nome da sua sub-rede gateway especificamente 'GatewaySubnet'. Se der outro nome, a criação da gateway falha.

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

### <a name="step-2---create-an-s2s-vpn-connection-with-an-ipsecike-policy"></a>Passo 2 - Criar uma ligação S2S VPN com uma política IPsec/IKE

1. Criar uma política IPsec/IKE.

   > [!IMPORTANT]
   > É necessário criar uma política IPsec/IKE para permitir a opção "UsePolicyBasedTrafficSelectors" na ligação.

   O exemplo a seguir cria uma política IPsec/IKE com estes algoritmos e parâmetros:
    * IKEv2: AES256, SHA384, DHGroup24
    * IPsec: AES256, SHA256, PFS None, SA Lifetime 14400 segundos & 10240000KB

   ```azurepowershell-interactive
   $ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
   ```
1. Crie a ligação S2S VPN com os seletores de tráfego baseados em políticas e com a política IPsec/IKE e aplique a política IPsec/IKE criada no passo anterior. Esteja atento ao parâmetro adicional "-UsePolicyBasedTrafficSelectors $True", que permite aos seletores de tráfego baseados em políticas na ligação.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
   $lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

   New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
   ```
1. Após completar os passos, a ligação S2S VPN utilizará a política IPsec/IKE definida e permitirá os seletores de tráfego baseados em políticas na ligação. Pode repetir os mesmos passos para adicionar mais ligações a dispositivos VPN baseados em políticas adicionais a partir do mesmo gateway Azure VPN.

## <a name="to-update-policy-based-traffic-selectors"></a><a name="update"></a>Para atualizar os seletores de tráfego baseados em políticas
Esta secção mostra-lhe como atualizar a opção de seletores de tráfego baseados em políticas para uma ligação S2S VPN existente.

1. Obtenha o recurso de ligação.

   ```azurepowershell-interactive
   $RG1          = "TestPolicyRG1"
   $Connection16 = "VNet1toSite6"
   $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
   ```
1. Consulte a opção de seletores de tráfego baseados em políticas.
A seguinte linha mostra se os seletores de tráfego baseados em políticas são utilizados para a ligação:

   ```azurepowershell-interactive
   $connection6.UsePolicyBasedTrafficSelectors
   ```

   Se a linha devolver "**True**", então os seletores de tráfego baseados em políticas são configurados na ligação; caso contrário, devolve "**Falso**.".
1. Assim que obtiver o recurso de ligação, pode ativar ou desativar os seletores de tráfego baseados em políticas numa ligação.

   - Para Ativar

      O exemplo a seguir permite a opção de seletores de tráfego baseados em políticas, mas deixa a política IPsec/IKE inalterada:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
      ```

   - Para desativar

      O exemplo a seguir desativa a opção de seletores de tráfego baseados em políticas, mas deixa a política IPsec/IKE inalterada:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
      ```

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/windows/quick-create-portal.md) para obter os passos.

Reveja também [a política de Configuração IPsec/IKE para ligações S2S VPN ou VNet-to-VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter mais detalhes sobre as políticas IPsec/IKE personalizadas.
