---
title: 'Azure ExpressRoute: Configure S2S VPN sobre olhando a Microsoft'
description: Configure a conectividade IPsec/IKE com o Azure através de um circuito de observação ExpressRoute Microsoft utilizando uma porta VPN site-to-site.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 366f27a0e2a22e9aa10dda20e105bf644255bdd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89393144"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Configure uma VPN site-to-site sobre o ExpressRoute Microsoft olhando

Este artigo ajuda-o a configurar uma conectividade encriptada segura entre a sua rede no local e as suas redes virtuais Azure (VNets) através de uma ligação privada ExpressRoute. Pode utilizar o Microsoft para estabelecer um túnel VPN IPsec/IKE local entre as suas redes de televisão selecionadas e VNets Azure. Configurar um túnel seguro sobre o ExpressRoute permite a troca de dados com confidencialidade, anti-repetição, autenticidade e integridade.

>[!NOTE]
>Quando configurar a VPN site-to-site sobre o espreitamento da Microsoft, é cobrado para o gateway VPN e saída VPN. Para mais informações, consulte [os preços da VPN Gateway.](https://azure.microsoft.com/pricing/details/vpn-gateway)
>
>

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="architecture"></a><a name="architecture"></a>Arquitetura


  ![Visão geral da conectividade](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


Para uma elevada disponibilidade e redundância, pode configurar vários túneis sobre os dois pares MSEE-PE de um circuito ExpressRoute e permitir o equilíbrio de carga entre os túneis.

  ![opções de alta disponibilidade](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

Os túneis VPN sobre o espreitamento da Microsoft podem ser encerrados através do gateway VPN ou utilizando um aparelho virtual de rede apropriado (NVA) disponível através do Azure Marketplace. Pode trocar rotas estática ou dinamicamente pelos túneis encriptados sem expor a troca de rotas para o perspitamento subjacente da Microsoft. Nos exemplos deste artigo, o BGP (diferente da sessão de BGP utilizada para criar o estornudo microsoft) é usado para trocar dinâmicamente prefixos sobre os túneis encriptados.

>[!IMPORTANT]
>Para o lado do local, normalmente o espreitamento da Microsoft é terminado no DMZ e o espreitamento privado é terminado na zona de rede principal. As duas zonas seriam segregadas usando firewalls. Se estiver a configurar a Microsoft a espreitar exclusivamente para permitir um túnel seguro sobre o ExpressRoute, lembre-se de filtrar apenas através dos IPs de interesse público que estão a ser anunciados através do espreitamento da Microsoft.
>
>

## <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

1. Configure a Microsoft a espreitar para o seu circuito ExpressRoute.
2. Anuncie prefixos públicos regionais selecionados da Azure para a sua rede de instalações através do seu olhar microsoft.
3. Configure uma porta de entrada VPN e estabeleça túneis IPsec
4. Configure o dispositivo VPN no local.
5. Crie a ligação site-to-site IPsec/IKE.
6. (Opcional) Configure as firewalls/filtragem no dispositivo VPN no local.
7. Teste e valide a comunicação IPsec sobre o circuito ExpressRoute.

## <a name="1-configure-microsoft-peering"></a><a name="peering"></a>1. Configurar o espreitamento da Microsoft

Para configurar uma ligação VPN site-to-site sobre o ExpressRoute, deve aproveitar o olho da Microsoft ExpressRoute.

* Para configurar um novo circuito ExpressRoute, comece com o artigo [pré-requisitos ExpressRoute](expressroute-prerequisites.md) e, em seguida, [Crie e modifique um circuito ExpressRoute](expressroute-howto-circuit-arm.md).

* Se já tem um circuito ExpressRoute, mas não tem a Microsoft a espreitar configurada, configurar a Microsoft a espreitar utilizando o Create e modificar o [espreitamento para um artigo de circuito ExpressRoute.](expressroute-howto-routing-arm.md#msft)

Uma vez configurado o seu circuito e o seu espreitamento da Microsoft, pode facilmente vê-lo usando a página **'Vista Geral'** no portal Azure.

![circuito](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="2-configure-route-filters"></a><a name="routefilter"></a>2. Filtros de rota de configuração

Um filtro de rota permite-lhe identificar os serviços que deseja consumir através do peering da Microsoft do circuito do ExpressRoute. É essencialmente uma lista de todos os valores comunitários da BGP. 

![filtro de rota](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

Neste exemplo, a implantação está apenas na região *Azure West US 2.* Uma regra de filtro de rota é adicionada para permitir apenas a publicidade de prefixos regionais Azure West US 2, que tem o valor comunitário BGP *12076:51026*. Especifica os prefixos regionais que pretende permitir selecionando a **regra De gerir**.

Dentro do filtro de rota, também é necessário escolher os circuitos ExpressRoute para os quais se aplica o filtro de rota. Pode escolher os circuitos ExpressRoute selecionando **o circuito Add**. Na figura anterior, o filtro de rota está associado ao circuito ExpressRoute exemplo.

### <a name="21-configure-the-route-filter"></a><a name="configfilter"></a>2.1 Configurar o filtro de rota

Configure um filtro de rota. Para obter passos, consulte [filtros de rota de configuração para o microsoft espreitar](how-to-routefilter-portal.md).

### <a name="22-verify-bgp-routes"></a><a name="verifybgp"></a>2.2 Verificar rotas de BGP

Depois de ter criado com sucesso a Microsoft a espreitar o circuito ExpressRoute e ter associado um filtro de rota com o circuito, pode verificar as rotas BGP recebidas dos MSEEs nos dispositivos PE que estão a espreitar com os MSEEs. O comando de verificação varia, dependendo do sistema operativo dos seus dispositivos PE.

#### <a name="cisco-examples"></a>Exemplos de Cisco

Este exemplo utiliza um comando Cisco IOS-XE. No exemplo, é utilizado um caso de encaminhamento e encaminhamento virtual (VRF) para isolar o tráfego de observação.

```
show ip bgp vpnv4 vrf 10 summary
```

A seguinte saída parcial mostra que 68 prefixos foram recebidos do vizinho \* .243.229.34 com o ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Para ver a lista de prefixos recebidos do vizinho, use o seguinte exemplo:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Para confirmar que está a receber o conjunto correto de prefixos, pode verificar. A seguinte saída de comando Azure PowerShell lista os prefixos anunciados através da Microsoft, olhando para cada um dos serviços e para cada uma das regiões Azure:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="3-configure-the-vpn-gateway-and-ipsec-tunnels"></a><a name="vpngateway"></a>3. Configurar o portal VPN e os túneis IPsec

Nesta secção, os túneis VPN IPsec são criados entre o gateway Azure VPN e o dispositivo VPN no local. Os exemplos utilizam dispositivos VPN Cisco Cloud Service Router (CSR1000).

O diagrama seguinte mostra os túneis VPN IPsec estabelecidos entre o dispositivo VPN no local 1 e o par de instâncias de gateway Azure VPN. Os dois túneis VPN IPsec estabelecidos entre o dispositivo VPN 2 no local e o par de instâncias de gateway VPN Azure não estão ilustrados no diagrama, e os detalhes de configuração não estão listados. No entanto, ter túneis VPN adicionais melhora a elevada disponibilidade.

  ![Túneis VPN](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Sobre o par do túnel IPsec, é estabelecida uma sessão eBGP para trocar rotas de rede privada. O seguinte diagrama mostra a sessão eBGP estabelecida sobre o par do túnel IPsec:

  ![Sessões eBGP sobre par de túneis](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

O diagrama a seguir mostra a visão geral abstrata da rede de exemplos:

  ![rede exemplo](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Sobre os exemplos do modelo do Gestor de Recursos Azure

Nos exemplos, o gateway VPN e as terminações do túnel IPsec são configurados usando um modelo de Gestor de Recursos Azure. Se você é novo para usar modelos de Gestor de Recursos, ou para entender o básico do modelo de Gestor de Recursos, consulte [a estrutura e sintaxe dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/template-syntax.md). O modelo nesta secção cria um ambiente azul de campo verde (VNet). No entanto, se tiver um VNet existente, pode fazê-lo referenciar no modelo. Se não estiver familiarizado com as configurações site-to-site do gateway VPN/IKE, consulte [Criar uma ligação site-to-site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Não precisa de utilizar os modelos do Gestor de Recursos Azure para criar esta configuração. Pode criar esta configuração utilizando o portal Azure ou PowerShell.
>
>

### <a name="31-declare-the-variables"></a><a name="variables3"></a>3.1 Declarar as variáveis

Neste exemplo, as declarações variáveis correspondem à rede de exemplo. Ao declarar variáveis, modifique esta secção para refletir o seu ambiente.

* A **variável localAddressPrefix** é uma série de endereços IP no local para terminar os túneis IPsec.
* O **gatewaySku** determina a produção VPN. Para obter mais informações sobre gatewaySku e vpnType, consulte [as definições de configuração do Gateway VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Para obter preços, consulte [os preços da VPN Gateway.](https://azure.microsoft.com/pricing/details/vpn-gateway)
* Desa estabamente o **VPNType** para **RouteBased**.

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

### <a name="32-create-virtual-network-vnet"></a><a name="vnet"></a>3.2 Criar rede virtual (VNet)

Se estiver a associar um VNet existente com os túneis VPN, pode saltar este passo.

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

### <a name="33-assign-public-ip-addresses-to-vpn-gateway-instances"></a><a name="ip"></a>3.3 Atribuir endereços IP públicos a instâncias de gateway VPN
 
Atribua um endereço IP público para cada instância de uma porta de entrada VPN.

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

### <a name="34-specify-the-on-premises-vpn-tunnel-termination-local-network-gateway"></a><a name="termination"></a>3.4 Especificar a terminação do túnel VPN no local (porta de entrada de rede local)

Os dispositivos VPN no local são referidos como o **portal de rede local**. O seguinte json snippet também especifica detalhes remotos do par BGP:

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

### <a name="35-create-the-vpn-gateway"></a><a name="creategw"></a>3.5 Criar o portal VPN

Esta secção do modelo configura o gateway VPN com as definições necessárias para uma configuração ativa. Tenha em mente os seguintes requisitos:

* Crie o gateway VPN com um VpnType **"RouteBased".** Esta definição é obrigatória se pretender ativar o encaminhamento BGP entre o gateway VPN e o VPN no local.
* Para estabelecer túneis VPN entre as duas instâncias do gateway VPN e um determinado dispositivo no local em modo ativo, o parâmetro **"ActiveActive"** é definido como **verdadeiro** no modelo de Gestor de Recursos. Para obter mais informações sobre gateways VPN altamente disponíveis, consulte [a conectividade de gateway VPN altamente disponível.](../vpn-gateway/vpn-gateway-highlyavailable.md)
* Para configurar as sessões de eBGP entre os túneis VPN, deve especificar duas ASNs diferentes de cada lado. É preferível especificar números ASN privados. Para mais informações, consulte [a visão geral das portas BGP e Azure VPN](../vpn-gateway/vpn-gateway-bgp-overview.md).

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

### <a name="36-establish-the-ipsec-tunnels"></a><a name="ipsectunnel"></a>3.6 Estabelecer os túneis IPsec

A ação final do script cria túneis IPsec entre o gateway Azure VPN e o dispositivo VPN no local.

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

## <a name="4-configure-the-on-premises-vpn-device"></a><a name="device"></a>4. Configurar o dispositivo VPN no local

O gateway Azure VPN é compatível com muitos dispositivos VPN de diferentes fornecedores. Para obter informações de configuração e dispositivos que tenham sido validados para trabalhar com gateway VPN, consulte [sobre dispositivos VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

Ao configurar o seu dispositivo VPN, necessita dos seguintes itens:

* Uma chave partilhada. Esta é a mesma chave partilhada que especifica ao criar a sua ligação VPN site-to-site. Os exemplos usam uma chave partilhada básica. Deve gerar uma chave mais complexa para utilizar.
* O endereço IP público do seu gateway VPN. Pode ver o endereço IP público através do portal do Azure, do PowerShell ou da CLI. Para encontrar o endereço IP Público do seu gateway de VPN através do portal do Azure, navegue para Gateways de rede virtual e, em seguida, clique no nome do gateway.

Normalmente, os pares eBGP estão ligados diretamente (muitas vezes sobre uma ligação WAN). No entanto, quando está a configurar o eBGP sobre os túneis VPN IPsec através do expressRoute Microsoft, existem vários domínios de encaminhamento entre os pares eBGP. Use o comando **ebgp-multihop** para estabelecer a relação de vizinho eBGP entre os dois pares não diretamente ligados. O número inteiro que segue o comando ebgp-multihop especifica o valor TTL nos pacotes BGP. O comando **de vias máximas eibgp 2** permite o equilíbrio de carga do tráfego entre os dois caminhos BGP.

### <a name="cisco-csr1000-example"></a><a name="cisco1"></a>Exemplo cisco CSR1000

O exemplo a seguir mostra a configuração para Cisco CSR1000 numa máquina virtual Hyper-V como o dispositivo VPN no local:

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

## <a name="5-configure-vpn-device-filtering-and-firewalls-optional"></a><a name="firewalls"></a>5. Filtragem e firewalls do dispositivo VPN configurado (opcional)

Configure a sua firewall e a filtragem de acordo com os seus requisitos.

## <a name="6-test-and-validate-the-ipsec-tunnel"></a><a name="testipsec"></a>6. Testar e validar o túnel IPsec

O estado dos túneis IPsec pode ser verificado no gateway Azure VPN pelos comandos Powershell:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Exemplo de saída:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Para verificar o estado dos túneis nas instâncias de gateway Azure VPN de forma independente, utilize o seguinte exemplo:

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

Também pode verificar o estado do túnel no seu dispositivo VPN no local.

Exemplo cisco CSR1000:

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

O protocolo de linha na Interface do Túnel Virtual (VTI) não muda para "up" até que a fase 2 do IKE esteja concluída. O seguinte comando verifica a associação de segurança:

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

### <a name="verify-end-to-end-connectivity-between-the-inside-network-on-premises-and-the-azure-vnet"></a><a name="verifye2e"></a>Verifique a conectividade de ponta a ponta entre a rede interna no local e o Azure VNet

Se os túneis IPsec estiverem prontos e as rotas estáticas estiverem corretamente definidas, deverá ser capaz de verificar o endereço IP do remoto par BGP:

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

### <a name="verify-the-bgp-sessions-over-ipsec"></a><a name="verifybgp"></a>Verifique as sessões de BGP sobre o IPsec

No gateway Azure VPN, verifique o estado do par BGP:

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

Para verificar a lista de prefixos de rede recebidos via eBGP a partir do concentrador VPN no local, pode filtrar por atributo "Origem":

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

No exemplo, o ASN 65010 é o número do sistema autónomo BGP no local de VPN.

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

Exemplo para os locais Cisco CSR1000:

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

A lista de redes anunciadas a partir das instalações cisco CSR1000 até ao gateway Azure VPN pode ser listada usando o seguinte comando:

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

* [Adicione uma ligação site-a-local a um VNet com uma ligação de gateway VPN existente](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
