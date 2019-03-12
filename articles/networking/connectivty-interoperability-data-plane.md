---
title: 'Interoperabilidade nas funcionalidades de conectividade de back-end do Azure: Análise do plano de dados | Documentos da Microsoft'
description: Este artigo fornece a análise de plano de dados da configuração do teste, que pode usar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site a site e da rede virtual peering no Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 75569d552baba8ce72b5e322d6ef6f81fcaa4bc5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761264"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-data-plane-analysis"></a>Interoperabilidade nas funcionalidades de conectividade de back-end do Azure: Análise de plano de dados

Este artigo descreve a análise de plano de dados do [configuração de teste][Setup]. Também pode rever o [configuração do programa de configuração de teste] [ Configuration] e o [controlar a análise de plano] [ Control-Analysis] da configuração do teste.

Análise de plano de dados examina o caminho percorrido por pacotes que atravessam a partir de uma rede local (LAN ou de rede virtual) para outra dentro de uma topologia. O caminho de dados entre duas redes locais não é necessariamente simétrico. Portanto, neste artigo, vamos analisar um caminho de encaminhamento de uma rede local, a outra rede separada do caminho inverso.

## <a name="data-path-from-the-hub-vnet"></a>Caminho de dados do hub de VNet

### <a name="path-to-the-spoke-vnet"></a>Caminho para o VNet spoke

Peering de rede virtual (VNet) emula a funcionalidade de ponte de rede entre as duas VNets em modo de peering. Traceroute de uma VNet do hub de saída a uma VM no spoke que vnet é mostrado aqui:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

A figura a seguir mostra a exibição de gráfico de ligação de VNet do hub e spoke VNet da perspetiva do observador de rede do Azure:


[![1]][1]

### <a name="path-to-the-branch-vnet"></a>Caminho para o ramo de VNet

Traceroute de uma VNet do hub de saída a uma VM no ramo que vnet é mostrado aqui:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

Este traceroute, o primeiro salto é o gateway de VPN no Gateway de VPN do Azure de VNet do hub. O segundo salto é o gateway VPN do ramo de VNet. O endereço IP do gateway de VPN do ramo de VNet não é anunciado na VNet do hub. O terceiro salto é a VM no ramo VNet.

A figura a seguir mostra a exibição de gráfico de ligação de VNet do hub e o ramo VNet da perspetiva do observador de rede:

[![2]][2]

Para a mesma ligação, a figura seguinte mostra a exibição de grade no observador de rede:

[![3]][3]

### <a name="path-to-on-premises-location-1"></a>Caminho para a localização no local 1

O traceroute saída de uma VNet do hub para uma VM em 1 de localização no local é mostrada aqui:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Este traceroute, o primeiro salto é o Azure ExpressRoute túnel ponto final de gateway para um roteador de borda do Microsoft Enterprise (MSEE). Os segundo e terceiro saltos são o router de limite (CE) do cliente e os IPs de LAN de 1 de localização no local. Estes endereços IP não são anunciados na VNet do hub. O quarto salto é a VM no 1 localização no local.


### <a name="path-to-on-premises-location-2"></a>Caminho para a localização no local 2

O traceroute saída de uma VNet do hub para uma VM em 2 de localização no local é mostrada aqui:

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

Este traceroute, o primeiro salto é o ponto final túnel do gateway ExpressRoute para uma MSEE. Os segundo e terceiro saltos são o roteador CE e os IPs de LAN de 2 de localização no local. Estes endereços IP não são anunciados na VNet do hub. O quarto salto é a VM em 2 de localização no local.

### <a name="path-to-the-remote-vnet"></a>Caminho para a VNet remota

O traceroute saída de uma VNet do hub para uma VM na VNet remota é mostrada aqui:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

Este traceroute, o primeiro salto é o ponto final túnel do gateway ExpressRoute para uma MSEE. O segundo salto é o IP do gateway da VNet remota. O intervalo IP do segundo salto não é anunciado na VNet do hub. O terceiro salto é a VM na VNet remota.

## <a name="data-path-from-the-spoke-vnet"></a>Caminho de dados a partir da VNet spoke

O VNet spoke partilha a visão de rede da VNet do hub. Através de VNet peering, o VNet spoke utiliza a conectividade de gateway remoto de VNet do hub como se ele está conectado diretamente à spoke VNet.

### <a name="path-to-the-hub-vnet"></a>Caminho para a VNet do hub

Traceroute de saída da spoke VNet para uma VM no hub que vnet é mostrado aqui:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>Caminho para o ramo de VNet

Traceroute de saída da spoke VNet para uma VM no ramo que vnet é mostrado aqui:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

Este traceroute, o primeiro salto é o gateway de VPN de VNet do hub. O segundo salto é o gateway VPN do ramo de VNet. O endereço IP do gateway de VPN do ramo de VNet não é anunciado dentro do VNet hub/spoke. O terceiro salto é a VM no ramo VNet.

### <a name="path-to-on-premises-location-1"></a>Caminho para a localização no local 1

O traceroute saída da spoke VNet para uma VM em 1 de localização no local é mostrada aqui:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Este traceroute, o primeiro salto é ExpressRoute túnel ponto final de gateway a VNet hub para um MSEE. Os segundo e terceiro saltos são o roteador CE e os IPs de LAN de 1 de localização no local. Estes endereços IP não são anunciados no hub/spoke VNet. O quarto salto é a VM no 1 localização no local.

### <a name="path-to-on-premises-location-2"></a>Caminho para a localização no local 2

O traceroute saída da spoke VNet para uma VM em 2 de localização no local é mostrada aqui:


    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Este traceroute, o primeiro salto é ExpressRoute túnel ponto final de gateway a VNet hub para um MSEE. Os segundo e terceiro saltos são o roteador CE e os IPs de LAN de 2 de localização no local. Estes endereços IP não são anunciados no hub/spoke VNets. O quarto salto é a VM na localização 2 no local.

### <a name="path-to-the-remote-vnet"></a>Caminho para a VNet remota

O traceroute saída da spoke VNet para uma VM na VNet remota é mostrada aqui:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

Este traceroute, o primeiro salto é ExpressRoute túnel ponto final de gateway a VNet hub para um MSEE. O segundo salto é o IP do gateway da VNet remota. O intervalo IP do segundo salto não está anunciado no hub/spoke VNet. O terceiro salto é a VM na VNet remota.

## <a name="data-path-from-the-branch-vnet"></a>Caminho de dados a partir do ramo de VNet

### <a name="path-to-the-hub-vnet"></a>Caminho para a VNet do hub

Traceroute saída a partir do ramo de VNet a uma VM no hub de que vnet é mostrado aqui:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

Este traceroute, o primeiro salto é o gateway de VPN do ramo de VNet. O segundo salto é o gateway VPN de VNet do hub. O endereço IP do gateway de VPN de VNet do hub não é anunciado na VNet remota. O terceiro salto é a VM na VNet do hub.

### <a name="path-to-the-spoke-vnet"></a>Caminho para o VNet spoke

Traceroute saída a partir do ramo de VNet a uma VM no spoke que vnet é mostrado aqui:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

Este traceroute, o primeiro salto é o gateway de VPN do ramo de VNet. O segundo salto é o gateway VPN de VNet do hub. O endereço IP do gateway de VPN de VNet do hub não é anunciado na VNet remota. O terceiro salto é a VM na spoke VNet.

### <a name="path-to-on-premises-location-1"></a>Caminho para a localização no local 1

Saída de traceroute do ramo a VNet a uma VM em 1 de localização no local é mostrada aqui:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

Este traceroute, o primeiro salto é o gateway de VPN do ramo de VNet. O segundo salto é o gateway VPN de VNet do hub. O endereço IP do gateway de VPN de VNet do hub não é anunciado na VNet remota. O terceiro salto é o ponto de terminação de túnel VPN no router CE primário. O quarto salto é um endereço IP de 1 de localização no local. Este endereço IP de LAN não está anunciado fora o roteador CE. O quinto salto é o destino de VM no 1 localização no local.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Caminho para 2 de localização no local e a VNet remota

Conforme discutimos na análise de plano de controlo, o ramo VNet tem sem visibilidade para 2 de localização no local ou para a VNet remota pela configuração de rede. Confirmar os resultados de ping seguintes: 

    C:\Users\rb>ping 10.1.31.10

    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

    C:\Users\rb>ping 10.17.30.4

    Pinging 10.17.30.4 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.17.30.4:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

## <a name="data-path-from-on-premises-location-1"></a>Caminho de dados de 1 de localização no local

### <a name="path-to-the-hub-vnet"></a>Caminho para a VNet do hub

Traceroute de saída de 1 de localização no local a uma VM no hub de que vnet é mostrado aqui:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

Neste traceroute, os dois primeiros saltos fazem parte da rede no local. O terceiro salto é a interface MSEE primária que enfrenta o roteador CE. O salto quarto é o gateway do ExpressRoute de VNet do hub. O intervalo IP do gateway ExpressRoute de VNet do hub não é anunciado à rede no local. O quinto salto é a VM de destino.

Observador de rede fornece apenas uma vista centrada no Azure. Para um ponto de vista no local, podemos usar o Monitor de desempenho de rede do Azure. Monitor de desempenho de rede fornece agentes que pode instalar em servidores em redes fora do Azure para análise de caminho de dados.

A figura a seguir mostra a vista de topologia de conectividade de VM de 1 de localização no local para a VM na VNet através do ExpressRoute do hub:

[![4]][4]

Como discutido anteriormente, a configuração de teste utiliza uma VPN de site a site como conectividade de cópia de segurança para o ExpressRoute entre 1 de localização no local e a VNet do hub. Para testar o caminho de cópia de segurança de dados, vamos induza uma falha de ligação de ExpressRoute entre o router no local 1 localização primário CE e do MSEE correspondente. Para induza uma falha de ligação do ExpressRoute, encerre a interface de CE que enfrenta do MSEE:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

A figura a seguir mostra a vista de topologia de conectividade de VM de 1 de localização no local para a VM na VNet do hub através de conectividade VPN de site a site quando a conectividade do ExpressRoute está inativo:

[![5]][5]

### <a name="path-to-the-spoke-vnet"></a>Caminho para o VNet spoke

Traceroute de saída de 1 de localização no local a uma VM no spoke que vnet é mostrado aqui:

Vamos trazer de volta a conectividade de principal do ExpressRoute para fazer a análise de caminho de dados para a VNet spoke:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Abra a conectividade de 1 do ExpressRoute primária para o restante da análise de caminho de dados.

### <a name="path-to-the-branch-vnet"></a>Caminho para o ramo de VNet

Traceroute de saída de 1 de localização no local a uma VM no ramo que vnet é mostrado aqui:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>Caminho para a localização no local 2

À medida que abordamos a [controlar a análise de plano][Control-Analysis], tem de 1 de localização no local sem visibilidade para 2 de localização no local pela configuração de rede. Confirmar os resultados de ping seguintes: 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>Caminho para a VNet remota

O traceroute saída de 1 de localização no local a uma VM na VNet remota é mostrada aqui:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>Caminho de dados de 2 de localização no local

### <a name="path-to-the-hub-vnet"></a>Caminho para a VNet do hub

Traceroute de saída de 2 de localização no local a uma VM no hub de que vnet é mostrado aqui:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Caminho para o VNet spoke

Traceroute de saída de 2 de localização no local a uma VM no spoke que vnet é mostrado aqui:

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Caminho para o ramo VNet, no local a 1 de localização e a VNet remota

À medida que abordamos a [controlar a análise de plano][Control-Analysis], tem de 1 de localização no local sem visibilidade para o ramo VNet, a 1 de localização no local ou para a VNet remota pela configuração de rede. 

## <a name="data-path-from-the-remote-vnet"></a>Caminho de dados a partir da VNet remota

### <a name="path-to-the-hub-vnet"></a>Caminho para a VNet do hub

Traceroute de saída da VNet remota a uma VM no hub de que vnet é mostrado aqui:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Caminho para o VNet spoke

Traceroute de saída da VNet remota a uma VM no spoke que vnet é mostrado aqui:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Caminho para o ramo de VNet e de 2 de localização no local

À medida que abordamos a [controlar a análise de plano][Control-Analysis], a VNet remota tiver sem visibilidade para o ramo de VNet ou para 2 de localização no local pela configuração de rede. 

### <a name="path-to-on-premises-location-1"></a>Caminho para a localização no local 1

O traceroute saída da VNet remota a uma VM em 1 de localização no local é mostrada aqui:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Conectividade VPN de site a site e ExpressRoute em conjunto

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN de site a site através do ExpressRoute

Pode configurar uma VPN de site a site com o ExpressRoute peering em privado trocar dados entre a rede no local e as VNets do Azure da Microsoft. Com esta configuração, podem trocar dados com confidencialidade e integridade autenticidade. Além disso, a troca de dados é anti-repetições. Para obter mais informações sobre como configurar uma VPN IPsec de site a site no modo de túnel com o ExpressRoute peering da Microsoft, consulte [VPN de Site a site através do peering da Microsoft do ExpressRoute][S2S-Over-ExR]. 

A principal limitação de configuração de uma VPN de site a site que utiliza o peering da Microsoft é a taxa de transferência. Taxa de transferência sobre o túnel IPsec é limitada pela capacidade do gateway VPN. O débito do gateway VPN é inferior a taxa de transferência do ExpressRoute. Neste cenário, utilizar o túnel IPsec para o tráfego altamente seguro e utilizar o peering privado para todos os outros tráfegos ajuda a otimizar a utilização de largura de banda do ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN de site a site como um caminho de ativação pós-falha seguro para o ExpressRoute

ExpressRoute funciona como um par de circuito redundantes para elevada disponibilidade. Pode configurar a conectividade do ExpressRoute com redundância geográfica em diferentes regiões do Azure. Além disso, como demonstrado em nossa configuração de teste, dentro de uma região do Azure, pode utilizar uma VPN de site a site para criar um caminho de ativação pós-falha para a conectividade do ExpressRoute. Quando os mesmos prefixos são anunciados através do ExpressRoute e uma VPN de site a site, o Azure dá prioridade ExpressRoute. Para evitar o encaminhamento assimétricas entre ExpressRoute e VPN site a site, no local a configuração de rede também deve reciprocate utilizando a conectividade do ExpressRoute antes de ele usa a conectividade VPN de site a site.

Para obter mais informações sobre como configurar ligações coexistentes ExpressRoute e uma VPN de site a site, consulte [ExpressRoute e coexistência site a site][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Expandir a conectividade de back-end para spoke VNets e localizações de sucursais

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Conectividade de VNet do spoke ao utilizar o VNet peering

Hub- and -spoke arquitetura da VNet é amplamente utilizada. O hub é uma VNet no Azure que age como um ponto central de conectividade entre a VNets spoke e à sua rede no local. Os spokes são VNets que partilham com o hub, e que pode ser usada para isolar cargas de trabalho. O tráfego flui entre o datacenter no local e o hub através de uma ligação VPN ou ExpressRoute. Para obter mais informações sobre a arquitetura, consulte [implementar uma topologia de rede hub-and-spoke no Azure][Hub-n-Spoke].

O VNet peering dentro de uma região, spoke VNets pode utilizar gateways de VNet do hub (gateways VPN e ExpressRoute) para comunicar com redes remotas.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Ramificar a conectividade VNet através da VPN site a site

Pode desejar ramo VNets, que estão em diferentes regiões e redes no local para comunicar entre si através de uma VNet do hub. A solução nativa do Azure para esta configuração é a conectividade VPN de site a site com uma VPN. Uma alternativa é usar uma aplicação virtual de rede (NVA) para o encaminhamento no hub.

Para obter mais informações, consulte [o que é o Gateway de VPN?] [ VPN] e [implementar uma NVA altamente disponível][Deploy-NVA].


## <a name="next-steps"></a>Passos Seguintes

Consulte a [FAQ do ExpressRoute] [ ExR-FAQ] para:
-   Saiba quantos circuitos do ExpressRoute pode ligar a um gateway do ExpressRoute.
-   Saiba quantos gateways do ExpressRoute pode ligar a um circuito do ExpressRoute.
-   Saiba mais sobre outros limites de dimensionamento do ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "vista do observador de rede de conectividade entre uma VNet do hub e uma VNet spoke"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "vista do observador de rede de conectividade entre uma VNet do hub e um ramo de VNet"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "exibição de grade do observador de rede de conectividade entre uma VNet do hub e um ramo de VNet"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "exibição de Monitor de desempenho de rede de conectividade entre a VM de 1 de localização e a VNet através do ExpressRoute 1 do hub"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "exibição de Monitor de desempenho de rede de conectividade entre a VM de 1 de localização e a VNet através de uma VPN de site a site de hub"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


