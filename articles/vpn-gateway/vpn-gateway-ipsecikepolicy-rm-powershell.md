---
title: 'Configure a política de IPsec/IKE para ligações S2S VPN ou VNet a VNet: Azure Resource Manager: PowerShell | Microsoft Docs'
description: Configure a política de IPsec/IKE para ligações S2S ou VNet a VNet com Gateways de VPN do Azure com o Azure Resource Manager e o PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: d04d62d66b4ba22437e6d854566f8bbf5536a6fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66121080"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>Configurar a política de IPsec/IKE para ligações S2S VPN ou VNet a VNet

Este artigo orienta-o pelos passos para configurar a política de IPsec/IKE para ligações de VPN de Site a Site ou VNet a VNet com o modelo de implementação do Resource Manager e o PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about"></a>Sobre os parâmetros de política de IPsec e IKE para gateways de VPN do Azure
Protocolo IPsec e IKE standard suporta uma vasta gama de algoritmos criptográficos em várias combinações. Consulte a [sobre os requisitos criptográficos e gateways de VPN do Azure](vpn-gateway-about-compliance-crypto.md) para ver como isso pode ajudar a garantir que, em vários locais e conectividade VNet-para-VNet satisfazem os requisitos de conformidade ou de segurança.

Este artigo fornece instruções para criar e configurar uma política de IPsec/IKE e aplicam-se a uma ligação nova ou existente:

* [Parte 1 - fluxo de trabalho para criar e definir a política de IPsec/IKE](#workflow)
* [Principais pontos fortes e algoritmos de criptográficos de parte 2 – suportado](#params)
* [Parte 3: criar uma nova ligação de S2S VPN com a política de IPsec/IKE](#crossprem)
* [Parte 4 - criar uma nova ligação de VNet a VNet com a política de IPsec/IKE](#vnet2vnet)
* [Parte 5 - gerir (criar, adicionar, remover) políticas de IPsec/IKE para uma ligação](#managepolicy)

> [!IMPORTANT]
> 1. Tenha em atenção que a política de IPsec/IKE só funciona no gateway SKUs seguinte:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (route-based)
>    * ***Standard*** e ***HighPerformance*** (baseado na rota)
> 2. Só pode especificar ***uma*** combinação de políticas para uma determinada ligação.
> 3. Tem de especificar todos os parâmetros e algoritmos de IKE (modo principal) e IPsec (modo rápido). Não é permitida a especificação da política parcial.
> 4. Consulte com suas especificações de fornecedor de dispositivo VPN para garantir que a política é suportada nos seus dispositivos VPN no local. S2S ou ligações de VNet para vnet não é possível estabelecer se as políticas são incompatíveis.

## <a name ="workflow"></a>Parte 1 - fluxo de trabalho para criar e definir a política de IPsec/IKE
Esta secção descreve o fluxo de trabalho para criar e atualizar a política de IPsec/IKE numa conexão de S2S VPN ou VNet a VNet:
1. Criar uma rede virtual e um gateway de VPN
2. Criar um gateway de rede local para cruzada ligação locais ou outra rede virtual e um gateway para ligação de VNet a VNet
3. Criar uma política de IPsec/IKE com parâmetros e algoritmos selecionados
4. Criar uma ligação (IPsec ou VNet2VNet) com a política de IPsec/IKE
5. Adicionar/atualizar/remover uma política de IPsec/IKE para uma ligação existente

As instruções neste artigo ajuda-o a preparar e configurar políticas de IPsec/IKE, conforme mostrado no diagrama:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name ="params"></a>Parte 2 - suportado de algoritmos criptográficos e restrições de chave

A tabela seguinte lista os algoritmos de criptográficos suportados e os principais pontos fortes de configuráveis pelos clientes:

| **IPsec/IKEv2**  | **Opções**    |
| ---  | --- 
| Encriptação IKEv2 | AES256, AES192, AES128, DES3, DES  
| Integridade do IKEv2  | SHA384, SHA256, SHA1, MD5  |
| Grupo DH         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| Encriptação do IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nenhum    |
| Integridade do IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Grupo PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Nenhum 
| Duração de SA QM   | (**Opcional**: valores predefinidos são utilizados se não for especificados)<br>Segundos (número inteiro; **mín. 300** /predefinição de 27000 segundos)<br>KBytes (número inteiro; **mín. 1024** /predefinição de 102400000 KBytes)   |
| Seletor de tráfego | UsePolicyBasedTrafficSelectors * * ($True/$False; **Opcional**, predefinido $False se não for especificado)    |
|  |  |

> [!IMPORTANT]
> 1. **A configuração do dispositivo VPN no local tem de corresponder ou conter os seguintes algoritmos e parâmetros que especificar na política de IPsec/IKE do Azure:**
>    * Algoritmo de encriptação do IKE (modo principal / fase 1)
>    * Algoritmo de integridade do IKE (modo principal / fase 1)
>    * Grupo DH (modo principal / fase 1)
>    * Algoritmo de encriptação de IPsec (modo rápido / fase 2)
>    * Algoritmo de integridade do IPsec (modo rápido / fase 2)
>    * Grupo PFS (modo rápido / fase 2)
>    * Seletor de tráfego (se for utilizada UsePolicyBasedTrafficSelectors)
>    * A duração do SA é apenas a especificação local, não é necessário corresponder.
>
> 2. **Se GCMAES for utilizado como para o algoritmo de encriptação de IPsec, tem de selecionar o mesmo algoritmo GCMAES e o comprimento da chave de integridade do IPsec; Por exemplo, usando GCMAES128 para ambos**
> 3. Na tabela acima:
>    * IKEv2 corresponde ao modo principal ou de fase 1
>    * IPsec corresponde ao modo rápido ou de fase 2
>    * Grupo DH Especifica o grupo de Diffie-Hellmen utilizado no modo principal ou de fase 1
>    * Grupo PFS especificado o grupo de Diffie-Hellmen utilizado no modo rápido ou de fase 2
> 4. A duração do SA do Modo Principal do IKEv2 é fixa em 28 800 segundos em gateways de VPN do Azure
> 5. Definição de "UsePolicyBasedTrafficSelectors" $True numa ligação irá configurar o gateway de VPN do Azure para ligar à firewall baseada em políticas VPN no local. Se ativar PolicyBasedTrafficSelectors, tem de garantir que o dispositivo VPN tem os seletores de tráfego correspondentes definidos com todas as combinações de seu local de rede prefixos (gateway de rede local) de/para os prefixos de rede virtual do Azure, em vez de qualquer para qualquer. Por exemplo, se os prefixos de rede local são 10.1.0.0/16 e 10.2.0.0/16, e os prefixos de rede virtual são 192.168.0.0/16 e 172.16.0.0/16, tem de especificar os seletores de tráfego seguintes:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Para obter mais informações sobre a seletores de tráfego baseado em políticas, consulte [ligar dispositivos VPN baseado em políticas vários locais](vpn-gateway-connect-multiple-policybased-rm-ps.md).

A tabela seguinte lista os grupos de Diffie-Hellman correspondente, suportado pela política personalizada:

| **Grupo Diffie-Hellman**  | **DHGroup**              | **PFSGroup** | **Comprimento da chave** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | MODP de 768 bits   |
| 2                         | DHGroup2                 | PFS2         | MODP de 1024 bits  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | MODP de 2048 bits  |
| 19                        | ECP256                   | ECP256       | ECP de 256 bits    |
| 20                        | ECP384                   | ECP284       | ECP de 384 bits    |
| 24                        | DHGroup24                | PFS24        | MODP de 2048 bits  |

Consulte [RFC3526](https://tools.ietf.org/html/rfc3526) e [RFC5114](https://tools.ietf.org/html/rfc5114) para obter mais detalhes.

## <a name ="crossprem"></a>Parte 3: criar uma nova ligação de S2S VPN com a política de IPsec/IKE

Esta secção explica os passos da criação de uma ligação S2S VPN com uma política de IPsec/IKE. Os passos seguintes criam a ligação, conforme mostrado no diagrama:

![política de s2s](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Ver [criar uma ligação S2S VPN](vpn-gateway-create-site-to-site-rm-powershell.md) para obter mais instruções passo a passo para criar uma ligação de S2S VPN.

### <a name="before"></a>Antes de começar

* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Instale os cmdlets do PowerShell do Azure Resource Manager. Ver [descrição geral do Azure PowerShell](/powershell/azure/overview) para obter mais informações sobre como instalar os cmdlets do PowerShell.

### <a name="createvnet1"></a>Passo 1 – criar a rede virtual, o gateway de VPN e o gateway de rede local

#### <a name="1-declare-your-variables"></a>1. Declarar as variáveis

Para este exercício, vamos começar por declarar as nossas variáveis. Verifique se substitui os valores pelos seus quando configurar para produção.

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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Ligar à sua subscrição e criar um novo grupo de recursos

Confirme que muda para o modo do PowerShell para utilizar os cmdlets do Resource Manager. Para obter mais informações, veja [Utilizar o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

Abra a consola do PowerShell e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar na ligação:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Criar a rede virtual, o gateway de VPN e o gateway de rede local

O exemplo seguinte cria a rede virtual, TestVNet1, com três sub-redes e o gateway de VPN. Quando estiver a substituir os valores, é importante que dê sempre à sub-rede do gateway o nome específico GatewaySubnet. Se der outro nome, a criação da gateway falha.

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

### <a name="s2sconnection"></a>Passo 2 - criar uma ligação S2S VPN com uma política de IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Criar uma política de IPsec/IKE

O script de exemplo seguinte cria uma política de IPsec/IKE com os parâmetros e algoritmos seguintes:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS None, 14400 de duração de SA segundos e 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Se usar GCMAES para IPsec, tem de utilizar o mesmo algoritmo GCMAES e o comprimento da chave de encriptação de IPsec e a integridade. Por exemplo acima, os parâmetros correspondentes será "-IpsecEncryption GCMAES256 - IpsecIntegrity GCMAES256" ao utilizar GCMAES256.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Criar a ligação de S2S VPN com a política de IPsec/IKE

Criar uma ligação S2S VPN e aplicar a política de IPsec/IKE que criou anteriormente.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Opcionalmente, pode adicionar "-UsePolicyBasedTrafficSelectors $True" para o cmdlet de ligação criar para ativar o gateway de VPN do Azure ligar a dispositivos VPN baseado em políticas no local, conforme descrito acima.

> [!IMPORTANT]
> Depois de uma política de IPsec/IKE é especificada numa conexão, o gateway de VPN do Azure só irá enviar ou aceitar a proposta de IPsec/IKE com algoritmos criptográficos especificados e os principais pontos fortes nessa ligação específica. Certificar-se de que o dispositivo VPN no local para a ligação utiliza ou aceita a combinação exata de política, caso contrário, não irão estabelecer o túnel S2S VPN.


## <a name ="vnet2vnet"></a>Parte 4 - criar uma nova ligação de VNet a VNet com a política de IPsec/IKE

Os passos da criação de uma ligação VNet a VNet com uma política de IPsec/IKE são semelhantes de uma ligação S2S VPN. Os seguintes scripts de criam a ligação, conforme mostrado no diagrama:

![política de v2v](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Ver [criar uma ligação VNet a VNet](vpn-gateway-vnet-vnet-rm-ps.md) para obter mais passos para criar uma ligação VNet a VNet. Tem de concluir [parte 3](#crossprem) para criar e configurar a TestVNet1 e o Gateway de VPN.

### <a name="createvnet2"></a>Passo 1 – criar a segunda rede virtual e gateway de VPN

#### <a name="1-declare-your-variables"></a>1. Declarar as variáveis

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

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. Criar a segunda rede virtual e gateway de VPN no novo grupo de recursos

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

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Passo 2 - criar uma ligação de VNet toVNet com a política de IPsec/IKE

É semelhante à ligação S2S VPN, criar uma política de IPsec/IKE, em seguida, aplicar a política para a nova ligação.

#### <a name="1-create-an-ipsecike-policy"></a>1. Criar uma política de IPsec/IKE

O script de exemplo seguinte cria uma política IPsec/IKE diferente com os seguintes parâmetros e algoritmos:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, 14400 de duração de SA segundos & 102400000KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. Criar ligações de VNet a VNet com a política de IPsec/IKE

Criar uma ligação VNet a VNet e aplicar a política de IPsec/IKE que criou. Neste exemplo, ambos os gateways estão na mesma subscrição. Portanto, é possível criar e configurar ambas as ligações com a mesma política de IPsec/IKE na mesma sessão do PowerShell.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Depois de uma política de IPsec/IKE é especificada numa conexão, o gateway de VPN do Azure só irá enviar ou aceitar a proposta de IPsec/IKE com algoritmos criptográficos especificados e os principais pontos fortes nessa ligação específica. Certifique-se de que as políticas de IPsec para ambas as ligações são o mesmo, caso contrário, não irão estabelecer a ligação de VNet a VNet.

Depois de concluir estes passos, a ligação é estabelecida dentro de alguns minutos e terá a seguinte topologia de rede, conforme mostrado no início:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name ="managepolicy"></a>Parte 5 - política de IPsec/IKE de atualização para uma ligação

A última seção mostra-lhe como gerir a política de IPsec/IKE para uma ligação de S2S ou VNet a VNet existente. O exercício abaixo explica-lhe as seguintes operações numa conexão:

1. Mostrar a política de IPsec/IKE de uma ligação
2. Adicionar ou atualizar a política de IPsec/IKE para uma ligação
3. Remove a política de IPsec/IKE de uma ligação

Os mesmos passos aplicam-se às ligações S2S e VNet-para-VNet.

> [!IMPORTANT]
> Política de IPsec/IKE é suportada no *padrão* e *HighPerformance* baseado na rota apenas gateways VPN. Ele não funciona no SKU de gateway básico ou o gateway de VPN baseado em políticas.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Mostrar a política de IPsec/IKE de uma ligação

O exemplo seguinte mostra como obter a política de IPsec/IKE configurada numa conexão. Os scripts também continuam de exercícios acima.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

O último comando apresenta uma lista a política de IPsec/IKE atual configurada na ligação, se existir alguma. Segue-se uma saída de exemplo para a ligação:

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

Se não existir nenhuma política de IPsec/IKE configurado, o comando (PS > $connection6.policy) obtém vazio retorno. Não significa IPsec/IKE não está configurado na ligação, mas que não existe nenhuma política de IPsec/IKE personalizada. A ligação real utiliza a política predefinida negociada entre o dispositivo VPN no local e o gateway de VPN do Azure.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Adicionar ou atualizar uma política de IPsec/IKE para uma ligação

Os passos para adicionar uma nova política ou atualizar uma política existente numa conexão são os mesmos: criar uma nova política, em seguida, aplicar a nova política para a ligação.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Para ativar a "UsePolicyBasedTrafficSelectors" ao ligar a um dispositivo VPN baseado em políticas no local, adicione o "-UsePolicyBaseTrafficSelectors" parâmetro para o cmdlet, ou defina-o como $False para desativar a opção:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

Pode obter a ligação novamente para verificar se a política for atualizada.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Deverá ver o resultado da última linha, conforme mostrado no exemplo a seguir:

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

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Remove uma política de IPsec/IKE de uma ligação

Depois de remover a política personalizada de uma ligação, o gateway de VPN do Azure reverte para o [lista predefinida de propostas de IPsec/IKE](vpn-gateway-about-vpn-devices.md) e renegotiates novamente com o dispositivo VPN no local.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Pode utilizar o mesmo script para verificar se a política foi removida a partir da ligação.

## <a name="next-steps"></a>Passos Seguintes

Ver [ligar dispositivos VPN baseado em políticas vários locais](vpn-gateway-connect-multiple-policybased-rm-ps.md) para obter mais detalhes sobre os seletores de tráfego baseado em políticas.

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter os passos.
