---
title: As definições do gateway VPN para o Azure Stack | Documentos da Microsoft
description: Saiba mais sobre as definições de gateways de VPN que utiliza com o Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: ac713e4abacc8cece1b14972ddf3a1f3fe2f1cdf
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770191"
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>Definições de configuração do gateway VPN para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Um gateway de VPN é um tipo de gateway de rede virtual que envia o tráfego encriptado entre a rede virtual no Azure Stack e um gateway VPN remoto. O gateway VPN remoto pode estar no Azure, um dispositivo no seu datacenter ou num dispositivo noutro site. Se existir conectividade de rede entre os dois pontos de extremidade, pode estabelecer uma ligação segura de VPN Site a Site (S2S) entre as duas redes.

Uma ligação de gateway VPN depende da configuração de vários recursos, cada um deles contém definições configuráveis. Este artigo descreve os recursos e as definições relacionadas com a um gateway VPN para uma rede virtual que criar no modelo de implementação do Resource Manager. Encontrará descrições e diagramas de topologia para cada solução de ligação no [sobre o Gateway de VPN para o Azure Stack](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>Definições do gateway VPN

### <a name="gateway-types"></a>Tipos de gateway

Cada rede virtual do Azure Stack oferece suporte a um gateway de rede virtual individual, que tem de ser do tipo **Vpn**.  Esse suporte é diferente do Azure, o que oferece suporte a tipos adicionais.  

Quando cria um gateway de rede virtual, deve certificar-se de que o tipo de gateway está correto para a sua configuração. Requer um gateway de VPN a `-GatewayType Vpn`sinalizar; por exemplo:

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>SKUs de gateway

Quando cria um gateway de rede virtual, tem de especificar o SKU de gateway que pretende utilizar. Selecione os SKU que atendem as suas necessidades com base nos tipos de cargas de trabalho, taxas de transferência, funcionalidades e SLA.

O Azure Stack oferece o gateway de VPN SKUs mostrados na tabela seguinte.

|   | Débito do gateway VPN |Gateway de VPN de túneis IPsec do máximo |
|-------|-------|-------|
|**SKU básico**  | 100 Mbps  | 20    |
|**Standard SKU**           | 100 Mbps  | 20    |
|**SKU de elevado desempenho** | 200 Mbps    | 10    |

### <a name="resizing-gateway-skus"></a>Redimensionamento de SKUs de gateway

O Azure Stack não suporta um redimensionamento de SKUs entre os SKUs legados suportados.

Da mesma forma, o Azure Stack não suporta um redimensionamento de um SKU legado suportado (Basic, Standard e HighPerformance) para um SKU de mais recente suportado pelo Azure (VpnGw1, VpnGw2 e VpnGw3.)

### <a name="configure-the-gateway-sku"></a>Configurar o SKU de gateway

#### <a name="azure-stack-portal"></a>Portal do Azure Stack

Se utilizar o portal do Azure Stack para criar um gateway de rede virtual do Resource Manager, pode selecionar o SKU de gateway utilizando a lista pendente. As opções correspondem para o tipo de gateway e o tipo VPN que selecionar.

#### <a name="powershell"></a>PowerShell

O exemplo de PowerShell seguinte especifica os **- GatewaySku** como `VpnGw1`:

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>Tipos de ligação

No modelo de implementação do Resource Manager, cada configuração exige um tipo de ligação de gateway de rede virtual específica. O PowerShell do Gestor de recursos disponíveis, os valores para **- ConnectionType** são:

* IPsec

   No exemplo de PowerShell seguinte, é criada uma ligação de S2S que requer que o tipo de ligação IPsec:

   ```PowerShell
   New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
   -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

### <a name="vpn-types"></a>Tipos de VPN

Ao criar o gateway de rede virtual para uma configuração de gateway VPN, tem de especificar um tipo de VPN. O tipo de VPN que escolher depende da topologia de ligação que pretende criar. Um tipo de VPN também pode confiar no hardware que está a utilizar. Configurações de S2S requerem um dispositivo VPN. Alguns dispositivos VPN só suportam um determinado tipo VPN.

> [!IMPORTANT]  
> Atualmente, o Azure Stack apenas suporta o tipo de VPN de baseado em rota. Se o dispositivo só suporta VPNs de com base em políticas, em seguida, não são suportadas ligações a esses dispositivos a partir do Azure Stack.  
>
> Além disso, Azure Stack não suporta a utilização Seletores de tráfego com base em políticas para Gateways de com base em rota neste momento, porque as configurações da política de IPSec/IKE personalizadas não são suportadas.

* **PolicyBased**: As VPNs baseadas em políticas encriptam e direcionam os pacotes através de túneis IPsec com base nas políticas IPsec configuradas com as combinações de prefixos de endereços entre a rede no local e a VNet do Azure Stack. A política ou o Seletor de tráfego, normalmente, é uma lista de acesso na configuração do dispositivo VPN.

  >[!NOTE]
  >**PolicyBased** é suportado no Azure, mas não no Azure Stack.

* **RouteBased**: RouteBased VPNs utilizar rotas que estão configuradas no reencaminhamento de IP ou na tabela de encaminhamento para direcionar os pacotes para suas interfaces de túnel correspondente. As interfaces de túnel, em seguida, encriptam ou desencriptam os pacotes dentro e fora dos túneis. A política ou o Seletor de tráfego, para **RouteBased** VPNs estão configuradas como qualquer a qualquer (ou utilizar carateres universais). Por predefinição, não pode ser alterados. O valor para um **RouteBased** é do tipo de VPN **RouteBased**.

O exemplo de PowerShell seguinte especifica os **- VpnType** como **RouteBased**. Quando cria um gateway, deve certificar-se de que o **- VpnType** está correto para a sua configuração.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>Requisitos do gateway

A tabela seguinte lista os requisitos para gateways de VPN.

| |Gateway de VPN PolicyBased básico | Gateway de VPN RouteBased básico | Gateway de VPN RouteBased Standard | Gateway de VPN RouteBased alto desempenho|
|--|--|--|--|--|
| **Conectividade de site a Site (S2S conectividade)** | Não suportado | Configuração de RouteBased VPN | Configuração de RouteBased VPN | Configuração de RouteBased VPN |
| **Método de autenticação**  | Não suportado | Chave pré-partilhada para a conetividade S2S  | Chave pré-partilhada para a conetividade S2S  | Chave pré-partilhada para a conetividade S2S  |   
| **Número máximo de ligações S2S**  | Não suportado | 20 | 20| 10|
|**Suporte de encaminhamento ativo (BGP)** | Não suportado | Não suportado | Suportadas | Suportadas |

### <a name="gateway-subnet"></a>Sub-rede do gateway

Antes de criar um gateway de VPN, tem de criar uma sub-rede de gateway. A sub-rede do gateway tem os endereços IP que utilizam os serviços e VMs de gateway de rede virtual. Ao criar o gateway de rede virtual, VMs de gateway são implementadas na sub-rede de gateway e configuradas com as definições do gateway VPN necessárias. Não implementa qualquer outra coisa (por exemplo, VMs adicionais) para a sub-rede do gateway.

>[!IMPORTANT]
>Para funcionar corretamente, a sub-rede do gateway tem de ter o nome **GatewaySubnet**. O Azure Stack utiliza este nome para identificar a sub-rede para implementar o gateway de rede virtual VMs e serviços para.

Quando cria a sub-rede do gateway, especifica o número de endereços IP que a sub-rede contém. Os endereços IP na sub-rede de gateway são alocados para as VMs de gateway e os serviços de gateway. Algumas configurações requerem mais endereços IP do que outras. Veja as instruções para a configuração que pretende criar e certifique-se de que a sub-rede do gateway que pretende criar cumpre os requisitos.

Além disso, certifique-se de que a sub-rede de gateway tem endereços IP suficientes para processar adicionais configurações futuras. Embora seja possível criar uma sub-rede de gateway tão pequena como/29, recomendamos que crie uma sub-rede de gateway de/28 ou superior (/ 28, / 27, / 26 etc.) Dessa forma, se adicionar a funcionalidade no futuro, não precisa que subdividir o gateway, em seguida, eliminar e recriar a sub-rede do gateway para permitir mais endereços IP.

O exemplo de PowerShell do Resource Manager seguinte mostra uma sub-rede de gateway com o nome **GatewaySubnet**. Pode ver que a notação CIDR Especifica/27, que permite endereços IP suficientes na maioria das configurações que existem atualmente.

```PowerShell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Ao trabalhar com sub-redes de gateway, evite associar um grupo de segurança de rede (NSG) às mesmas. Associar um grupo de segurança de rede para esta sub-rede pode fazer com que o gateway de VPN deixe de funcionar conforme esperado. Para obter mais informações sobre os grupos de segurança de rede, consulte [o que é um grupo de segurança de rede?](../../virtual-network/virtual-networks-nsg.md).

### <a name="local-network-gateways"></a>Gateways de rede local

Ao criar uma configuração de gateway VPN no Azure, o gateway de rede local representa, muitas vezes, sua localização no local. No Azure Stack, ele representa qualquer dispositivo VPN remoto que está fora do Azure Stack. Isto pode ser um dispositivo VPN no seu datacenter (ou num datacenter remoto) ou um Gateway de VPN no Azure.

Dê um nome, o endereço IP público do dispositivo VPN, ao gateway de rede local e especificar os prefixos de endereços que estão na localização no local. O Azure analisa os prefixos de endereço de destino para tráfego de rede, consulta a configuração especificada para o seu gateway de rede local e encaminha os pacotes em conformidade.

O seguinte exemplo do PowerShell cria um novo gateway de rede local:

```PowerShell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Às vezes precisa modificar as definições do gateway de rede local; Por exemplo, se adicionar ou modificar o intervalo de endereços, ou se o endereço IP do dispositivo VPN é alterado. Ver [modificar as definições do gateway de rede local com o PowerShell](../../vpn-gateway/vpn-gateway-modify-local-network-gateway.md).

## <a name="ipsecike-parameters"></a>Parâmetros IPsec/IKE

Quando configurar uma ligação VPN no Azure Stack, tem de configurar a ligação em ambas as extremidades. Se estiver a configurar uma ligação VPN entre o Azure Stack e um dispositivo de hardware, como um comutador ou um router que está a agir como um gateway de VPN, que o dispositivo poderá pedir-lhe para as definições adicionais.

Ao contrário do Azure, que suporta várias ofertas como um iniciador e um dispositivo de resposta, o Azure Stack suporta apenas uma oferta.

### <a name="ike-phase-1-main-mode-parameters"></a>Parâmetros de IKE Fase 1 (Modo Principal)

| Propriedade              | Value|
|-|-|
| Versão do IKE           | IKEv2 |
|Grupo Diffie-Hellman   | Grupo 2 (1024 bits) |
| Método de Autenticação | Chave Pré-partilhada |
|Algoritmos de Encriptação e Hash | AES256, SHA256 |
|Duração de SA (Tempo)     | 28 800 segundos|

### <a name="ike-phase-2-quick-mode-parameters"></a>Parâmetros de IKE Fase 2 (Modo Rápido)

| Propriedade| Value|
|-|-|
|Versão do IKE |IKEv2 |
|Encriptação e hash algoritmos (encriptação)     | GCMAES256|
|Encriptação e hash algoritmos (autenticação) | GCMAES256|
|Duração de SA (Tempo)  | 27,000 segundos  |
|Duração de SA (em quilobytes) | 33,553,408     |
|Perfect Forward Secrecy (PFS) |Nenhum<sup>ver nota 1</sup> |
|Deteção de Elemento Inutilizado | Suportadas|  

* *Nota 1:*  Antes da versão 1807, o Azure Stack utiliza um valor de PFS2048 para o perfeito para a frente Secrecy (PFS).

## <a name="next-steps"></a>Passos Seguintes

* [Ligar através do ExpressRoute](../azure-stack-connect-expressroute.md)
