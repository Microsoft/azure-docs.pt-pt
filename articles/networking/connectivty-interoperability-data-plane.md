---
title: 'Interoperabilidade em Azure : Análise de planos de dados'
description: Este artigo fornece a análise de plano de dados da configuração do teste que pode usar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site-to-site e o peering de rede virtual em Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: fe7b74b0d4d065d4f222fefbbdc4a1d434d1163b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518254"
---
# <a name="interoperability-in-azure--data-plane-analysis"></a>Interoperabilidade em Azure : Análise de planos de dados

Este artigo descreve a análise do plano de dados da [configuração][Setup]do teste . Também pode rever a configuração da [configuração][Configuration] do teste e a análise do plano de [controlo][Control-Analysis] da configuração do teste.

A análise de planos de dados examina o caminho percorrido por pacotes que atravessam de uma rede local (LAN ou rede virtual) para outra dentro de uma topologia. O caminho de dados entre duas redes locais não é necessariamente simétrico. Portanto, neste artigo, analisamos um caminho de encaminhamento de uma rede local para outra rede que está separada do caminho inverso.

## <a name="data-path-from-the-hub-vnet"></a>Caminho de dados do hub VNet

### <a name="path-to-the-spoke-vnet"></a>Caminho para o VNet falado

O peering da rede virtual (VNet) imita a funcionalidade da ponte de rede entre os dois VNets que são espreitados. A saída de traceroute de um hub VNet para um VM no VNet falado é mostrada aqui:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

A figura que se segue mostra a visão de ligação gráfica do hub VNet e do VNet falado na perspetiva do Azure Network Watcher:


![1][1]

### <a name="path-to-the-branch-vnet"></a>Caminho para o ramo VNet

A saída de traceroute de um hub VNet para um VM no ramo VNet é mostrada aqui:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

Nesta rota de rastreio, o primeiro salto é o gateway VPN em Azure VPN Gateway do hub VNet. O segundo salto é a porta vpn do ramo VNet. O endereço IP da porta VPN da sucursal VNet não é anunciado no centro VNet. O terceiro salto é o VM no ramo VNet.

A figura que se segue mostra a vista de ligação gráfica do hub VNet e do ramo VNet na perspetiva do Observador de Rede:

![2][2]

Para a mesma ligação, o seguinte número mostra a visão da grelha no Observador da Rede:

![3][3]

### <a name="path-to-on-premises-location-1"></a>Caminho para a localização no local 1

A saída de traceroute de um hub VNet para um VM no local 1 é mostrada aqui:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Nesta rota de rastreio, o primeiro salto é o ponto final do túnel de gateway Azure ExpressRoute para um Microsoft Enterprise Edge Router (MSEE). O segundo e terceiro lúpulo são o router de borda do cliente (CE) e o local de funcionamento Local 1 LAN IPs. Estes endereços IP não são anunciados no centro VNet. O quarto salto é o VM no local 1.


### <a name="path-to-on-premises-location-2"></a>Caminho para a localização no local 2

A saída de traceroute de um hub VNet para um VM no local de localização 2 no local é mostrada aqui:

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

Nesta rota de rastreio, o primeiro salto é o ponto final do túnel de gateway ExpressRoute para um MSEE. O segundo e terceiro lúpulo são o router CE e o local no local 2 LAN IPs. Estes endereços IP não são anunciados no centro VNet. O quarto salto é o VM no local 2.

### <a name="path-to-the-remote-vnet"></a>Caminho para o VNet remoto

A saída de traceroute de um hub VNet para um VM no VNet remoto é mostrada aqui:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

Nesta rota de rastreio, o primeiro salto é o ponto final do túnel de gateway ExpressRoute para um MSEE. O segundo salto é o IP remoto da VNet. A segunda gama ip hop não é publicitada no centro VNet. O terceiro salto é o VM no VNet remoto.

## <a name="data-path-from-the-spoke-vnet"></a>Caminho de dados do VNet falado

A VNet fala da rede do hub VNet. Através do peering VNet, o VNet falado usa a conectividade remota de gateway do hub VNet como se estivesse diretamente ligado ao VNet falado.

### <a name="path-to-the-hub-vnet"></a>Caminho para o hub VNet

A saída de traceroute do VNet falado para um VM no centro VNet é mostrada aqui:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>Caminho para o ramo VNet

A saída de traceroute do VNet falado para um VM no ramo VNet é mostrada aqui:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

Nesta rota de rastreio, o primeiro salto é a porta de entrada VPN do hub VNet. O segundo salto é a porta vpn do ramo VNet. O endereço IP da porta VPN da sucursal VNet não é anunciado dentro do hub/spoke VNet. O terceiro salto é o VM no ramo VNet.

### <a name="path-to-on-premises-location-1"></a>Caminho para a localização no local 1

A saída de traceroute do VNet falado para um VM no local 1 no local é mostrada aqui:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Nesta rota de rastreio, o primeiro salto é o ponto final do túnel de gateway ExpressRoute do hub VNet para um MSEE. O segundo e terceiro lúpulo são o router CE e o local 1 LAN IPs. Estes endereços IP não são anunciados no hub/spoke VNet. O quarto salto é o VM no local 1.

### <a name="path-to-on-premises-location-2"></a>Caminho para a localização no local 2

A saída de traceroute do VNet falado para um VM no local de instalação Localização 2 é mostrada aqui:


    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    76 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

Nesta rota de rastreio, o primeiro salto é o ponto final do túnel de gateway ExpressRoute do hub VNet para um MSEE. O segundo e terceiro lúpulo são o router CE e o local no local 2 LAN IPs. Estes endereços IP não são anunciados no hub/spoke VNets. O quarto salto é o VM no local 2.

### <a name="path-to-the-remote-vnet"></a>Caminho para o VNet remoto

A saída de traceroute do VNet falado para um VM no VNet remoto é mostrada aqui:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

Nesta rota de rastreio, o primeiro salto é o ponto final do túnel de gateway ExpressRoute do hub VNet para um MSEE. O segundo salto é o IP remoto da VNet. A segunda gama ip hop não é publicitada no hub/spoke VNet. O terceiro salto é o VM no VNet remoto.

## <a name="data-path-from-the-branch-vnet"></a>Caminho de dados do ramo VNet

### <a name="path-to-the-hub-vnet"></a>Caminho para o hub VNet

A saída de traceroute do ramo VNet para um VM no centro VNet é mostrada aqui:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

Nesta rota de rastreio, o primeiro salto é a porta de entrada VPN do ramo VNet. O segundo salto é a porta de entrada VPN do hub VNet. O endereço IP da porta VPN do hub VNet não é anunciado no VNet remoto. O terceiro salto é o VM no centro VNet.

### <a name="path-to-the-spoke-vnet"></a>Caminho para o VNet falado

A saída de traceroute do ramo VNet para um VM no VNet falado é mostrada aqui:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

Nesta rota de rastreio, o primeiro salto é a porta de entrada VPN do ramo VNet. O segundo salto é a porta de entrada VPN do hub VNet. O endereço IP da porta VPN do hub VNet não é anunciado no VNet remoto. O terceiro salto é o VM no VNet falado.

### <a name="path-to-on-premises-location-1"></a>Caminho para a localização no local 1

A saída de traceroute do ramo VNet para um VM no local 1 é mostrada aqui:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

Nesta rota de rastreio, o primeiro salto é a porta de entrada VPN do ramo VNet. O segundo salto é a porta de entrada VPN do hub VNet. O endereço IP da porta VPN do hub VNet não é anunciado no VNet remoto. O terceiro lúpulo é o ponto de terminação do túnel VPN no router CE primário. O quarto lúpulo é um endereço IP interno do local 1 no local. Este endereço IP LAN não é anunciado fora do router CE. O quinto lúpulo é o destino VM no local 1 no local.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Caminho para a localização no local 2 e o VNet remoto

Como discutimos na análise do plano de controlo, o ramo VNet não tem visibilidade nem para o local Local 2 ou para o VNet remoto por configuração da rede. Os seguintes resultados do ping confirmam: 

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

## <a name="data-path-from-on-premises-location-1"></a>Caminho de dados a partir do local 1

### <a name="path-to-the-hub-vnet"></a>Caminho para o hub VNet

A saída de traceroute do local 1 para um VM no centro VNet é mostrada aqui:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

Nesta rota, os dois primeiros lúpulos fazem parte da rede no local. O terceiro salto é a interface principal do MSEE que está virada para o router CE. O quarto salto é a porta de entrada ExpressRoute do hub VNet. A gama IP da porta de entrada ExpressRoute do hub VNet não é publicitada para a rede no local. O quinto salto é o destino VM.

O Network Watcher fornece apenas uma visão centrada no Azure. Para uma perspetiva no local, utilizamos o Monitor de Desempenho da Rede Azure. O Network Performance Monitor fornece aos agentes que pode instalar em servidores em redes fora do Azure para análise de caminhos de dados.

A figura que se segue mostra a visão de topologia da conectividade VM local 1 do local 1 com o VM no hub VNet via ExpressRoute:

![4][4]

Como discutido anteriormente, a configuração do teste utiliza uma VPN site-to-site como conectividade de backup para a ExpressRoute entre o local de instalação 1 e o hub VNet. Para testar o caminho dos dados de backup, vamos induzir uma falha de ligação ExpressRoute entre o router primário CE local 1 e o MSEE correspondente. Para induzir uma falha de ligação ExpressRoute, desligue a interface CE que se encontra virada para o MSEE:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

A figura que se segue mostra a visão de topologia da conectividade VM local 1 do Local 1 com o VM no hub VNet através da conectividade VPN local-a-local quando a conectividade ExpressRoute está em baixo:

![5][5]

### <a name="path-to-the-spoke-vnet"></a>Caminho para o VNet falado

A saída de traceroute do local 1 para um VM no VNet falado é mostrada aqui:

Vamos trazer de volta a conectividade primária expressRoute para fazer a análise do caminho de dados para o VNet falado:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Eleve a conectividade primária expressRoute 1 para o resto da análise da trajetória de dados.

### <a name="path-to-the-branch-vnet"></a>Caminho para o ramo VNet

A saída de traceroute do local 1 para um VM no ramo VNet é mostrada aqui:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>Caminho para a localização no local 2

Como discutimos na análise do plano de [controlo,][Control-Analysis]o local 1 no local não tem visibilidade para o local Local 2 de acordo com a configuração da rede. Os seguintes resultados do ping confirmam: 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>Caminho para o VNet remoto

A saída de traceroute do local 1 para um VM no VNet remoto é mostrada aqui:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>Caminho de dados a partir do local 2

### <a name="path-to-the-hub-vnet"></a>Caminho para o hub VNet

A saída de traceroute do local local 2 para um VM no centro VNet é mostrada aqui:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Caminho para o VNet falado

A saída de traceroute do local local 2 para um VM no VNet falado é mostrada aqui:

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Caminho para o ramo VNet, no local Localização 1, e o VNet remoto

Como discutimos na análise do plano de [controlo,][Control-Analysis]o local de localização 1 no local não tem visibilidade para o ramo VNet, para o local local Localização 1, ou para o VNet remoto de acordo com a configuração da rede. 

## <a name="data-path-from-the-remote-vnet"></a>Caminho de dados a partir do VNet remoto

### <a name="path-to-the-hub-vnet"></a>Caminho para o hub VNet

A saída de traceroute do VNet remoto para um VM no centro VNet é mostrada aqui:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Caminho para o VNet falado

A saída de traceroute do VNet remoto para um VM no VNet falado é mostrada aqui:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Caminho para o ramo VNet e no local Localização 2

Como discutimos na análise do plano de [controlo,][Control-Analysis]o VNet remoto não tem visibilidade para o ramo VNet ou para o local Localização 2 de acordo com a configuração da rede. 

### <a name="path-to-on-premises-location-1"></a>Caminho para a localização no local 1

A saída de traceroute do VNet remoto para um VM no local 1 é mostrada aqui:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute e conectividade VPN site-to-site em conjunto

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN site-to-site sobre ExpressRoute

Pode configurar um VPN site-to-site utilizando o ExpressRoute Microsoft, olhando para a troca privada de dados entre a sua rede no local e os seus VNets Azure. Com esta configuração, pode trocar dados com confidencialidade, autenticidade e integridade. A troca de dados também é anti-repetição. Para obter mais informações sobre como configurar um IPsec VPN site-to-site em modo túnel, utilizando o peering expressRoute Microsoft, consulte [o Site-to-site VPN através do peering expressRoute Microsoft][S2S-Over-ExR]. 

A principal limitação de configurar uma VPN site-to-site que usa o peering da Microsoft é a entrada. A entrada sobre o túnel IPsec é limitada pela capacidade de gateway VPN. A entrada de gateway VPN é inferior à entrada do ExpressRoute. Neste cenário, a utilização do túnel IPsec para tráfego altamente seguro e a utilização de um esparso privado para todos os outros tráfegos ajuda a otimizar a utilização da largura de banda ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN do site-para-site como um caminho de falha seguro para expressRoute

A ExpressRoute serve como um par de circuitos redundantes para garantir uma alta disponibilidade. Pode configurar a conectividade ExpressRoute geo-redundante em diferentes regiões do Azure. Além disso, como demonstrado na nossa configuração de teste, dentro de uma região do Azure, você pode usar um VPN site-to-site para criar um caminho de failover para a sua conectividade ExpressRoute. Quando os mesmos prefixos são anunciados tanto sobre o ExpressRoute como sobre uma VPN site-to-site, o Azure prioriza o ExpressRoute. Para evitar o encaminhamento assimétrico entre o ExpressRoute e o VPN site-to-site, a configuração da rede no local também deve retribuir usando a conectividade ExpressRoute antes de utilizar a conectividade VPN site-to-site.

Para obter mais informações sobre como configurar as ligações coexistentes para o ExpressRoute e uma VPN site-to-site, consulte [expressRoute e coexistência site-to-site][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Alargar a conectividade de back-end a VNets falados e localizações de filiais

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Conectividade VNet falada usando o peering VNet

A arquitetura VNet hub e falada é amplamente usada. O hub é um VNet em Azure que funciona como um ponto central de conectividade entre os seus VNets falados e a sua rede no local. Os raios são VNets que espreitam com o centro, e que você pode usar para isolar cargas de trabalho. Fluxos de tráfego entre o datacenter no local e o centro através de uma ligação ExpressRoute ou VPN. Para obter mais informações sobre a arquitetura, consulte [Implementar uma topologia de rede de hub-spoke em Azure][Hub-n-Spoke].

Em VNet espreitando dentro de uma região, vNets falados podem usar gateways vNet hub (tanto gateways VPN como ExpressRoute) para comunicar com redes remotas.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Conectividade Branch VNet utilizando VPN site-to-site

Você pode querer que os VNets de ramificação, que estão em diferentes regiões, e redes no local para comunicar uns com os outros através de um hub VNet. A solução azure nativa para esta configuração é a conectividade VPN site-to-site utilizando uma VPN. Uma alternativa é utilizar um aparelho virtual de rede (NVA) para o encaminhamento no centro.

Para mais informações, consulte o [Deploy a highly available NVA][Deploy-NVA] [que é VPN Gateway?][VPN]


## <a name="next-steps"></a>Passos seguintes

Consulte o [ExpressRoute FAQ][ExR-FAQ] para:
-   Saiba quantos circuitos ExpressRoute pode ligar a um gateway ExpressRoute.
-   Saiba quantos gateways ExpressRoute pode ligar a um circuito ExpressRoute.
-   Conheça outros limites de escala da ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Visão de Network Watcher da conectividade de um hub VNet para um VNet falado"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Visão de network watcher da conectividade de um hub VNet para um ramo VNet"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "Visão de rede Observador da conectividade de um hub VNet para um ramo VNet"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Visão do Monitor de Desempenho da Rede da conectividade desde a Localização 1 VM até ao hub VNet via ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Visão do Monitor de Desempenho da Rede da conectividade desde a Localização 1 VM até ao hub VNet através de uma VPN site-to-site"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
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


