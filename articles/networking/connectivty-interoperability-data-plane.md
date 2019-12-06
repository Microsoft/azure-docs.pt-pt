---
title: 'Interoperabilidade nos recursos de conectividade de back-end do Azure: análise do plano de dados | Microsoft Docs'
description: Este artigo fornece a análise do plano de dados da configuração de teste que você pode usar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site a site e um emparelhamento de rede virtual no Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 11c964bedce7a8b979434b888d756c2121d06a60
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873833"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-data-plane-analysis"></a>Interoperabilidade nos recursos de conectividade de back-end do Azure: análise do plano de dados

Este artigo descreve a análise do plano de dados da [configuração do teste][Setup]. Você também pode examinar a [configuração][Configuration] de configuração de teste e a [análise do plano de controle][Control-Analysis] da configuração do teste.

A análise do plano de dados examina o caminho obtido por pacotes que atravessam de uma rede local (LAN ou rede virtual) para outra dentro de uma topologia. O caminho de dados entre duas redes locais não é necessariamente simétrico. Portanto, neste artigo, analisamos um caminho de encaminhamento de uma rede local para outra rede separada do caminho inverso.

## <a name="data-path-from-the-hub-vnet"></a>Caminho de dados da VNet do Hub

### <a name="path-to-the-spoke-vnet"></a>Caminho para a VNet do spoke

O emparelhamento de rede virtual (VNet) emula a funcionalidade de ponte de rede entre os dois VNets emparelhados. A saída de traceroute de uma VNet de Hub para uma VM na VNet do spoke é mostrada aqui:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

A figura a seguir mostra a exibição de conexão gráfica da VNet do Hub e a VNet do spoke da perspectiva do observador de rede do Azure:


![1][1]

### <a name="path-to-the-branch-vnet"></a>Caminho para a VNet da ramificação

A saída de traceroute de uma VNet de Hub para uma VM na VNet de ramificação é mostrada aqui:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

Nessas rotas, o primeiro salto é o gateway de VPN no gateway de VPN do Azure da VNet do Hub. O segundo salto é o gateway de VPN da VNet da ramificação. O endereço IP do gateway de VPN da VNet de ramificação não é anunciado na VNet do Hub. O terceiro salto é a VM na VNet da ramificação.

A figura a seguir mostra a exibição de conexão gráfica da VNet do Hub e a VNet da ramificação da perspectiva do observador de rede:

![2][2]

Para a mesma conexão, a figura a seguir mostra o modo de exibição de grade no observador de rede:

![3][3]

### <a name="path-to-on-premises-location-1"></a>Caminho para o local 1

A saída de traceroute de uma VNet de Hub para uma VM no local 1 é mostrada aqui:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Nessas rotas, o primeiro salto é o ponto de extremidade de túnel de gateway do Azure ExpressRoute para um roteador Microsoft Enterprise Edge (MSEE). O segundo e o terceiro saltos são o roteador da borda do cliente (CE) e os IPs de LAN locais 1. Esses endereços IP não são anunciados na VNet do Hub. O quarto salto é a VM no local 1.


### <a name="path-to-on-premises-location-2"></a>Caminho para o local 2

A saída de traceroute de uma VNet de Hub para uma VM no local 2 é mostrada aqui:

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

Nessas rotas, o primeiro salto é o ponto de extremidade de túnel de gateway do ExpressRoute para um MSEE. O segundo e o terceiro saltos são o roteador CE e os IPs de LAN locais 2. Esses endereços IP não são anunciados na VNet do Hub. O quarto salto é a VM no local 2.

### <a name="path-to-the-remote-vnet"></a>Caminho para a VNet remota

A saída de traceroute de uma VNet de Hub para uma VM na VNet remota é mostrada aqui:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

Nessas rotas, o primeiro salto é o ponto de extremidade de túnel de gateway do ExpressRoute para um MSEE. O segundo salto é o IP do gateway da VNet remota. O intervalo de IP do segundo salto não é anunciado na VNet do Hub. O terceiro salto é a VM na VNet remota.

## <a name="data-path-from-the-spoke-vnet"></a>Caminho de dados da VNet do spoke

A VNet do spoke compartilha a exibição de rede da VNet do Hub. Por meio do emparelhamento VNet, a VNet do spoke usa a conectividade do gateway remoto da VNet do Hub como se ela fosse conectada diretamente à VNet do spoke.

### <a name="path-to-the-hub-vnet"></a>Caminho para a VNet do Hub

A saída de traceroute da VNet do spoke para uma VM na VNet do hub é mostrada aqui:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>Caminho para a VNet da ramificação

A saída de traceroute da VNet do spoke para uma VM na VNet da ramificação é mostrada aqui:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

Nessas rotas, o primeiro salto é o gateway de VPN da VNet do Hub. O segundo salto é o gateway de VPN da VNet da ramificação. O endereço IP do gateway de VPN da VNet de ramificação não é anunciado na VNet hub/spoke. O terceiro salto é a VM na VNet da ramificação.

### <a name="path-to-on-premises-location-1"></a>Caminho para o local 1

A saída de traceroute da VNet do spoke para uma VM no local 1 é mostrada aqui:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Nessas rotas, o primeiro salto é o ponto de extremidade de túnel de gateway do ExpressRoute da VNet do hub para um MSEE. O segundo e o terceiro saltos são o roteador CE e os IPs de LAN locais 1. Esses endereços IP não são anunciados na VNet hub/spoke. O quarto salto é a VM no local 1.

### <a name="path-to-on-premises-location-2"></a>Caminho para o local 2

A saída de traceroute da VNet do spoke para uma VM no local 2 é mostrada aqui:


    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Nessas rotas, o primeiro salto é o ponto de extremidade de túnel de gateway do ExpressRoute da VNet do hub para um MSEE. O segundo e o terceiro saltos são o roteador CE e os IPs de LAN locais 2. Esses endereços IP não são anunciados no VNets hub/spoke. O quarto salto é a VM no local 2.

### <a name="path-to-the-remote-vnet"></a>Caminho para a VNet remota

A saída de traceroute da VNet do spoke para uma VM na VNet remota é mostrada aqui:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

Nessas rotas, o primeiro salto é o ponto de extremidade de túnel de gateway do ExpressRoute da VNet do hub para um MSEE. O segundo salto é o IP do gateway da VNet remota. O intervalo de IP do segundo salto não é anunciado na VNet hub/spoke. O terceiro salto é a VM na VNet remota.

## <a name="data-path-from-the-branch-vnet"></a>Caminho de dados da VNet da ramificação

### <a name="path-to-the-hub-vnet"></a>Caminho para a VNet do Hub

A saída de traceroute da VNet de ramificação para uma VM na VNet do hub é mostrada aqui:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

Nessas rotas, o primeiro salto é o gateway de VPN da VNet da ramificação. O segundo salto é o gateway de VPN da VNet do Hub. O endereço IP do gateway de VPN da VNet do Hub não é anunciado na VNet remota. O terceiro salto é a VM na VNet do Hub.

### <a name="path-to-the-spoke-vnet"></a>Caminho para a VNet do spoke

A saída de traceroute da VNet de ramificação para uma VM na VNet do spoke é mostrada aqui:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

Nessas rotas, o primeiro salto é o gateway de VPN da VNet da ramificação. O segundo salto é o gateway de VPN da VNet do Hub. O endereço IP do gateway de VPN da VNet do Hub não é anunciado na VNet remota. O terceiro salto é a VM na VNet do spoke.

### <a name="path-to-on-premises-location-1"></a>Caminho para o local 1

A saída de traceroute da VNet de ramificação para uma VM no local 1 é mostrada aqui:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

Nessas rotas, o primeiro salto é o gateway de VPN da VNet da ramificação. O segundo salto é o gateway de VPN da VNet do Hub. O endereço IP do gateway de VPN da VNet do Hub não é anunciado na VNet remota. O terceiro salto é o ponto de término do túnel VPN no roteador CE primário. O quarto salto é um endereço IP interno do local 1. Esse endereço IP de LAN não é anunciado fora do roteador CE. O quinto salto é a VM de destino no local 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Caminho para o local 2 e a VNet remota

Como discutimos na análise do plano de controle, a VNet da ramificação não tem visibilidade para o local 2 ou para a VNet remota por configuração de rede. Os seguintes resultados de ping são confirmados: 

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

## <a name="data-path-from-on-premises-location-1"></a>Caminho de dados do local 1

### <a name="path-to-the-hub-vnet"></a>Caminho para a VNet do Hub

A saída de traceroute do local 1 para uma VM na VNet do hub é mostrada aqui:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

Nessas rotas, os dois primeiros saltos fazem parte da rede local. O terceiro salto é a interface MSEE primária que enfrenta o roteador CE. O quarto salto é o gateway de ExpressRoute da VNet do Hub. O intervalo de IP do gateway de ExpressRoute da VNet do Hub não é anunciado para a rede local. O quinto salto é a VM de destino.

O observador de rede fornece apenas uma exibição centrada no Azure. Para uma perspectiva local, usamos o Azure Monitor de Desempenho de Rede. O Monitor de Desempenho de Rede fornece agentes que você pode instalar em servidores em redes fora do Azure para análise de caminho de dados.

A figura a seguir mostra a exibição de topologia da conectividade de VM local 1 para a VM na VNet do hub por meio do ExpressRoute:

![4][4]

Conforme discutido anteriormente, a configuração de teste usa uma VPN site a site como conectividade de backup para o ExpressRoute entre o local 1 e a VNet do Hub. Para testar o caminho de dados de backup, vamos induzir uma falha de link do ExpressRoute entre o roteador CE local 1 primário e o MSEE correspondente. Para induzir uma falha de link do ExpressRoute, desligue a interface CE que enfrenta o MSEE:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

A figura a seguir mostra a exibição de topologia da conectividade de VM local 1 para a VM na VNet do hub por meio da conectividade VPN site a site quando a conectividade do ExpressRoute está inoperante:

![5][5]

### <a name="path-to-the-spoke-vnet"></a>Caminho para a VNet do spoke

A saída de traceroute do local 1 para uma VM na VNet do spoke é mostrada aqui:

Vamos voltar à conectividade primária do ExpressRoute para fazer a análise do caminho de dados em direção à VNet do spoke:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Traga a conectividade primária do ExpressRoute 1 para o restante da análise de caminho de dados.

### <a name="path-to-the-branch-vnet"></a>Caminho para a VNet da ramificação

A saída de traceroute do local 1 para uma VM na VNet da ramificação é mostrada aqui:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>Caminho para o local 2

À medida que discutimos na [análise do plano de controle][Control-Analysis], o local 1 não tem visibilidade no local 2 de acordo com a configuração de rede. Os seguintes resultados de ping são confirmados: 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>Caminho para a VNet remota

A saída de traceroute do local 1 para uma VM na VNet remota é mostrada aqui:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>Caminho de dados do local 2

### <a name="path-to-the-hub-vnet"></a>Caminho para a VNet do Hub

A saída de traceroute do local 2 para uma VM na VNet do hub é mostrada aqui:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Caminho para a VNet do spoke

A saída de traceroute do local 2 para uma VM na VNet do spoke é mostrada aqui:

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Caminho para a VNet da ramificação, local 1 e a VNet remota

Conforme discutimos na [análise do plano de controle][Control-Analysis], o local 1 não tem visibilidade para a vnet do Branch, para o local 1 ou para a vnet remota por configuração de rede. 

## <a name="data-path-from-the-remote-vnet"></a>Caminho de dados da VNet remota

### <a name="path-to-the-hub-vnet"></a>Caminho para a VNet do Hub

A saída de traceroute da VNet remota para uma VM na VNet do hub é mostrada aqui:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Caminho para a VNet do spoke

A saída de traceroute da VNet remota para uma VM na VNet do spoke é mostrada aqui:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Caminho para a VNet de ramificação e local 2

À medida que discutimos na [análise do plano de controle][Control-Analysis], a vnet remota não tem visibilidade para a VNet da ramificação ou para o local 2 de acordo com a configuração de rede. 

### <a name="path-to-on-premises-location-1"></a>Caminho para o local 1

A saída de traceroute da VNet remota para uma VM no local 1 é mostrada aqui:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Conectividade de VPN site a site e de ExpressRoute em tandem

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN site a site por meio do ExpressRoute

Você pode configurar uma VPN site a site usando o emparelhamento da Microsoft ExpressRoute para trocar dados de forma privada entre sua rede local e o VNets do Azure. Com essa configuração, você pode trocar dados com confidencialidade, autenticidade e integridade. A troca de dados também é a anti-repetição. Para obter mais informações sobre como configurar uma VPN IPsec site a site no modo de túnel usando o emparelhamento da Microsoft do ExpressRoute, consulte [VPN site a site sobre o emparelhamento da Microsoft do expressroute][S2S-Over-ExR]. 

A principal limitação de configurar uma VPN site a site que usa o emparelhamento da Microsoft é a taxa de transferência. A taxa de transferência sobre o túnel IPsec é limitada pela capacidade do gateway de VPN. A taxa de transferência do gateway de VPN é inferior à taxa de transferência do ExpressRoute. Nesse cenário, o uso do túnel IPsec para tráfego altamente seguro e o uso de emparelhamento privado para todo o outro tráfego ajuda a otimizar a utilização da largura de banda do ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN site a site como um caminho de failover seguro para o ExpressRoute

O ExpressRoute serve como um par de circuitos redundantes para garantir a alta disponibilidade. Você pode configurar a conectividade do ExpressRoute com redundância geográfica em diferentes regiões do Azure. Além disso, conforme demonstrado em nossa configuração de teste, em uma região do Azure, você pode usar uma VPN site a site para criar um caminho de failover para a conectividade do ExpressRoute. Quando os mesmos prefixos são anunciados no ExpressRoute e em uma VPN site a site, o Azure prioriza o ExpressRoute. Para evitar o roteamento assimétrico entre o ExpressRoute e a VPN site a site, a configuração de rede local também deve ser reciprocada usando a conectividade de ExpressRoute antes de usar a conectividade VPN site a site.

Para obter mais informações sobre como configurar conexões coexistentes para o ExpressRoute e uma VPN site a site, consulte [ExpressRoute e coexistência site a site][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Estender a conectividade de back-end para spoke VNets e locais de filiais

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Conectividade de VNet do spoke usando o emparelhamento VNet

A arquitetura de VNet de Hub e spoke é amplamente usada. O Hub é uma VNet no Azure que atua como um ponto central de conectividade entre seu VNets de spoke e sua rede local. Os spokes são VNets que emparelham com o Hub e que você pode usar para isolar cargas de trabalho. O tráfego flui entre o datacenter local e o Hub por meio de uma conexão de ExpressRoute ou VPN. Para obter mais informações sobre a arquitetura, consulte [implementar uma topologia de rede hub-spoke no Azure][Hub-n-Spoke].

No emparelhamento VNet dentro de uma região, o spoke VNets pode usar gateways de VNet de Hub (gateways VPN e ExpressRoute) para se comunicar com redes remotas.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Conectividade de VNet de ramificação usando VPN site a site

Talvez você queira ramificar VNets, que estão em regiões diferentes, e redes locais para se comunicarem entre si por meio de uma VNet de Hub. A solução nativa do Azure para essa configuração é a conectividade VPN site a site usando uma VPN. Uma alternativa é usar uma NVA (solução de virtualização de rede) para roteamento no Hub.

Para obter mais informações, consulte [o que é o gateway de VPN?][VPN] e [implantar um NVA altamente disponível][Deploy-NVA].


## <a name="next-steps"></a>Passos seguintes

Consulte as [perguntas frequentes sobre o ExpressRoute][ExR-FAQ] para:
-   Saiba quantos circuitos do ExpressRoute você pode se conectar a um gateway de ExpressRoute.
-   Saiba quantos gateways do ExpressRoute você pode se conectar a um circuito do ExpressRoute.
-   Saiba mais sobre outros limites de escala do ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Exibição de conectividade do observador de rede de uma VNet do hub para uma VNet do spoke"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Exibição do observador de rede de conectividade de uma VNet de Hub para uma VNet de ramificação"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "Exibição em grade do observador de rede de conectividade de uma VNet de Hub para uma VNet de ramificação"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Monitor de Desempenho de Rede exibição de conectividade da VM local 1 para a VNet do hub por meio do ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Monitor de Desempenho de Rede exibição de conectividade da VM local 1 para a VNet do hub por meio de uma VPN site a site"

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


