---
title: 'Configurar a política de IPsec/IKE para conexões VPN S2S ou VNet para VNet: Azure Resource Manager: PowerShell | Microsoft Docs'
description: Configure a política de IPsec/IKE para conexões S2S ou VNet para VNet com gateways de VPN do Azure usando o Azure Resource Manager e o PowerShell.
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
ms.openlocfilehash: a4a0431a8d40f7905805e0a7d902988b7eb26208
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035040"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>Configurar a política de IPsec/IKE para conexões VPN S2S ou VNet para VNet

Este artigo orienta você pelas etapas para configurar a política de IPsec/IKE para conexões de VPN site a site ou VNet para VNet usando o modelo de implantação do Gerenciador de recursos e o PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about"></a>Sobre os parâmetros de política IKE e IPsec para gateways de VPN do Azure
O IPsec e o protocolo IKE Standard dão suporte a uma ampla gama de algoritmos criptográficos em várias combinações. Consulte [sobre os requisitos de criptografia e os gateways de VPN do Azure](vpn-gateway-about-compliance-crypto.md) para ver como isso pode ajudar a garantir a conectividade entre locais e VNet a vnet para atender aos seus requisitos de conformidade ou de segurança.

Este artigo fornece instruções para criar e configurar uma política de IPsec/IKE e aplicar a uma conexão nova ou existente:

* [Parte 1-fluxo de trabalho para criar e definir a política de IPsec/IKE](#workflow)
* [Parte 2-algoritmos de criptografia e forças de chave com suporte](#params)
* [Parte 3-criar uma nova conexão VPN S2S com a política de IPsec/IKE](#crossprem)
* [Parte 4-criar uma nova conexão de VNet para VNet com a política de IPsec/IKE](#vnet2vnet)
* [Parte 5-gerenciar (criar, adicionar, remover) política de IPsec/IKE para uma conexão](#managepolicy)

> [!IMPORTANT]
> 1. Observe que a política de IPsec/IKE só funciona nas seguintes SKUs de gateway:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (baseado em rota)
>    * ***Standard*** e ***HighPerformance*** (baseado em rota)
> 2. Só pode especificar ***uma*** combinação de políticas para uma determinada ligação.
> 3. Você deve especificar todos os algoritmos e parâmetros para IKE (modo principal) e IPsec (modo rápido). Não é permitida a especificação da política parcial.
> 4. Consulte as especificações do fornecedor do dispositivo VPN para garantir que a política tenha suporte em seus dispositivos VPN locais. As conexões S2S ou VNet para VNet não podem estabelecer se as políticas são incompatíveis.

## <a name ="workflow"></a>Parte 1-fluxo de trabalho para criar e definir a política de IPsec/IKE
Esta seção descreve o fluxo de trabalho para criar e atualizar a política de IPsec/IKE em uma conexão VPN S2S ou VNet para VNet:
1. Criar uma rede virtual e um gateway de VPN
2. Criar um gateway de rede local para conexão entre locais ou outra rede virtual e gateway para conexão VNet a VNet
3. Criar uma política de IPsec/IKE com algoritmos e parâmetros selecionados
4. Criar uma conexão (IPsec ou VNet2VNet) com a política de IPsec/IKE
5. Adicionar/atualizar/remover uma política de IPsec/IKE para uma conexão existente

As instruções neste artigo ajudam você a configurar e configurar as políticas de IPsec/IKE, conforme mostrado no diagrama:

![IPSec-IKE-política](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name ="params"></a>Parte 2-algoritmos de criptografia com suporte & forças de chave

A tabela a seguir lista os algoritmos de criptografia com suporte e as forças de chave configuráveis pelos clientes:

| **IPsec/IKEv2**  | **Opções**    |
| ---  | --- 
| Encriptação IKEv2 | AES256, AES192, AES128, DES3, DES  
| Integridade do IKEv2  | SHA384, SHA256, SHA1, MD5  |
| Grupo DH         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| Encriptação do IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nenhum    |
| Integridade do IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Grupo PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Nenhum 
| Duração de SA QM   | (**Opcional**: os valores padrão serão usados se não forem especificados)<br>Segundos (número inteiro; **mín. 300** /predefinição de 27000 segundos)<br>KBytes (número inteiro; **mín. 1024** /predefinição de 102400000 KBytes)   |
| Seletor de tráfego | UsePolicyBasedTrafficSelectors * * ($True/$False; **Opcional**, padrão $false se não for especificado)    |
|  |  |

> [!IMPORTANT]
> 1. **A configuração do dispositivo VPN local deve corresponder ou conter os seguintes algoritmos e parâmetros especificados na política de IPsec/IKE do Azure:**
>    * Algoritmo de criptografia IKE (modo principal/fase 1)
>    * Algoritmo de integridade IKE (modo principal/fase 1)
>    * Grupo DH (modo principal/fase 1)
>    * Algoritmo de criptografia IPsec (modo rápido/fase 2)
>    * Algoritmo de integridade IPsec (modo rápido/fase 2)
>    * Grupo PFS (modo rápido/fase 2)
>    * Seletor de tráfego (se UsePolicyBasedTrafficSelectors for usado)
>    * A duração do SA é apenas a especificação local, não é necessário corresponder.
>
> 2. **Se GCMAES for usado como para o algoritmo de criptografia IPsec, você deverá selecionar o mesmo algoritmo GCMAES e o comprimento da chave para a integridade do IPsec; por exemplo, usando GCMAES128 para ambos**
> 3. Na tabela acima:
>    * IKEv2 corresponde ao modo principal ou à fase 1
>    * O IPsec corresponde ao modo rápido ou à fase 2
>    * Grupo DH especifica o grupo Diffie-Hellmen usado no modo principal ou na fase 1
>    * O grupo PFS especificou o grupo Diffie-Hellmen usado no modo rápido ou na fase 2
> 4. A duração do SA do Modo Principal do IKEv2 é fixa em 28 800 segundos em gateways de VPN do Azure
> 5. Definir "UsePolicyBasedTrafficSelectors" como $True em uma conexão configurará o gateway de VPN do Azure para se conectar ao firewall de VPN baseado em políticas no local. Se você habilitar o PolicyBasedTrafficSelectors, precisará garantir que o dispositivo VPN tenha os seletores de tráfego correspondentes definidos com todas as combinações de prefixos de rede local (gateway de rede locais) de/para os prefixos de rede virtual do Azure, em vez de qualquer para qualquer. Por exemplo, se os prefixos de rede local são 10.1.0.0/16 e 10.2.0.0/16, e os prefixos de rede virtual são 192.168.0.0/16 e 172.16.0.0/16, tem de especificar os seletores de tráfego seguintes:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Para obter mais informações sobre os seletores de tráfego com base em políticas, consulte [conectar vários dispositivos VPN baseados em políticas locais](vpn-gateway-connect-multiple-policybased-rm-ps.md).

A tabela a seguir lista os grupos Diffie-Hellman correspondentes com suporte da política personalizada:

| **Grupo Diffie-Hellman**  | **DHGroup**              | **PFSGroup** | **Comprimento da chave** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | MODP de 768 bits   |
| 2                         | DHGroup2                 | PFS2         | MODP de 1024 bits  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | MODP de 2048 bits  |
| 19                        | ECP256                   | ECP256       | ECP de 256 bits    |
| 20                        | ECP384                   | ECP384       | ECP de 384 bits    |
| 24                        | DHGroup24                | PFS24        | MODP de 2048 bits  |

Consulte [RFC3526](https://tools.ietf.org/html/rfc3526) e [RFC5114](https://tools.ietf.org/html/rfc5114) para obter mais detalhes.

## <a name ="crossprem"></a>Parte 3-criar uma nova conexão VPN S2S com a política de IPsec/IKE

Esta seção orienta você pelas etapas de criação de uma conexão VPN S2S com uma política de IPsec/IKE. As etapas a seguir criam a conexão, conforme mostrado no diagrama:

![política S2S](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Consulte [criar uma conexão VPN S2S](vpn-gateway-create-site-to-site-rm-powershell.md) para obter instruções passo a passo mais detalhadas para criar uma conexão VPN S2S.

### <a name="before"></a>Antes de começar

* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Instale os cmdlets do Azure Resource Manager PowerShell. Consulte [visão geral do Azure PowerShell](/powershell/azure/overview) para obter mais informações sobre como instalar os cmdlets do PowerShell.

### <a name="createvnet1"></a>Etapa 1-criar a rede virtual, o gateway de VPN e o gateway de rede local

#### <a name="1-declare-your-variables"></a>1. Declarar as variáveis

Para este exercício, começamos declarando nossas variáveis. Verifique se substitui os valores pelos seus quando configurar para produção.

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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Conectar-se à sua assinatura e criar um novo grupo de recursos

Confirme que muda para o modo do PowerShell para utilizar os cmdlets do Resource Manager. Para obter mais informações, veja [Utilizar o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

Abra a consola do PowerShell e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar na ligação:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Criar a rede virtual, o gateway de VPN e o gateway de rede local

O exemplo a seguir cria a rede virtual, TestVNet1, com três sub-redes e o gateway de VPN. Quando estiver a substituir os valores, é importante que dê sempre à sub-rede do gateway o nome específico GatewaySubnet. Se der outro nome, a criação da gateway falha.

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

### <a name="s2sconnection"></a>Etapa 2-criar uma conexão VPN S2S com uma política de IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Criar uma política de IPsec/IKE

O script de exemplo a seguir cria uma política de IPsec/IKE com os seguintes algoritmos e parâmetros:

* IKEv2: AES256, SHA384, DHGroup24
* IPSec AES256, SHA256, PFS None, tempo de vida de SA de 14400 segundos & 102400000 KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Se você usar o GCMAES para IPsec, deverá usar o mesmo algoritmo GCMAES e o comprimento da chave para a criptografia e a integridade do IPsec. Por exemplo acima, os parâmetros correspondentes serão "-IpsecEncryption GCMAES256-IpsecIntegrity GCMAES256" ao usar GCMAES256.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Criar a conexão VPN S2S com a política de IPsec/IKE

Crie uma conexão VPN S2S e aplique a política de IPsec/IKE criada anteriormente.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Opcionalmente, você pode adicionar "-UsePolicyBasedTrafficSelectors $True" ao cmdlet Create Connection para habilitar o gateway de VPN do Azure para se conectar a dispositivos VPN baseados em políticas localmente, conforme descrito acima.

> [!IMPORTANT]
> Depois que uma política de IPsec/IKE for especificada em uma conexão, o gateway de VPN do Azure enviará ou aceitará apenas a proposta de IPsec/IKE com algoritmos de criptografia e forças de chave especificados nessa conexão específica. Verifique se o dispositivo VPN local para a conexão usa ou aceita a combinação de política exata, caso contrário, o túnel VPN S2S não estabelecerá.


## <a name ="vnet2vnet"></a>Parte 4-criar uma nova conexão de VNet para VNet com a política de IPsec/IKE

As etapas de criação de uma conexão de VNet para VNet com uma política de IPsec/IKE são semelhantes às de uma conexão VPN S2S. Os scripts de exemplo a seguir criam a conexão, conforme mostrado no diagrama:

![V2V-política](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Consulte [criar uma conexão de vnet para vnet](vpn-gateway-vnet-vnet-rm-ps.md) para obter etapas mais detalhadas para criar uma conexão de VNet para vnet. Você deve concluir a [parte 3](#crossprem) para criar e configurar o TestVNet1 e o gateway de VPN.

### <a name="createvnet2"></a>Etapa 1-criar a segunda rede virtual e o gateway de VPN

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

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. Criar a segunda rede virtual e o gateway de VPN no novo grupo de recursos

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

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Etapa 2-criar uma conexão VNet-vnet para vnet com a política de IPsec/IKE

Semelhante à conexão VPN S2S, crie uma política de IPsec/IKE e, em seguida, aplique-a à política para a nova conexão.

#### <a name="1-create-an-ipsecike-policy"></a>1. Criar uma política de IPsec/IKE

O script de exemplo seguinte cria uma política IPsec/IKE diferente com os seguintes parâmetros e algoritmos:
* IKEv2: AES128, SHA1, DHGroup14
* IPSec GCMAES128, GCMAES128, PFS14, tempo de vida da SA 14400 segundos & 102400000 KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. Criar conexões de VNet para VNet com a política de IPsec/IKE

Crie uma conexão de VNet para VNet e aplique a política de IPsec/IKE que você criou. Neste exemplo, ambos os gateways estão na mesma assinatura. Portanto, é possível criar e configurar ambas as conexões com a mesma política de IPsec/IKE na mesma sessão do PowerShell.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Depois que uma política de IPsec/IKE for especificada em uma conexão, o gateway de VPN do Azure enviará ou aceitará apenas a proposta de IPsec/IKE com algoritmos de criptografia e forças de chave especificados nessa conexão específica. Verifique se as políticas de IPsec para ambas as conexões são as mesmas, caso contrário, a conexão VNet a VNet não será estabelecida.

Depois de concluir essas etapas, a conexão será estabelecida em alguns minutos e você terá a seguinte topologia de rede, conforme mostrado no início:

![IPSec-IKE-política](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name ="managepolicy"></a>Parte 5-atualizar a política de IPsec/IKE para uma conexão

A última seção mostra como gerenciar a política de IPsec/IKE para uma conexão S2S ou VNet para VNet existente. O exercício a seguir orienta você pelas seguintes operações em uma conexão:

1. Mostrar a política de IPsec/IKE de uma conexão
2. Adicionar ou atualizar a política de IPsec/IKE para uma conexão
3. Remover a política de IPsec/IKE de uma conexão

As mesmas etapas se aplicam a conexões S2S e VNet a VNet.

> [!IMPORTANT]
> A política de IPsec/IKE tem suporte somente em gateways de VPN baseados em rota *Standard* e *HighPerformance* . Ele não funciona no SKU do gateway básico ou no gateway de VPN baseado em políticas.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Mostrar a política de IPsec/IKE de uma conexão

O exemplo a seguir mostra como obter a política de IPsec/IKE configurada em uma conexão. Os scripts também continuam dos exercícios acima.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

O último comando lista a política de IPsec/IKE atual configurada na conexão, se houver alguma. Veja a seguir um exemplo de saída para a conexão:

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

Se não houver nenhuma política de IPsec/IKE configurada, o comando (PS > $connection 6. Policy) obterá um retorno vazio. Isso não significa que o IPsec/IKE não esteja configurado na conexão, mas que não haja nenhuma política de IPsec/IKE personalizada. A conexão real usa a política padrão negociada entre o dispositivo VPN local e o gateway de VPN do Azure.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Adicionar ou atualizar uma política de IPsec/IKE para uma conexão

As etapas para adicionar uma nova política ou atualizar uma política existente em uma conexão são as mesmas: criar uma nova política e aplicar a nova política à conexão.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Para habilitar "UsePolicyBasedTrafficSelectors" ao se conectar a um dispositivo VPN baseado em política local, adicione o parâmetro "-UsePolicyBaseTrafficSelectors" ao cmdlet ou defina-o como $False para desabilitar a opção:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

Você pode obter a conexão novamente para verificar se a política está atualizada.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Você deve ver a saída da última linha, conforme mostrado no exemplo a seguir:

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

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Remover uma política de IPsec/IKE de uma conexão

Depois de remover a política personalizada de uma conexão, o gateway de VPN do Azure reverte de volta para a [lista padrão de propostas de IPSec/IKE](vpn-gateway-about-vpn-devices.md) e renegocia novamente com seu dispositivo VPN local.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Você pode usar o mesmo script para verificar se a política foi removida da conexão.

## <a name="next-steps"></a>Passos seguintes

Consulte [conectar vários dispositivos VPN baseados em políticas locais](vpn-gateway-connect-multiple-policybased-rm-ps.md) para obter mais detalhes sobre os seletores de tráfego com base em políticas.

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter os passos.
