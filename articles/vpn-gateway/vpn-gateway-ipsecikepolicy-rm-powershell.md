---
title: Política IPsec/IKE para ligações VNet-to-VNet s2 &S
titleSuffix: Azure VPN Gateway
description: Configure a política IPsec/IKE para ligações S2S ou VNet-to-VNet com Gateways VpN Azure utilizando o Gestor de Recursos Azure e powerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: eaca48fc354f1cf37635e9729b04eaaaa882ba1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161907"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>Configurar a política IPsec/IKE para ligações VPN S2S ou VNet a VNet

Este artigo percorre-o através dos passos para configurar a política IPsec/IKE para ligações Site-to-Site VPN ou VNet-to-VNet utilizando o modelo de implementação do Gestor de Recursos e powerShell.



## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a><a name="about"></a>Sobre os parâmetros políticos IPsec e IKE para gateways Azure VPN
O protocolo IPsec e o ike standard suportam uma ampla gama de algoritmos criptográficos em várias combinações. Consulte [os requisitos criptográficos e os portões azure VPN](vpn-gateway-about-compliance-crypto.md) para ver como isso pode ajudar a garantir que as instalações cruzadas e a conectividade VNet-to-VNet satisfazem os seus requisitos de conformidade ou segurança.

Este artigo fornece instruções para criar e configurar uma política IPsec/IKE e aplicar-se a uma ligação nova ou existente:

* [Parte 1 - Fluxo de trabalho para criar e definir a política IPsec/IKE](#workflow)
* [Parte 2 - Algoritmos criptográficos suportados e pontos fortes chave](#params)
* [Parte 3 - Criar uma nova ligação VpN S2S com a política IPsec/IKE](#crossprem)
* [Parte 4 - Criar uma nova ligação VNet-to-VNet com a política IPsec/IKE](#vnet2vnet)
* [Parte 5 - Gerir (criar, adicionar, remover) política iPsec/IKE para uma ligação](#managepolicy)

> [!IMPORTANT]
> 1. Note que a política IPsec/IKE funciona apenas no seguinte portal SKUs:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (baseado em rota)
>    * ***Standard*** e ***HighPerformance*** (baseado em rotas)
> 2. Só pode especificar ***uma*** combinação de políticas para uma determinada ligação.
> 3. Deve especificar todos os algoritmos e parâmetros tanto para IKE (Modo Principal) como para IPsec (Modo Rápido). Não é permitida a especificação da política parcial.
> 4. Consulte as especificações do fornecedor de dispositivos VPN para garantir que a apólice é suportada nos seus dispositivos VPN no local. As ligações S2S ou VNet-to-VNet não podem determinar se as políticas são incompatíveis.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a><a name ="workflow"></a>Parte 1 - Fluxo de trabalho para criar e definir a política IPsec/IKE
Esta secção descreve o fluxo de trabalho para criar e atualizar a política IPsec/IKE numa ligação S2S VPN ou VNet-to-VNet:
1. Criar uma rede virtual e um gateway de VPN
2. Criar uma porta de entrada de rede local para ligação de instalações cruzadas, ou outra rede virtual e porta de entrada para a ligação VNet-to-VNet
3. Criar uma política IPsec/IKE com algoritmos e parâmetros selecionados
4. Criar uma ligação (IPsec ou VNet2VNet) com a política IPsec/IKE
5. Adicionar/atualizar/remover uma política IPsec/IKE para uma ligação existente

As instruções deste artigo ajudam-no a configurar e configurar as políticas iPsec/IKE, como mostra o diagrama:

![ipsec-ike-política](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name="part-2---supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Parte 2 - Algoritmos criptográficos suportados & pontos fortes fundamentais

A tabela seguinte lista os algoritmos criptográficos suportados e os pontos fortes fundamentais configuráveis pelos clientes:

| **IPsec/IKEv2**  | **Opções**    |
| ---  | --- 
| Encriptação IKEv2 | AES256, AES192, AES128, DES3, DES  
| Integridade do IKEv2  | SHA384, SHA256, SHA1, MD5  |
| Grupo DH         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, Nenhum |
| Encriptação do IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nenhum    |
| Integridade do IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Grupo PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Nenhum 
| Duração de SA QM   | **(Opcional:** os valores predefinidos são utilizados se não especificados)<br>Segundos (número inteiro; **mín. 300 **/predefinição de 27000 segundos)<br>KBytes (número inteiro; **mín. 1024 **/predefinição de 102400000 KBytes)   |
| Seletor de tráfego | UsePolicyBasedTrafficSelectors** ($True/$False; **Opcional,**$False por defeito se não especificado)    |
|  |  |

> [!IMPORTANT]
> 1. **A configuração de dispositivo VPN local deve corresponder ou deve conter os seguintes algoritmos e parâmetros que especificar na política de IPsec/IKE do Azure:**
>    * Algoritmo de encriptação IKE (Modo Principal / Fase 1)
>    * Algoritmo de integridade IKE (Modo Principal / Fase 1)
>    * Grupo DH (Modo Principal / Fase 1)
>    * Algoritmo de encriptação IPsec (Modo Rápido / Fase 2)
>    * Algoritmo de integridade do IPsec (Modo Rápido / Fase 2)
>    * Grupo PFS (Modo Rápido / Fase 2)
>    * Seletor de tráfego (se for utilizado UsePolicyBasedTrafficSselects)
>    * A duração do SA é apenas a especificação local, não é necessário corresponder.
>
> 2. **Se o GCMAES for utilizado como para algoritmo de encriptação IPsec, deve selecionar o mesmo algoritmo GCMAES e o comprimento da chave para a Integridade do IPsec; por exemplo, a utilização de GCMAES128 para ambos**
> 3. Na tabela acima:
>    * IKEv2 corresponde ao Modo Principal ou fase 1
>    * IPsec corresponde a Modo Rápido ou Fase 2
>    * O Grupo DH especifica o Grupo Diffie-Hellmen utilizado no Modo Principal ou na Fase 1
>    * O Grupo PFS especificou o Grupo Diffie-Hellmen utilizado no Modo Rápido ou na Fase 2
> 4. A duração do SA do Modo Principal do IKEv2 é fixa em 28 800 segundos em gateways de VPN do Azure
> 5. A definição de "UsePolicyBasedTrafficSelectors" para $True numa ligação configurará o gateway VPN Azure para ligar à firewall VPN baseada em políticas nas instalações. Se ativar os selecionadores PolicyBasedTrafficS, tem de garantir que o seu dispositivo VPN tem os seletores de tráfego correspondentes definidos com todas as combinações da sua rede no local (gateway de rede local) prefixos de/para os prefixos da rede virtual Azure, em vez de qualquer um. Por exemplo, se os prefixos de rede local são 10.1.0.0/16 e 10.2.0.0/16, e os prefixos de rede virtual são 192.168.0.0/16 e 172.16.0.0/16, tem de especificar os seletores de tráfego seguintes:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Para obter mais informações sobre os selecionadores de tráfego baseados em políticas, consulte [Connect multiple on-premises policy-based VPN devices](vpn-gateway-connect-multiple-policybased-rm-ps.md).

A tabela seguinte lista os grupos diffie-hellman correspondentes apoiados pela política personalizada:

| **Grupo Diffie-Hellman**  | **DHGroup**              | **PFSGroup** | **Comprimento da chave** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | MODP de 768 bits   |
| 2                         | DHGroup2                 | PFS2         | MODP de 1024 bits  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | MODP de 2048 bits  |
| 19                        | ECP256                   | ECP256       | ECP de 256 bits    |
| 20                        | ECP384                   | ECP384       | ECP de 384 bits    |
| 24                        | DHGroup24                | PFS24        | MODP de 2048 bits  |

Consulte [RFC3526](https://tools.ietf.org/html/rfc3526) e [RFC5114](https://tools.ietf.org/html/rfc5114) para obter mais detalhes.

## <a name="part-3---create-a-new-s2s-vpn-connection-with-ipsecike-policy"></a><a name ="crossprem"></a>Parte 3 - Criar uma nova ligação VpN S2S com a política IPsec/IKE

Esta secção acompanha-o através dos passos de criação de uma ligação VPN S2S com uma política IPsec/IKE. Os seguintes passos criam a ligação como mostrado no diagrama:

![s2s-política](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Consulte [Criar uma ligação VPN S2S](vpn-gateway-create-site-to-site-rm-powershell.md) para obter instruções passo a passo mais detalhadas para criar uma ligação VPN S2S.

### <a name="before-you-begin"></a><a name="before"></a>Antes de começar

* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Instale os cmdlets PowerShell do Gestor de Recursos Azure. Consulte a [visão geral do Azure PowerShell](/powershell/azure/overview) para obter mais informações sobre a instalação dos cmdlets PowerShell.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>Passo 1 - Criar a rede virtual, gateway VPN e gateway de rede local

#### <a name="1-declare-your-variables"></a>1. Declare as suas variáveis

Para este exercício, começamos por declarar as nossas variáveis. Verifique se substitui os valores pelos seus quando configurar para produção.

```powershell
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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Ligue-se à sua subscrição e crie um novo grupo de recursos

Confirme que muda para o modo do PowerShell para utilizar os cmdlets do Resource Manager. Para obter mais informações, veja [Utilizar o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

Abra a consola do PowerShell e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar na ligação:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Criar a rede virtual, o gateway VPN e o gateway da rede local

A amostra seguinte cria a rede virtual, TestVNet1, com três subredes, e o gateway VPN. Quando estiver a substituir os valores, é importante que dê sempre à sub-rede do gateway o nome específico GatewaySubnet. Se der outro nome, a criação da gateway falha.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a><a name="s2sconnection"></a>Passo 2 - Criar uma ligação VPN S2S com uma política IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Criar uma política IPsec/IKE

O seguinte script de amostra cria uma política IPsec/IKE com os seguintes algoritmos e parâmetros:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS None, SA Lifetime 14400 segundos & 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Se utilizar GCMAES para IPsec, deve utilizar o mesmo algoritmo GCMAES e o comprimento da chave tanto para encriptação iPsec como para a integridade. Por exemplo, acima, os parâmetros correspondentes serão "-IpsecEncryption GCMAES256 -IpsecIntegrity GCMAES256" ao utilizar o GCMAES256.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Criar a ligação VPN S2S com a política IPsec/IKE

Crie uma ligação VPN S2S e aplique a política IPsec/IKE criada anteriormente.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Pode opcionalmente adicionar "-UsePolicyBasedTrafficSelectors $True" ao cmdlet de ligação para permitir que o gateway VPN Azure se ligue a dispositivos VPN baseados em políticas nas instalações, conforme descrito acima.

> [!IMPORTANT]
> Uma vez especificada uma política IPsec/IKE numa ligação, o gateway Azure VPN apenas enviará ou aceitará a proposta IPsec/IKE com algoritmos criptográficos especificados e pontos fortes fundamentais nessa ligação específica. Certifique-se de que o seu dispositivo VPN no local para a ligação utiliza ou aceita a combinação de política exata, caso contrário o túnel VPN S2S não se estabelecerá.


## <a name="part-4---create-a-new-vnet-to-vnet-connection-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>Parte 4 - Criar uma nova ligação VNet-to-VNet com a política IPsec/IKE

Os passos de criar uma ligação VNet-to-VNet com uma política IPsec/IKE são semelhantes aos de uma ligação VPN S2S. As seguintes scripts de amostra criam a ligação como mostrado no diagrama:

![v2v-política](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Consulte [Criar uma ligação VNet-to-VNet](vpn-gateway-vnet-vnet-rm-ps.md) para obter passos mais detalhados para criar uma ligação VNet-to-VNet. Tem de completar a [Parte 3](#crossprem) para criar e configurar o TestVNet1 e o Gateway VPN.

### <a name="step-1---create-the-second-virtual-network-and-vpn-gateway"></a><a name="createvnet2"></a>Passo 1 - Criar a segunda rede virtual e gateway VPN

#### <a name="1-declare-your-variables"></a>1. Declare as suas variáveis

Confirme que substitui os valores pelos que pretende utilizar para a configuração.

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. Criar a segunda rede virtual e a porta de entrada VPN no novo grupo de recursos

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Passo 2 - Criar uma ligação VNet-toVNet com a política IPsec/IKE

Semelhante à ligação VPN S2S, crie uma política IPsec/IKE e depois aplique-se à política à nova ligação.

#### <a name="1-create-an-ipsecike-policy"></a>1. Criar uma política IPsec/IKE

O script de exemplo seguinte cria uma política IPsec/IKE diferente com os seguintes parâmetros e algoritmos:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, SA Lifetime 14400 segundos & 10240000KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. Criar ligações VNet-to-VNet com a política IPsec/IKE

Crie uma ligação VNet-to-VNet e aplique a política IPsec/IKE que criou. Neste exemplo, ambos os gateways estão na mesma subscrição. Assim, é possível criar e configurar ambas as ligações com a mesma política IPsec/IKE na mesma sessão powerShell.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Uma vez especificada uma política IPsec/IKE numa ligação, o gateway Azure VPN apenas enviará ou aceitará a proposta IPsec/IKE com algoritmos criptográficos especificados e pontos fortes fundamentais nessa ligação específica. Certifique-se de que as políticas de IPsec para ambas as ligações são as mesmas, caso contrário a ligação VNet-to-VNet não estabelecerá.

Após completar estes passos, a ligação é estabelecida em poucos minutos, e você terá a seguinte topologia de rede como mostrado no início:

![ipsec-ike-política](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name="part-5---update-ipsecike-policy-for-a-connection"></a><a name ="managepolicy"></a>Parte 5 - Atualizar a política IPsec/IKE para uma ligação

A última secção mostra-lhe como gerir a política IPsec/IKE para uma ligação S2S ou VNet-to-VNet existente. O exercício abaixo acompanha-o através das seguintes operações numa ligação:

1. Mostrar a política IPsec/IKE de uma ligação
2. Adicione ou atualize a política IPsec/IKE a uma ligação
3. Retire a política IPsec/IKE de uma ligação

Os mesmos passos aplicam-se tanto às ligações S2S como VNet-to-VNet.

> [!IMPORTANT]
> A política IPsec/IKE é suportada apenas nos gateways VPN baseados em rotas *Standard* e *HighPerformance.* Não funciona com o Gateway Básico SKU ou com o gateway VPN baseado em políticas.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Mostrar a política IPsec/IKE de uma ligação

O exemplo que se segue mostra como configurar a política IPsec/IKE numa ligação. Os scripts também continuam a partir dos exercícios acima.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

O último comando lista a atual política IPsec/IKE configurada na ligação, se houver alguma. Segue-se uma saída de amostra para a ligação:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES256
IkeIntegrity        : SHA384
DhGroup             : DHGroup24
PfsGroup            : PFS24
```

Se não houver uma política IPsec/IKE configurada, o comando (PS> $connection6. IpsecPolicies) obtém um retorno vazio. Não significa que o IPsec/IKE não esteja configurado na ligação, mas que não exista uma política personalizada de IPsec/IKE. A ligação real utiliza a política de padrão negociada entre o seu dispositivo VPN no local e o gateway VpN Azure.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Adicione ou atualize uma política IPsec/IKE para uma ligação

As medidas para adicionar uma nova política ou atualizar uma política existente sobre uma ligação são as mesmas: criar uma nova política e depois aplicar a nova política à ligação.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Para ativar os "UsePolicyBasedTrafficSElectors" ao ligar-se a um dispositivo VPN baseado na política no local, adicione o parâmetro "UsePolicyBaseTrafficS" ao cmdlet ou defina-o para $False desativar a opção:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

Pode obter a ligação novamente para verificar se a apólice é atualizada.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Deve ver a saída da última linha, como mostra o seguinte exemplo:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES128
IkeIntegrity        : SHA1
DhGroup             : DHGroup14
PfsGroup            : None
```

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Remova uma política IPsec/IKE de uma ligação

Uma vez removido a política personalizada de uma ligação, o gateway Azure VPN reverte para a [lista padrão de propostas IPsec/IKE](vpn-gateway-about-vpn-devices.md) e renegocia novamente com o seu dispositivo VPN no local.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Pode utilizar o mesmo guião para verificar se a apólice foi removida da ligação.

## <a name="next-steps"></a>Passos seguintes

Consulte a [Connect vários dispositivos VPN baseados em políticas no local](vpn-gateway-connect-multiple-policybased-rm-ps.md) para obter mais detalhes sobre os selecionadores de tráfego baseados em políticas.

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter os passos.
