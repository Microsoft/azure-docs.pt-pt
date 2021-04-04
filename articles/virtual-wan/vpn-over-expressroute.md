---
title: 'Configurar encriptação ExpressRoute: IPsec over ExpressRoute for Azure Virtual WAN'
description: Saiba como usar o Azure Virtual WAN para criar uma ligação VPN site-to-site sobre o espreitamento privado ExpressRoute.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: b8dde3ed76587e2343edaec8626287853ec6ef9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96487512"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>Encriptação ExpressRoute: IPsec over ExpressRoute for Virtual WAN

Este artigo mostra-lhe como usar a Azure Virtual WAN para estabelecer uma ligação IPsec/IKE VPN da sua rede no local para Azure sobre o espreitamento privado de um circuito Azure ExpressRoute. Esta técnica pode fornecer um trânsito encriptado entre as redes no local e as redes virtuais Azure através do ExpressRoute, sem passar pela internet pública ou usar endereços IP públicos.

## <a name="topology-and-routing"></a>Topologia e encaminhamento

O seguinte diagrama mostra um exemplo de conectividade VPN sobre o espreguimamento privado ExpressRoute:

:::image type="content" source="./media/vpn-over-expressroute/vwan-vpn-over-er.png" alt-text="VPN sobre ExpressRoute":::

O diagrama mostra uma rede dentro da rede de instalações ligada à porta de entrada VPN do hub Azure sobre o expressRoute private peering. O estabelecimento de conectividade é simples:

1. Estabeleça a conectividade ExpressRoute com um circuito ExpressRoute e um espreitamento privado.
2. Estabeleça a conectividade VPN como descrito neste artigo.

Um aspeto importante desta configuração é o encaminhamento entre as redes no local e o Azure sobre os caminhos ExpressRoute e VPN.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Tráfego das redes no local para Azure

Para o tráfego das redes no local para Azure, os prefixos Azure (incluindo o hub virtual e todas as redes virtuais faladas ligadas ao hub) são anunciados através do ExpressRoute private peering BGP e do VPN BGP. Isto resulta em duas rotas de rede (caminhos) em direção a Azure a partir das redes no local:

- Um sobre o caminho protegido pelo IPsec
- Um diretamente sobre ExpressRoute *sem* proteção IPsec 

Para aplicar encriptação à comunicação, deve certificar-se de que, para a rede ligada à VPN no diagrama, as rotas Azure através do gateway VPN no local são preferíveis ao longo do caminho expresso direto.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Tráfego de Azure para redes no local

O mesmo requisito aplica-se ao tráfego de Azure para redes no local. Para garantir que o caminho IPsec é preferido sobre o caminho expresso direto (sem IPsec), tem duas opções:

- Anuncie prefixos mais específicos na sessão VPN BGP para a rede ligada à VPN. Pode anunciar uma gama maior que engloba a rede ligada à VPN sobre o estomamento privado ExpressRoute, depois gamas mais específicas na sessão de BGP VPN. Por exemplo, anuncie 10.0.0.0/16 sobre o ExpressRoute e 10.0.0.0/24 sobre a VPN.

- Anuncie prefixos de desarticulação para VPN e ExpressRoute. Se as gamas de rede ligadas à VPN forem desarticuladas de outras redes conectadas com o ExpressRoute, pode anunciar os prefixos nas sessões BGP VPN e ExpressRoute, respectivamente. Por exemplo, anuncie 10.0.0.0/24 sobre o ExpressRoute e 10.0.0.0/24 sobre a VPN.

Em ambos os exemplos, a Azure enviará tráfego para 10.0.1.0/24 sobre a ligação VPN em vez de diretamente sobre ExpressRoute sem proteção VPN.

> [!WARNING]
> Se publicitar os *mesmos* prefixos sobre as ligações ExpressRoute e VPN, a Azure utilizará o caminho ExpressRoute diretamente sem proteção VPN.
>

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. Criar um WAN virtual e hub com gateways

Os seguintes recursos Azure e as configurações correspondentes no local devem estar em vigor antes de proceder:

- Um WAN virtual Azure
- Um hub WAN virtual com uma [porta de entrada ExpressRoute](virtual-wan-expressroute-portal.md) e uma [porta de entrada VPN](virtual-wan-site-to-site-portal.md)

Para os passos para criar um WAN virtual Azure e um hub com uma associação ExpressRoute, consulte [Criar uma associação ExpressRoute utilizando a Azure Virtual WAN](virtual-wan-expressroute-portal.md). Para que os passos criem uma porta de entrada VPN no WAN virtual, consulte [Criar uma ligação site-to-site utilizando Azure Virtual WAN](virtual-wan-site-to-site-portal.md).

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. Criar um site para a rede no local

O recurso do site é o mesmo que os sites VPN não ExpressRoute para um WAN virtual. O endereço IP do dispositivo VPN no local pode agora ser um endereço IP privado, ou um endereço IP público na rede de acesso ao local através do peering privado ExpressRoute criado no passo 1.

> [!NOTE]
> O endereço IP para o dispositivo VPN no local *deve fazer* parte dos prefixos de endereço anunciados para o hub wan virtual via Azure ExpressRoute.
>

1. Vá ao portal Azure no seu browser. 
1. Selecione o hub que criou. Na página virtual do hub WAN, em **Conectividade,** selecione **sites VPN**.
1. Na página dos **sites VPN,** selecione **+Criar site.**
1. Na página **Criar site**, preencha os seguintes campos:
   * **Assinatura**: Verifique a subscrição.
   * **Grupo de Recursos**: Selecione ou crie o grupo de recursos que pretende utilizar.
   * **Região**: Entre na região de Azure para o recurso do site VPN.
   * **Nome**: Introduza o nome pelo qual pretende consultar o seu site no local.
   * **Fornecedor de dispositivos**: Introduza o fornecedor do dispositivo VPN no local.
   * **Protocolo border gateway**: Selecione "Enable" se a sua rede no local utilizar o BGP.
   * **Espaço de endereço privado**: Insira o espaço de endereço IP localizado no seu site no local. O tráfego destinado a este espaço de endereço é encaminhado para a rede no local através do gateway VPN.
   * **Hubs**: Selecione um ou mais centros para ligar este site VPN. Os centros selecionados devem ter gateways VPN já criados.
1. Selecione **Seguinte: Links >** para as definições de ligação VPN:
   * **Nome do link**: O nome pelo qual pretende consultar esta ligação.
   * **Nome do fornecedor**: O nome do fornecedor de serviços de internet para este site. Para uma rede expressRoute no local, é o nome do prestador de serviços ExpressRoute.
   * **Velocidade**: A velocidade da ligação de serviço de internet ou do circuito ExpressRoute.
   * **Endereço IP**: O endereço IP público do dispositivo VPN que reside no seu site no local. Ou, para o ExpressRoute no local, é o endereço IP privado do dispositivo VPN via ExpressRoute.

   Se o BGP estiver ativado, aplicar-se-á a todas as ligações criadas para este site em Azure. Configurar o BGP num WAN virtual equivale a configurar o BGP num gateway Azure VPN. 
   
   O seu endereço de pares BGP no local *não deve* ser o mesmo que o endereço IP da sua VPN para o dispositivo ou o espaço de endereço de rede virtual do site VPN. Utilize um endereço IP diferente no dispositivo VPN para o seu IP de pares BGP. Pode ser um endereço atribuído à interface de loopback no dispositivo. No entanto, *não pode* ser uma APIPA (169.254.*x*. *x*) endereço. Especifique este endereço no site VPN correspondente que representa a localização. Para pré-requisitos BGP, consulte [Sobre o BGP com o Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

1. Selecione **Seguinte: Revisão + criar >** para verificar os valores de definição e criar o site VPN. Se selecionou **Hubs** para ligar, a ligação será estabelecida entre a rede no local e o gateway VPN do hub.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. Atualize a definição de ligação VPN para utilizar o ExpressRoute

Depois de criar o site VPN e ligar ao centro, utilize os seguintes passos para configurar a ligação para utilizar o espreguite privado ExpressRoute:

1. Volte para a página virtual de recursos WAN e selecione o recurso hub. Ou navegue do site VPN para o centro conectado.

   :::image type="content" source="./media/vpn-over-expressroute/hub-selection.png" alt-text="Selecione um hub":::
1. Em **Conectividade,** selecione **VPN (Site-to-Site)**.

   :::image type="content" source="./media/vpn-over-expressroute/vpn-select.png" alt-text="Selecione VPN (Site-to-Site)":::
1. Selecione a elipse **(...**) no site VPN em vez do ExpressRoute e selecione **a ligação Editar VPN a este centro**.

   :::image type="content" source="./media/vpn-over-expressroute/config-menu.png" alt-text="Insira o menu de configuração":::
1. Para **utilizar o endereço IP privado Azure**, selecione **Sim**. A definição configura a porta de entrada VPN do hub para utilizar endereços IP privados dentro do intervalo de endereços do hub no gateway para esta ligação, em vez dos endereços IP públicos. Isto garantirá que o tráfego a partir da rede de instalações percorra os caminhos privados de observação ExpressRoute em vez de utilizar a internet pública para esta ligação VPN. A imagem que se segue mostra a definição:

   :::image type="content" source="./media/vpn-over-expressroute/vpn-link-configuration.png" alt-text="Definição para utilização de um endereço IP privado para a ligação VPN" border="false":::
1. Selecione **Guardar**.

Depois de guardar as suas alterações, o gateway VPN do hub utilizará os endereços IP privados no gateway VPN para estabelecer as ligações IPsec/IKE com o dispositivo VPN no local sobre o ExpressRoute.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. Obtenha os endereços IP privados para o portal VPN hub

Descarregue a configuração do dispositivo VPN para obter os endereços IP privados do gateway VPN do hub. Precisa destes endereços para configurar o dispositivo VPN no local.

1. Na página para o seu hub, selecione **VPN (Site-to-Site)** em **Conectividade**.
1. No topo da página **'Overview',** selecione **Download VPN Config**. 

   O Azure cria uma conta de armazenamento no grupo de recursos "microsoft-network-[location]", onde a *localização* é a localização do WAN. Depois de aplicar a configuração nos seus dispositivos VPN, pode eliminar esta conta de armazenamento.
1. Depois de o ficheiro ser criado, selecione o link para o descarregar.
1. Aplique a configuração ao dispositivo VPN.

### <a name="vpn-device-configuration-file"></a>Ficheiro de configuração do dispositivo VPN

O ficheiro de configuração do dispositivo contém as definições a utilizar quando está a configurar o dispositivo VPN no local. Quando vir este ficheiro, repare nas informações seguintes:

* **VPNSiteConfiguration**: Esta secção denota os detalhes do dispositivo configurados como um site que está a ligar-se ao WAN virtual. Inclui o nome e endereço IP público do dispositivo de filial.
* **VPNSiteConnections**: Esta secção fornece informações sobre as seguintes definições:

    * Espaço de endereço da rede virtual do centro virtual.<br/>Exemplo:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Endereço espaço das redes virtuais que estão ligadas ao hub.<br>Exemplo:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * Endereços IP do portal VPN do centro virtual. Como cada ligação do gateway VPN é composta por dois túneis em configuração ativa, verá ambos os endereços IP listados neste ficheiro. Neste exemplo, você vê `Instance0` e `Instance1` para cada site, e são endereços IP privados em vez de endereços IP públicos.<br>Exemplo:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * Detalhes de configuração para a ligação de gateway VPN, como BGP e chave pré-partilhada. A chave pré-partilhada é gerada automaticamente para si. Pode sempre editar a ligação na página **'Vista Geral'** para uma chave pré-partilhada personalizada.
  
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

* As instruções na página do dispositivo VPN não estão escritas para um WAN virtual. Mas pode utilizar os valores WAN virtuais do ficheiro de configuração para configurar manualmente o seu dispositivo VPN. 
* Os scripts de configuração do dispositivo descarregados que são para o gateway VPN não funcionam para o WAN virtual, porque a configuração é diferente.
* Um novo WAN virtual pode suportar tanto o IKEv1 como o IKEv2.
* Um WAN virtual só pode usar dispositivos VPN baseados em rotas e instruções do dispositivo.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. Veja o seu WAN virtual

1. Vá ao WAN virtual.
1. Na página **'Visão Geral',** cada ponto no mapa representa um hub.
1. Na secção **Hubs e conexões,** pode ver o centro, o local, a região e o estado de ligação VPN. Você também pode ver bytes dentro e fora.

## <a name="6-monitor-a-connection"></a><a name="connectmon"></a>6. Monitorizar uma ligação

Crie uma ligação para monitorizar a comunicação entre uma máquina virtual Azure (VM) e um site remoto. Para obter informações sobre como configurar um monitor de ligação, veja [Monitorizar a comunicação de rede](~/articles/network-watcher/connection-monitor.md). O campo de origem é o IP VM em Azure, e o IP de destino é o IP do site.

## <a name="7-clean-up-resources"></a><a name="cleanup"></a>7. Limpar recursos

Quando já não precisar destes recursos, pode utilizar [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que contém. Executar o seguinte comando PowerShell e substituir `myResourceGroup` pelo nome do seu grupo de recursos:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Este artigo ajuda-o a criar uma ligação VPN sobre o expressRoute peering privado usando O WAN Virtual. Para saber mais sobre o WAN virtual e funcionalidades relacionadas, consulte a visão geral do [WAN virtual.](virtual-wan-about.md)
