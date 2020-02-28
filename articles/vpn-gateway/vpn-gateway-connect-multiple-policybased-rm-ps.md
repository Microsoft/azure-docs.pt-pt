---
title: 'Azure VPN Gateway: Ligar gateways a múltiplos dispositivos VPN baseados em políticas no local'
description: Configure uma porta VPN baseada em rota Azure para vários dispositivos VPN baseados em políticas usando o Azure Resource Manager e powerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: yushwang
ms.openlocfilehash: 028ed1a632016fcbdf29bb47ab81a36f659785da
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660505"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Conecte gateways VPN Azure a vários dispositivos VPN baseados em políticas no local usando powerShell

Este artigo ajuda-o a configurar uma porta VPN baseada em rotas Azure para se conectar a vários dispositivos VPN baseados em políticas no local, alavancando políticas personalizadas de IPsec/IKE nas ligações VPN S2S.

## <a name="about"></a>Sobre gateways VPN baseados em políticas e baseados em rotas

Os dispositivos VPN baseados em políticas *vs.* rota diferem na forma como os selecionadores de tráfego iPsec são definidos numa ligação:

* **Baseado em políticas** Os dispositivos VPN utilizam as combinações de prefixos de ambas as redes para definir como o tráfego é encriptado/desencriptado através de túneis IPsec. É tipicamente construído em dispositivos de firewall que executam a filtragem do pacote. A encriptação e a desencriptação do túnel IPsec são adicionadas ao motor de filtragem e processamento do pacote.
* **Baseado em rota** Os dispositivos VPN utilizam selecionadores de tráfego (wildcard) e permitem que as tabelas de encaminhamento/encaminhamento direcionem as tabelas para diferentes túneis IPsec. É tipicamente construído em plataformas de router onde cada túnel IPsec é modelado como uma interface de rede ou VTI (interface de túnel virtual).

Os seguintes diagramas destacam os dois modelos:

### <a name="policy-based-vpn-example"></a>Exemplo vpn baseado em políticas
![baseada em políticas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Exemplo VPN baseado em rota
![baseada em rotas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Apoio azure à VPN baseada em políticas
Atualmente, o Azure suporta ambos os modos de gateways VPN: gateways VPN baseados em rotas e gateways VPN baseados em políticas. São construídos em diferentes plataformas internas, que resultam em diferentes especificações:

|                          | **Gateway VPN baseado em políticas** | **Gateway VPN baseado em rotas**       |**Gateway VPN baseado em rotas**                          |
| ---                      | ---                         | ---                              |---                                                 |
| **Azure Gateway SKU**    | Básica                       | Básica                            | Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3  |
| **Versão IKE**          | IKEv1                       | IKEv2                            | IKEv1 e IKEv2                                    |
| **Max, max. Ligações S2S** | **1**                       | 10                               |Padrão: 10<br> Outros SKUs: 30                     |
|                          |                             |                                  |                                                    |

Com a política personalizada IPsec/IKE, pode agora configurar gateways VPN baseados em rotas Azure para utilizar selecionadores de tráfego baseados em prefixo com opção "**PolicyBasedTrafficSelectors",** para ligar a dispositivos VPN baseados em políticas no local. Esta capacidade permite-lhe ligar-se de uma rede virtual Azure e porta de entrada VPN a múltiplos dispositivos VPN/firewall baseados em políticas no local, removendo o limite único de ligação dos atuais gateways VPN baseados na política do Azure.

> [!IMPORTANT]
> 1. Para permitir esta conectividade, os seus dispositivos VPN baseados em políticas no local devem apoiar o **IKEv2** para se ligarem aos gateways VPN baseados na rota Azure. Verifique as especificações do dispositivo VPN.
> 2. As redes no local que ligam através de dispositivos VPN baseados em políticas com este mecanismo só podem ligar-se à rede virtual Azure; **não podem transitar para outras redes ou redes virtuais através do mesmo portal Azure VPN**.
> 3. A opção de configuração faz parte da política de ligação IPsec/IKE personalizada. Se ativar a opção de seletor de tráfego baseada na política, deve especificar a política completa (algoritmos de encriptação e integridade IPsec/IKE, pontos de força chave e vida útil da SA).

O diagrama que se segue mostra porque é que o encaminhamento de trânsito através do gateway Azure VPN não funciona com a opção baseada na política:

![trânsito baseado em políticas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Como mostra o diagrama, o gateway Azure VPN tem selecionadores de tráfego da rede virtual para cada um dos prefixos da rede no local, mas não os prefixos de ligação cruzada. Por exemplo, o local no local 2, o local 3 e o local 4 podem comunicar cada um com vNet1 respectivamente, mas não podem ligar-se através da porta de entrada Azure VPN entre si. O diagrama mostra os seletores de tráfego de ligação cruzada que não estão disponíveis no gateway VPN Azure sob esta configuração.

## <a name="workflow"></a>Fluxo de trabalho

As instruções deste artigo seguem o mesmo exemplo descrito na [política Configure IPsec/IKE para ligações S2S ou VNet-to-VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) para estabelecer uma ligação VPN S2S. Isto é mostrado no seguinte diagrama:

![s2s-política](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

O fluxo de trabalho para permitir esta conectividade:
1. Crie a rede virtual, o gateway VPN e o portal de rede local para a sua ligação cross-premises.
2. Crie uma política IPsec/IKE.
3. Aplique a política quando criar uma ligação S2S ou VNet-to-VNet e **ative os seletores de tráfego baseados em políticas** na ligação.
4. Se a ligação já estiver criada, pode aplicar ou atualizar a política para uma ligação existente.

## <a name="before-you-begin"></a>Antes de começar

* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

* [!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enablepolicybased"></a>Ativar os selecionadores de tráfego baseados em políticas

Esta secção mostra-lhe como ativar os selecionadores de tráfego baseados em políticas numa ligação. Certifique-se de que concluiu a [Parte 3 do artigo de política Configure IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md). Os passos deste artigo usam os mesmos parâmetros.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Passo 1 - Criar a rede virtual, gateway VPN e gateway de rede local

#### <a name="connect-to-your-subscription-and-declare-your-variables"></a>Conecte-se à sua subscrição e declare as suas variáveis

1. Se estiver a executar o PowerShell localmente no seu computador, inscreva-se utilizando o cmdlet *Connect-AzAccount.* Ou, em vez disso, use a Azure Cloud Shell no seu navegador.

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

#### <a name="create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Criar a rede virtual, gateway VPN e gateway de rede local

1. Crie um grupo de recursos.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
2. Utilize o exemplo seguinte para criar a rede virtual TestVNet1 com três subredes e o gateway VPN. Se quiser substituir valores, é importante que nomeie sempre a sua subnet de gateway especificamente 'GatewaySubnet'. Se der outro nome, a criação da gateway falha.

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

### <a name="step-2---create-an-s2s-vpn-connection-with-an-ipsecike-policy"></a>Passo 2 - Criar uma ligação VPN S2S com uma política IPsec/IKE

1. Crie uma política IPsec/IKE.

   > [!IMPORTANT]
   > É necessário criar uma política IPsec/IKE para permitir a opção "UsePolicyBasedTrafficSselect" na ligação.

   O exemplo seguinte cria uma política IPsec/IKE com estes algoritmos e parâmetros:
    * IKEv2: AES256, SHA384, DHGroup24
    * IPsec: AES256, SHA256, PFS None, SA Lifetime 14400 segundos & 102400000KB

   ```azurepowershell-interactive
   $ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
   ```
1. Crie a ligação VPN S2S com os selecionadores de tráfego baseados em políticas e a política IPsec/IKE e aplique a política IPsec/IKE criada no passo anterior. Esteja ciente do parâmetro adicional "-UsePolicyBasedTrafficSelectors $True", que permite os selecionadores de tráfego baseados em políticas na ligação.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
   $lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

   New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
   ```
1. Após a conclusão dos passos, a ligação VPN S2S utilizará a política IPsec/IKE definida e permitirá os selecionadores de tráfego baseados em políticas na ligação. Pode repetir os mesmos passos para adicionar mais ligações a dispositivos VPN baseados em políticas adicionais no local a partir do mesmo gateway VPN Azure.

## <a name="update"></a>Para atualizar os selecionadores de tráfego baseados em políticas
Esta secção mostra-lhe como atualizar a opção de selecionadores de tráfego baseado em políticas para uma ligação VPN S2S existente.

1. Pegue o recurso de ligação.

   ```azurepowershell-interactive
   $RG1          = "TestPolicyRG1"
   $Connection16 = "VNet1toSite6"
   $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
   ```
1. Consulte a opção de selecionadores de tráfego baseados em políticas.
A seguinte linha mostra se os selecionadores de tráfego baseados em políticas são utilizados para a ligação:

   ```azurepowershell-interactive
   $connection6.UsePolicyBasedTrafficSelectors
   ```

   Se a linha voltar "**True**", então os selecionadores de tráfego baseados em políticas são configurados na ligação; caso contrário, devolve "**Falso**.".
1. Assim que obtiver o recurso de ligação, pode ativar ou desativar os seletores de tráfego baseados em políticas numa ligação.

   - Para ativar

      O exemplo que se segue permite a opção de selecionadores de tráfego baseados em políticas, mas deixa a política IPsec/IKE inalterada:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
      ```

   - Para desativar

      O exemplo que se segue desativa a opção de selecionadores de tráfego baseados em políticas, mas deixa a política IPsec/IKE inalterada:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
      ```

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter os passos.

Consulte também a [política Configure IPsec/IKE para ligações S2S VPN ou VNet-to-VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter mais detalhes sobre as políticas personalizadas de IPsec/IKE.
