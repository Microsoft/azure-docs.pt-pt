---
title: 'Encriptação Configure ExpressRoute: IPsec sobre ExpressRoute para O WAN Virtual Azure'
description: Neste tutorial, aprenda a usar o Azure Virtual WAN para criar uma ligação VPN site-to-site sobre o peering privado ExpressRoute.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: b1e6305d142530ab19849f61f12a122d0c6434aa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80059307"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>Encriptação ExpressRoute: IPsec sobre ExpressRoute para Wan Virtual

Este artigo mostra-lhe como usar o Azure Virtual WAN para estabelecer uma ligação IPsec/IKE VPN da sua rede no local até Azure sobre o epeering privado de um circuito Azure ExpressRoute. Esta técnica pode fornecer um trânsito encriptado entre as redes no local e as redes virtuais Azure através do ExpressRoute, sem passar pela internet pública ou utilizar endereços IP públicos.

## <a name="topology-and-routing"></a>Topologia e encaminhamento

O diagrama que se segue mostra um exemplo de conectividade VPN sobre o peering privado ExpressRoute:

![VPN sobre ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

O diagrama mostra uma rede dentro da rede no local ligada ao gateway VPN do hub Azure sobre o peering privado ExpressRoute. O estabelecimento de conectividade é simples:

1. Estabeleça a conectividade ExpressRoute com um circuito ExpressRoute e um peering privado.
2. Estabeleça a conectividade VPN como descrito neste artigo.

Um aspeto importante desta configuração é o encaminhamento entre as redes no local e o Azure sobre os caminhos ExpressRoute e VPN.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Tráfego das redes no local para Azure

Para o tráfego das redes no local para o Azure, os prefixos Azure (incluindo o hub virtual e todas as redes virtuais ligadas ao hub) são anunciados através do ExpressRoute private peering BGP e do VPN BGP. Isto resulta em duas rotas de rede (caminhos) em direção ao Azure a partir das redes no local:

- Um sobre o caminho protegido pelo IPsec
- Um diretamente sobre a ExpressRoute *sem* proteção IPsec 

Para aplicar a encriptação à comunicação, deve certificar-se de que, para a rede ligada à VPN no diagrama, as rotas Azure através do gateway VPN no local são preferidas sobre o caminho direto da ExpressRoute.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Tráfego de Azure para redes no local

O mesmo requisito aplica-se ao tráfego do Azure para as redes no local. Para garantir que o caminho do IPsec é preferido sobre o caminho direct ExpressRoute (sem IPsec), tem duas opções:

- Anuncie prefixos mais específicos na sessão VPN BGP para a rede vpn conectada. Pode anunciar uma gama maior que engloba a rede ligada à VPN através do peering privado ExpressRoute, e depois gamas mais específicas na sessão vpn BGP. Por exemplo, anuncie 10.0.0.0.0/16 sobre a ExpressRoute e 10.0.1.0/24 sobre VPN.

- Anuncie prefixos disconjuntos para VPN e ExpressRoute. Se as gamas de rede ligadas à VPN forem disarticuladas de outras redes ligadas à ExpressRoute, pode anunciar os prefixos nas sessões VPN e ExpressRoute BGP, respectivamente. Por exemplo, anuncie 10.0.0.0/24 sobre expressRoute, e 10.0.1.0/24 sobre VPN.

Em ambos os exemplos, o Azure enviará o tráfego para 10.0.1.0/24 sobre a ligação VPN em vez de diretamente sobre a ExpressRoute sem proteção VPN.

> [!WARNING]
> Se anunciar os *mesmos* prefixos sobre as ligações ExpressRoute e VPN, o Azure utilizará o caminho ExpressRoute diretamente sem proteção VPN.
>

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. Criar um WAN virtual e um hub com gateways

Os seguintes recursos Azure e as configurações correspondentes no local devem estar em vigor antes de proceder:

- Um WAN virtual Azure
- Um centro virtual WAN com um [gateway ExpressRoute](virtual-wan-expressroute-portal.md) e um [gateway VPN](virtual-wan-site-to-site-portal.md)

Para os passos para criar um Wan virtual Azure e um hub com uma associação ExpressRoute, consulte [Create a ExpressRoute association usando O Wan Virtual Azure](virtual-wan-expressroute-portal.md). Para os passos para criar um gateway VPN no WAN virtual, consulte [Criar uma ligação site-to-site usando O WAN Virtual Azure](virtual-wan-site-to-site-portal.md).

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. Criar um site para a rede no local

O recurso do site é o mesmo que os sites VPN não ExpressRoute para um WAN virtual. O endereço IP do dispositivo VPN no local pode agora ser um endereço IP privado, ou um endereço IP público na rede no local acessível através do público de acesso privado criado no passo 1.

> [!NOTE]
> O endereço IP do dispositivo VPN no local *deve* fazer parte dos prefixos de endereço anunciados para o centro virtual WAN via pinvo privado Azure ExpressRoute.
>

1. Vá ao portal Azure no seu navegador. 
1. Selecione o WAN que criou. Na página WAN, em **Conectividade,** selecione **sites VPN**.
1. Na página dos **sites vpN,** selecione **+Criar site**.
1. Na página **Criar site**, preencha os seguintes campos:
   * **Subscrição**: Verifique a subscrição.
   * **Grupo de Recursos**: Selecione ou crie o grupo de recursos que pretende utilizar.
   * **Região**: Entre na região de Azure para o recurso do site VPN.
   * **Nome**: Insira o nome pelo qual pretende consultar o seu site no local.
   * Fornecedor de **dispositivos**: Introduza o fornecedor do dispositivo VPN no local.
   * **Protocolo border gateway**: Selecione "Ativar" se a sua rede no local utilizar BGP.
   * Espaço de **endereços privados**: Introduza o espaço de endereçoIP localizado no seu local no local. O tráfego destinado a este espaço de endereço é encaminhado para a rede no local através do gateway VPN.
   * **Hubs**: Selecione um ou mais hubs para ligar este site VPN. Os centros selecionados devem ter gateways VPN já criados.
1. Selecione **Seguinte: Links >** para as definições de ligação VPN:
   * **Nome do link**: O nome pelo qual pretende consultar esta ligação.
   * **Nome do fornecedor**: O nome do fornecedor de serviços de internet para este site. Para uma rede expressRoute on-local, é o nome do prestador de serviços ExpressRoute.
   * **Velocidade**: A velocidade do link do serviço de internet ou do circuito ExpressRoute.
   * **Endereço IP**: O endereço IP público do dispositivo VPN que reside no seu site no local. Ou, para o ExpressRoute no local, é o endereço IP privado do dispositivo VPN via ExpressRoute.

   Se o BGP estiver ativado, aplicar-se-á a todas as ligações criadas para este site em Azure. Configurar o BGP num WAN virtual equivale a configurar o BGP num gateway Azure VPN. 
   
   O seu endereço de pares BGP no local *não deve* ser o mesmo que o endereço IP da sua VPN para o dispositivo ou o espaço de endereço de rede virtual do site VPN. Utilize um endereço IP diferente no dispositivo VPN para o seu IP de pares BGP. Pode ser um endereço atribuído à interface de loopback no dispositivo. No entanto, *não pode* ser um APIPA (169.254).* x*. *x*) endereço. Especifique este endereço no gateway de rede local correspondente que representa a localização. Para pré-requisitos de BGP, consulte [sobre bGP com Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

1. Selecione **Seguinte: Rever + criar >** para verificar os valores de definição e criar o site VPN. Se selecionar **Hubs** para ligar, a ligação será estabelecida entre a rede no local e o gateway VPN do hub.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. Atualize a definição de ligação VPN para utilizar a ExpressRoute

Depois de criar o site VPN e ligar-se ao centro, utilize os seguintes passos para configurar a ligação para utilizar o peering privado ExpressRoute:

1. Volte para a página virtual de recursos WAN e selecione o recurso hub. Ou navegar do site vpn para o centro conectado.
1. Em **Conectividade,** selecione **VPN (Site-a-Site)**.
1. Selecione a elipse **(...**) no site vpN através do ExpressRoute e selecione **a ligação VPN de Edição a este hub**.
1. Para **utilizar o endereço IP privado Azure,** selecione **Sim**. A definição configura o portal VPN do hub para utilizar endereços IP privados dentro da gama de endereços do hub na porta de entrada para esta ligação, em vez dos endereços IP públicos. Isto garantirá que o tráfego da rede no local atravessa os caminhos de observação privada expressRoute em vez de usar a internet pública para esta ligação VPN. A imagem seguinte mostra a definição.

   ![Definição para utilização de um endereço IP privado para a ligação VPN](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
1. Selecione **Guardar**.

Depois de guardar as suas alterações, o gateway VPN do hub utilizará os endereços IP privados na porta de entrada VPN para estabelecer as ligações IPsec/IKE com o dispositivo VPN no local através do ExpressRoute.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. Obtenha os endereços IP privados para o gateway VPN do hub

Descarregue a configuração do dispositivo VPN para obter os endereços IP privados do gateway VPN do hub. Precisa destes endereços para configurar o dispositivo VPN no local.

1. Na página do seu hub, selecione **VPN (Site-a-Site)** em **Conectividade**.
1. No topo da página **'Overview',** selecione **Baixar VPN Config**. 

   O Azure cria uma conta de armazenamento no grupo de recursos "microsoft-network-[localização]", onde a *localização* é a localização do WAN. Depois de aplicar a configuração nos seus dispositivos VPN, pode eliminar esta conta de armazenamento.
1. Depois de criado o ficheiro, selecione o link para o descarregar.
1. Aplique a configuração ao dispositivo VPN.

### <a name="vpn-device-configuration-file"></a>Ficheiro de configuração do dispositivo VPN

O ficheiro de configuração do dispositivo contém as definições a utilizar quando estiver a configurar o seu dispositivo VPN no local. Quando vir este ficheiro, repare nas informações seguintes:

* **vpnSiteConfiguration**: Esta secção denota os detalhes do dispositivo configurados como um site que está a ligar-se ao WAN virtual. Inclui o nome e endereço IP público do dispositivo de ramificação.
* **vpnSiteConnections**: Esta secção fornece informações sobre as seguintes definições:

    * Espaço de endereço da rede virtual do centro virtual.<br/>Exemplo:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Endereço espaço das redes virtuais que estão ligadas ao centro.<br>Exemplo:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * Endereços IP do gateway VPN do centro virtual. Como cada ligação do gateway VPN é composta por dois túneis em configuração ativa ativa, você verá ambos os endereços IP listados neste ficheiro. Neste exemplo, você `Instance0` `Instance1` vê e para cada site, e eles são endereços IP privados em vez de endereços IP públicos.<br>Exemplo:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * Detalhes de configuração para a ligação de gateway VPN, como BGP e chave pré-partilhada. A chave pré-partilhada é gerada automaticamente para si. Pode sempre editar a ligação na página **'Overview'** para uma chave pré-partilhada personalizada.
  
### <a name="example-device-configuration-file"></a>Exemplo de ficheiro de configuração de dispositivo

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>Configurar o dispositivo VPN

Se precisar de instruções para configurar o dispositivo, pode utilizar as instruções na [página VPN device configuration scripts](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) (Scripts de configuração de dispositivos VPN), tendo em conta os seguintes avisos:

* As instruções na página do dispositivo VPN não estão escritas para um WAN virtual. Mas pode utilizar os valores virtuais de WAN a partir do ficheiro de configuração para configurar manualmente o seu dispositivo VPN. 
* Os scripts de configuração do dispositivo descarregado saem para o gateway VPN não funcionam para o WAN virtual, porque a configuração é diferente.
* Um novo WAN virtual pode suportar tanto o IKEv1 como o IKEv2.
* Um WAN virtual só pode utilizar dispositivos VPN baseados na rota e instruções do dispositivo.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. Veja o seu WAN virtual

1. Vá ao WAN virtual.
1. Na página **overview,** cada ponto no mapa representa um hub.
1. Na secção **Hubs e conexões,** pode ver o estado de ligação do hub, local, região e VPN. Também pode ver bytes dentro e fora.

## <a name="7-monitor-a-connection"></a><a name="connectmon"></a>7. Monitorizar uma ligação

Criar uma ligação para monitorizar a comunicação entre uma máquina virtual Azure (VM) e um local remoto. Para obter informações sobre como configurar um monitor de ligação, veja [Monitorizar a comunicação de rede](~/articles/network-watcher/connection-monitor.md). O campo de origem é o VM IP em Azure, e o IP de destino é o site IP.

## <a name="8-clean-up-resources"></a><a name="cleanup"></a>8. Limpar recursos

Quando já não precisa destes recursos, pode utilizar o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que contém. Execute o seguinte comando `myResourceGroup` PowerShell e substitua-o pelo nome do seu grupo de recursos:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Este artigo ajuda-o a criar uma ligação VPN sobre o peering privado ExpressRoute utilizando o Virtual WAN. Para saber mais sobre o WAN virtual e as funcionalidades relacionadas, consulte a [visão geral do WAN virtual.](virtual-wan-about.md)
