---
title: Configurar ligações de VPN de site a site do Azure Stack | Documentos da Microsoft
description: Saiba mais sobre a política de IPsec/IKE para ligações de VPN ou VNet a VNet de site a site no Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: 07e598d6fd4ed2937d86f31593a220c0c28ba328
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58074663"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-connections"></a>Configurar a política de IPsec/IKE para ligações de VPN de site a site

Este artigo explica os passos para configurar uma política de IPsec/IKE para VPN site a site (S2S) ligações no Azure Stack.

>[!NOTE]
> Tem de executar compilação do Azure Stack **1809** ou posterior para utilizar esta funcionalidade.  Se estiver a executar atualmente uma compilação antes 1809, atualize o sistema do Azure Stack para a compilação mais recente antes de tentar utilizar esta funcionalidade ou siga os passos neste artigo.

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>Parâmetros de política de IPsec e IKE para gateways de VPN

O protocolo IPsec e IKE standard suporta uma vasta gama de algoritmos criptográficos em várias combinações. Para ver quais os parâmetros que são suportados no Azure Stack, veja [parâmetros IPsec/IKE](azure-stack-vpn-gateway-settings.md#ipsecike-parameters), que pode ajudar a satisfazer os requisitos de segurança ou conformidade.

Este artigo fornece instruções sobre como criar e configurar uma política de IPsec/IKE e aplicam-se a uma ligação nova ou existente.

## <a name="considerations"></a>Considerações

Tenha em atenção as seguintes considerações importantes ao utilizar estas políticas:

- A política de IPsec/IKE só funciona no *padrão* e *HighPerformance* SKUs de gateway (baseado na rota).

- Só pode especificar **um** combinação de política para uma determinada ligação.

- Tem de especificar todos os parâmetros e algoritmos de IKE (modo principal) e IPsec (modo rápido). Não é permitida a especificação da política parcial.

- Consulte com suas especificações de fornecedor de dispositivo VPN para garantir que a política é suportada nos seus dispositivos VPN no local. Não não possível estabelecer ligações site a site, se as políticas são incompatíveis.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a>Parte 1 - fluxo de trabalho para criar e definir a política de IPsec/IKE

Esta secção descreve o fluxo de trabalho necessário para criar e atualizar a política de IPsec/IKE numa conexão de VPN de site a site:

1. Crie uma rede virtual e um gateway VPN.

2. Crie um gateway de rede local para a ligação entre locais.

3. Crie uma política de IPsec/IKE com parâmetros e algoritmos selecionados.

4. Crie uma ligação IPSec com a política de IPsec/IKE.

5. Adicionar/atualizar/remover uma política de IPsec/IKE para uma ligação existente.

As instruções na ajuda neste artigo definir e configurar políticas de IPsec/IKE, conforme mostrado na figura a seguir:

![Definir e configurar políticas de IPsec/IKE](media/azure-stack-vpn-s2s/site-to-site.png)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>Principais pontos fortes e algoritmos de criptográficos de parte 2 – suportado

A tabela seguinte lista os algoritmos de criptográficos suportados e os principais pontos fortes de configuráveis pelos clientes do Azure Stack:

| IPsec/IKEv2                                          | Opções                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| Encriptação IKEv2                                     | AES256, AES192, AES128, DES3, DES                                        |
| Integridade do IKEv2                                      | SHA384, SHA256, SHA1, MD5                                                |
| Grupo DH                                             | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, nenhum         |
| Encriptação do IPsec                                     | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nenhum |
| Integridade do IPsec                                      | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                       |
| Grupo PFS                                            | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Nenhum                         |
| Duração de SA QM                                       | (Opcional: valores predefinidos são utilizados se não for especificados)<br />                         Segundos (inteiro; mín. 300/predefinido 27000 segundos)<br />                         KBytes (inteiro; mín. 1024/predefinição de 102400000 KBytes) |
| Seletor de tráfego                                     | Seletores de tráfego com base em políticas não são suportadas no Azure Stack.         |

- A configuração de dispositivo VPN local deve corresponder ou deve conter os seguintes algoritmos e parâmetros que especificar na política de IPsec/IKE do Azure:

  - Algoritmo de encriptação do IKE (modo principal / fase 1)
  - Algoritmo de integridade do IKE (modo principal / fase 1)
  - Grupo DH (modo principal / fase 1)
  - Algoritmo de encriptação de IPsec (modo rápido / fase 2)
  - Algoritmo de integridade do IPsec (modo rápido / fase 2)
  - Grupo PFS (modo rápido / fase 2)
  - A duração do SA é apenas a especificação local, não é necessário corresponder.

- Se GCMAES for utilizado como para o algoritmo de encriptação de IPsec, tem de selecionar o mesmo algoritmo GCMAES e o comprimento da chave de integridade do IPsec; Por exemplo, usando GCMAES128 para ambos.

- Na tabela anterior:

  - IKEv2 corresponde ao modo principal ou de fase 1
  - IPsec corresponde ao modo rápido ou de fase 2
  - Grupo DH Especifica o grupo de Diffie-Hellmen utilizado no modo principal ou de fase 1
  - Grupo PFS especificado o grupo de Diffie-Hellmen utilizado no modo rápido ou de fase 2

- Duração de SA de modo principal do IKEv2 é fixa em 28 800 segundos em gateways de VPN do Azure Stack.

A tabela seguinte lista os grupos de Diffie-Hellman correspondente, suportado pela política personalizada:

| Grupo Diffie-Hellman | DHGroup   | PFSGroup      | Comprimento da chave    |
|----------------------|-----------|---------------|---------------|
| 1                    | DHGroup1  | PFS1          | MODP de 768 bits  |
| 2                    | DHGroup2  | PFS2          | MODP de 1024 bits |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | MODP de 2048 bits |
| 19                   | ECP256    | ECP256        | ECP de 256 bits   |
| 20                   | ECP384    | ECP384        | ECP de 384 bits   |
| 24                   | DHGroup24 | PFS24         | MODP de 2048 bits |

Para obter mais informações, consulte [RFC3526](https://tools.ietf.org/html/rfc3526) e [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>Parte 3: criar uma nova ligação de VPN de site a site com a política de IPsec/IKE

Esta secção explica os passos para criar uma ligação de VPN de site a site com uma política de IPsec/IKE. Os passos seguintes criam a ligação, conforme mostrado na figura a seguir:

![site a site-política](media/azure-stack-vpn-s2s/site-to-site.png)

Para obter mais instruções passo a passo para criar uma ligação de VPN de site a site, consulte [criar uma ligação de VPN de site a site](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem os seguintes pré-requisitos:

- Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar sua [benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), ou inscrever-se um [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Os cmdlets do PowerShell do Azure Resource Manager. Ver [instalar o PowerShell para o Azure Stack](../azure-stack-powershell-install.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Passo 1 – criar a rede virtual, o gateway de VPN e o gateway de rede local

#### <a name="1-declare-variables"></a>1. Declarar variáveis

Para este exercício, comece por declarar as variáveis seguintes. Certifique-se de que substitua os marcadores de posição pelos seus próprios valores quando configurar para produção:

```powershell
$Sub1 = "\<YourSubscriptionName\>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Ligar à sua subscrição e criar um novo grupo de recursos

Confirme que muda para o modo do PowerShell para utilizar os cmdlets do Resource Manager. Para obter mais informações, consulte [ligue-se ao Azure Stack com o PowerShell como um utilizador](azure-stack-powershell-configure-user.md).

Abra a consola do PowerShell e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar na ligação:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Criar a rede virtual, o gateway de VPN e o gateway de rede local

O exemplo seguinte cria a rede virtual, **TestVNet1**, com três sub-redes e o gateway de VPN. Quando estiver a substituir valores, é importante que sempre atribua um nome à sub-rede do gateway especificamente **GatewaySubnet**. Se der outro nome, a criação da gateway falha.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>Passo 2 - criar uma ligação de VPN de site a site com uma política de IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Criar uma política de IPsec/IKE

Este script de exemplo cria uma política de IPsec/IKE com os parâmetros e algoritmos seguintes:

- IKEv2: AES128, SHA1, DHGroup14
- IPsec: AES256, SHA256, none, 14400 de duração de SA segundos e 102400000KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Se usar GCMAES para IPsec, tem de utilizar o mesmo algoritmo GCMAES e o comprimento da chave de encriptação de IPsec e a integridade.

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2. Criar a ligação de VPN de site a site com a política de IPsec/IKE

Criar uma ligação de VPN de site a site e aplicar a política de IPsec/IKE que criou anteriormente.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> Depois de uma política de IPsec/IKE é especificada numa conexão, o gateway de VPN do Azure só irá enviar ou aceitar a proposta de IPsec/IKE com algoritmos criptográficos especificados e os principais pontos fortes nessa ligação específica. Certificar-se de que o dispositivo VPN no local para a ligação utiliza ou aceita a combinação exata de política, caso contrário, que não será possível estabelecer o túnel VPN de site a site.

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>Parte 4 - política de IPsec/IKE de atualização para uma ligação

A secção anterior mostrou como gerir a política de IPsec/IKE para uma ligação site a site existente. A secção seguinte explica as seguintes operações numa conexão:

1. Mostrar a política de IPsec/IKE de uma ligação
2. Adicionar ou atualizar a política de IPsec/IKE para uma ligação
3. Remove a política de IPsec/IKE de uma ligação

> [!NOTE]
> Política de IPsec/IKE é suportada no *padrão* e *HighPerformance* baseado na rota apenas gateways VPN. Ele não funciona no *básica* SKU de gateway.

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Mostrar a política de IPsec/IKE de uma ligação

O exemplo seguinte mostra como obter a política de IPsec/IKE configurada numa conexão. Os scripts também continuam dos exercícios anteriores:

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

O último comando apresenta uma lista da política de IPsec/IKE atual configurada na ligação, se aplicável. O exemplo seguinte é uma saída de exemplo para a ligação:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

Se não existir nenhuma política de IPsec/IKE configurado, o comando `$connection6.policy` obtém vazio retorno. Não significa que o IPsec/IKE não está configurado na ligação; Isso significa que não existe nenhuma política de IPsec/IKE personalizada. A ligação real utiliza a política predefinida negociada entre o dispositivo VPN no local e o gateway de VPN do Azure.

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Adicionar ou atualizar uma política de IPsec/IKE para uma ligação

Os passos para adicionar uma nova política ou atualizar uma política existente numa conexão são os mesmos: criar uma nova política, em seguida, aplicar a nova política para a ligação.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Pode obter a ligação novamente para verificar se a política for atualizada:

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Deverá ver o resultado da última linha, conforme mostrado no exemplo a seguir:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Remove uma política de IPsec/IKE de uma ligação

Depois de remover a política personalizada de uma ligação, o gateway de VPN do Azure reverte para o [proposta de IPsec/IKE padrão](azure-stack-vpn-gateway-settings.md#ipsecike-parameters)e renegotiates com o dispositivo VPN no local.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = “AzS123”
$currentpolicy = $connection6.IpsecPolicies\[0\]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Pode utilizar o mesmo script para verificar se a política foi removida a partir da ligação.

## <a name="next-steps"></a>Passos Seguintes

- [Definições de configuração do gateway VPN para o Azure Stack](azure-stack-vpn-gateway-settings.md)
