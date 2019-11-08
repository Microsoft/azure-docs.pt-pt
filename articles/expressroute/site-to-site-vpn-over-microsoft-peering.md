---
title: Configurar uma VPN site a site por meio do emparelhamento da Microsoft-ExpressRoute-Azure | Microsoft Docs
description: Configure a conectividade de IPsec/IKE com o Azure por meio de um circuito de emparelhamento da Microsoft do ExpressRoute usando um gateway de VPN site a site.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: d26210ab226f8e907aa845d51dca94f59badd6a3
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748064"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Configurar uma VPN site a site em um emparelhamento da Microsoft de ExpressRoute

Este artigo ajuda você a configurar a conectividade criptografada segura entre sua rede local e suas VNets (redes virtuais do Azure) em uma conexão privada do ExpressRoute. Você pode usar o emparelhamento da Microsoft para estabelecer um túnel de VPN IPsec/IKE site a site entre suas redes locais selecionadas e o Azure VNets. A configuração de um túnel seguro no ExpressRoute permite a troca de dados com confidencialidade, anti-repetição, autenticidade e integridade.

>[!NOTE]
>Quando você configura a VPN site a site sobre o emparelhamento da Microsoft, você é cobrado pelo gateway de VPN e saída de VPN. Para obter mais informações, consulte [preços do gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="architecture"></a>Arquitectura


  ![Visão geral da conectividade](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


Para alta disponibilidade e redundância, você pode configurar vários túneis nos dois pares MSEE-PE de um circuito do ExpressRoute e habilitar o balanceamento de carga entre os túneis.

  ![opções de alta disponibilidade](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

Os túneis VPN via emparelhamento da Microsoft podem ser encerrados usando o gateway de VPN ou usando uma solução de virtualização de rede (NVA) apropriada disponível por meio do Azure Marketplace. Você pode trocar rotas de forma estática ou dinâmica sobre os túneis criptografados sem expor a troca de rota para o emparelhamento da Microsoft subjacente. Nos exemplos deste artigo, o BGP (diferente da sessão BGP usado para criar o emparelhamento da Microsoft) é usado para trocar dinamicamente prefixos nos túneis criptografados.

>[!IMPORTANT]
>Para o lado local, normalmente o emparelhamento da Microsoft é encerrado na DMZ e o emparelhamento privado é encerrado na zona de rede principal. As duas zonas seriam segregas usando firewalls. Se você estiver configurando o emparelhamento da Microsoft exclusivamente para habilitar o túnel seguro no ExpressRoute, lembre-se de filtrar apenas os IPs públicos de interesse que estão sendo anunciados por meio do emparelhamento da Microsoft.
>
>

## <a name="workflow"></a>Modelo

1. Configure o emparelhamento da Microsoft para o circuito do ExpressRoute.
2. Anuncie prefixos públicos do Azure selecionados à sua rede local por meio do emparelhamento da Microsoft.
3. Configurar um gateway de VPN e estabelecer túneis IPsec
4. Configure o dispositivo VPN local.
5. Crie a conexão IPsec/IKE site a site.
6. Adicional Configure firewalls/filtragem no dispositivo VPN local.
7. Teste e valide a comunicação IPsec no circuito do ExpressRoute.

## <a name="peering"></a>1. configurar o emparelhamento da Microsoft

Para configurar uma conexão VPN site a site no ExpressRoute, você deve aproveitar o emparelhamento da Microsoft do ExpressRoute.

* Para configurar um novo circuito do ExpressRoute, comece com o artigo [pré-requisitos do expressroute](expressroute-prerequisites.md) e, em seguida, [crie e modifique um circuito do expressroute](expressroute-howto-circuit-arm.md).

* Se você já tiver um circuito do ExpressRoute, mas não tiver o emparelhamento da Microsoft configurado, configure o emparelhamento da Microsoft usando o artigo [criar e modificar o emparelhamento para um circuito do ExpressRoute](expressroute-howto-routing-arm.md#msft) .

Depois de configurar o circuito e o emparelhamento da Microsoft, você poderá exibi-lo facilmente usando a página **visão geral** no portal do Azure.

![elétrico](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="routefilter"></a>2. configurar filtros de rota

Um filtro de rota permite-lhe identificar os serviços que deseja consumir através do peering da Microsoft do circuito do ExpressRoute. É essencialmente uma lista de permissões de todos os valores de comunidade BGP. 

![filtro de rota](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

Neste exemplo, a implantação só está na região *do Azure oeste dos EUA 2* . Uma regra de filtro de rota é adicionada para permitir apenas o anúncio de prefixos regionais do Azure oeste dos EUA 2, que tem o valor de comunidade BGP *12076:51026*. Você especifica os prefixos regionais que deseja permitir selecionando **gerenciar regra**.

Dentro do filtro de rota, você também precisa escolher os circuitos de ExpressRoute para os quais o filtro de rota se aplica. Você pode escolher os circuitos do ExpressRoute selecionando **Adicionar circuito**. Na figura anterior, o filtro de rota está associado ao circuito de ExpressRoute de exemplo.

### <a name="configfilter"></a>2,1 configurar o filtro de rota

Configurar um filtro de rota. Para obter as etapas, consulte [Configurar filtros de rota para o emparelhamento da Microsoft](how-to-routefilter-portal.md).

### <a name="verifybgp"></a>2,2 verificar rotas BGP

Depois de ter criado com êxito o emparelhamento da Microsoft em seu circuito do ExpressRoute e associado a um filtro de rota com o circuito, você pode verificar as rotas BGP recebidas do MSEEs nos dispositivos PE que estão emparelhando com o MSEEs. O comando de verificação varia, dependendo do sistema operacional de seus dispositivos PE.

#### <a name="cisco-examples"></a>Exemplos da Cisco

Este exemplo usa um comando Cisco IOS-XE. No exemplo, uma instância de roteamento virtual e encaminhamento (VRF) é usada para isolar o tráfego de emparelhamento.

```
show ip bgp vpnv4 vrf 10 summary
```

A saída parcial a seguir mostra que os prefixos 68 foram recebidos do vizinho \*. 243.229.34 com o ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Para ver a lista de prefixos recebidos do vizinho, use o seguinte exemplo:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Para confirmar que você está recebendo o conjunto correto de prefixos, você pode fazer uma verificação cruzada. O Azure PowerShell saída de comando a seguir lista os prefixos anunciados por meio do emparelhamento da Microsoft para cada um dos serviços e para cada uma das regiões do Azure:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="vpngateway"></a>3. configurar o gateway de VPN e os túneis IPsec

Nesta seção, os túneis de VPN IPsec são criados entre o gateway de VPN do Azure e o dispositivo VPN local. Os exemplos usam dispositivos VPN do roteador do serviço de nuvem da Cisco (CSR1000).

O diagrama a seguir mostra os túneis de VPN IPsec estabelecidos entre o dispositivo VPN local 1 e o par de instâncias de gateway de VPN do Azure. Os dois túneis de VPN IPsec estabelecidos entre o dispositivo VPN local 2 e o par de instâncias de gateway de VPN do Azure não são ilustrados no diagrama, e os detalhes de configuração não são listados. No entanto, ter túneis VPN adicionais melhora a alta disponibilidade.

  ![Túneis de VPN](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Sobre o par de túnel IPsec, uma sessão eBGP é estabelecida para trocar rotas de rede privada. O diagrama a seguir mostra a sessão eBGP estabelecida sobre o par de túnel IPsec:

  ![sessões eBGP sobre par de encapsulamento](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

O diagrama a seguir mostra a visão geral abstrata da rede de exemplo:

  ![rede de exemplo](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Sobre os exemplos de modelo de Azure Resource Manager

Nos exemplos, o gateway de VPN e as terminações de túnel IPsec são configuradas usando um modelo de Azure Resource Manager. Se você for novo no uso de modelos do Resource Manager ou para entender os conceitos básicos do modelo do Resource Manager, consulte [entender a estrutura e a sintaxe dos modelos de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). O modelo nesta seção cria um ambiente Greenfield (VNet) do Azure. No entanto, se você tiver uma VNet existente, poderá referenciá-la no modelo. Se você não estiver familiarizado com as configurações site a site de IPsec/IKE do gateway de VPN, consulte [criar uma conexão site a site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Você não precisa usar modelos de Azure Resource Manager para criar essa configuração. Você pode criar essa configuração usando o portal do Azure ou o PowerShell.
>
>

### <a name="variables3"></a>3,1 declarar as variáveis

Neste exemplo, as declarações de variável correspondem à rede de exemplo. Ao declarar variáveis, modifique esta seção para refletir seu ambiente.

* A variável **localAddressPrefix** é uma matriz de endereços IP locais para encerrar os túneis IPsec.
* O **gatewaySku** determina a taxa de transferência de VPN. Para obter mais informações sobre gatewaySku e vpnType, consulte [definições de configuração do gateway de VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Para obter os preços, consulte [preços do gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway).
* Defina **vpnType** como **RouteBased**.

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premises configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="vnet"></a>3,2 criar rede virtual (VNet)

Se você estiver associando uma VNet existente com os túneis de VPN, poderá ignorar esta etapa.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="ip"></a>3,3 atribuir endereços IP públicos a instâncias de gateway de VPN
 
Atribua um endereço IP público para cada instância de um gateway de VPN.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="termination"></a>3,4 especificar o encerramento do túnel VPN local (gateway de rede local)

Os dispositivos VPN locais são chamados de **Gateway de rede local**. O trecho JSON a seguir também especifica os detalhes do par BGP remoto:

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="creategw"></a>3,5 criar o gateway de VPN

Esta seção do modelo configura o gateway de VPN com as configurações necessárias para uma configuração ativa-ativa. Tenha em mente os seguintes requisitos:

* Crie o gateway de VPN com um VpnType **"RouteBased"** . Essa configuração será obrigatória se você quiser habilitar o roteamento BGP entre o gateway de VPN e a VPN local.
* Para estabelecer túneis de VPN entre as duas instâncias do gateway de VPN e um determinado dispositivo local no modo ativo-ativo, o parâmetro **"activeActive"** é definido como **true** no modelo do Resource Manager. Para entender mais sobre gateways de VPN altamente disponíveis, consulte [conectividade de gateway de VPN altamente disponível](../vpn-gateway/vpn-gateway-highlyavailable.md).
* Para configurar sessões eBGP entre os túneis de VPN, você deve especificar dois ASNs diferentes em ambos os lados. É preferível especificar números ASN privados. Para obter mais informações, consulte [visão geral do BGP e gateways de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="ipsectunnel"></a>3,6 estabelecer os túneis IPsec

A ação final do script cria túneis IPsec entre o gateway de VPN do Azure e o dispositivo VPN local.

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="device"></a>4. configurar o dispositivo VPN local

O gateway de VPN do Azure é compatível com vários dispositivos VPN de diferentes fornecedores. Para obter informações de configuração e dispositivos que foram validados para funcionar com o gateway de VPN, consulte [sobre dispositivos VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

Ao configurar seu dispositivo VPN, você precisará dos seguintes itens:

* Uma chave partilhada. Essa é a mesma chave compartilhada que você especifica ao criar a conexão VPN site a site. Os exemplos usam uma chave compartilhada básica. Deve gerar uma chave mais complexa para utilizar.
* O endereço IP público do seu gateway de VPN. Pode ver o endereço IP público através do portal do Azure, do PowerShell ou da CLI. Para localizar o endereço IP público do seu gateway de VPN usando o portal do Azure, navegue até gateways de rede virtual e clique no nome do seu gateway.

Normalmente, os pares eBGP são conectados diretamente (geralmente por meio de uma conexão WAN). No entanto, quando você estiver configurando o eBGP em túneis de VPN IPsec via emparelhamento da Microsoft do ExpressRoute, haverá vários domínios de roteamento entre os pares eBGP. Use o comando **eBGP-multihop** para estabelecer a relação de vizinho do eBGP entre os dois pares conectados não diretamente. O inteiro que segue o comando eBGP-multihop especifica o valor de TTL nos pacotes BGP. O comando **Maximum-Paths eiBGP 2** permite o balanceamento de carga de tráfego entre os dois caminhos BGP.

### <a name="cisco1"></a>Exemplo do Cisco CSR1000

O exemplo a seguir mostra a configuração do Cisco CSR1000 em uma máquina virtual do Hyper-V como o dispositivo VPN local:

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="firewalls"></a>5. configurar firewalls e filtragem de dispositivo VPN (opcional)

Configure o firewall e a filtragem de acordo com suas necessidades.

## <a name="testipsec"></a>6. testar e validar o túnel IPsec

O status dos túneis IPsec pode ser verificado no gateway de VPN do Azure por comandos do PowerShell:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Exemplo de saída:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Para verificar o status dos túneis nas instâncias de gateway de VPN do Azure de forma independente, use o exemplo a seguir:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Exemplo de saída:

```azurepowershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

Você também pode verificar o status do túnel em seu dispositivo VPN local.

Exemplo do Cisco CSR1000:

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

Exemplo de saída:

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

O protocolo de linha na interface de túnel virtual (VTI) não muda para "up" até que a fase 2 do IKE seja concluída. O comando a seguir verifica a associação de segurança:

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verifye2e"></a>Verificar a conectividade de ponta a ponta entre a rede interna local e a VNet do Azure

Se os túneis IPsec estiverem ativos e as rotas estáticas estiverem definidas corretamente, você deverá ser capaz de executar ping no endereço IP do par de BGP remoto:

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verifybgp"></a>Verificar as sessões BGP no IPsec

No gateway de VPN do Azure, verifique o status do par de BGP:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Exemplo de saída:

```azurepowershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

Para verificar a lista de prefixos de rede recebidos por meio do eBGP do concentrador de VPN local, você pode filtrar por "origem" do atributo:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

Na saída de exemplo, o ASN 65010 é o número do sistema autônomo de BGP na VPN local.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Para ver a lista de rotas anunciadas:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Exemplo de saída:

```azurepowershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Exemplo para o Cisco CSR1000 local:

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

A lista de redes anunciadas do Cisco CSR1000 local para o gateway de VPN do Azure pode ser listada usando o seguinte comando:

```
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>Passos seguintes

* [Configurar o Monitor de Desempenho de Rede para o ExpressRoute](how-to-npm.md)

* [Adicionar uma conexão site a site a uma VNet com uma conexão de gateway de VPN existente](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
