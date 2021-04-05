---
title: 'Interoperabilidade em Azure : Análise de plano de dados'
description: Este artigo fornece a análise do plano de dados da configuração do teste que pode utilizar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site-to-site, e o espreitamento de rede virtual em Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4797fca5b4a756ca502147172e6f1590b9eacbe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234176"
---
# <a name="interoperability-in-azure--data-plane-analysis"></a>Interoperabilidade em Azure : Análise de plano de dados

Este artigo descreve a análise do plano de dados da [configuração][Setup]do teste . Também pode rever a configuração da [configuração][Configuration] do teste e a análise do plano de [controlo][Control-Analysis] da configuração do teste.

A análise de planos de dados examina o caminho percorrido por pacotes que atravessam de uma rede local (LAN ou rede virtual) para outra dentro de uma topologia. O caminho de dados entre duas redes locais não é necessariamente simétrico. Portanto, neste artigo, analisamos um caminho de encaminhamento de uma rede local para outra rede que está separada do caminho inverso.

## <a name="data-path-from-the-hub-vnet"></a>Caminho de dados do hub VNet

### <a name="path-to-the-spoke-vnet"></a>Caminho para o VNet falado

O espreitamento da rede virtual (VNet) imita a funcionalidade da ponte de rede entre os dois VNets que são espreitados. A saída traceroute de um hub VNet para um VM no VNet falado é mostrada aqui:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1     2 ms     1 ms     1 ms  10.11.30.4

Trace complete.
```

A seguinte figura mostra a vista de conexão gráfica do hub VNet e do VNet falado na perspetiva do Azure Network Watcher:


![1][1]

### <a name="path-to-the-branch-vnet"></a>Caminho para o ramo VNet

A saída traceroute de um hub VNet para um VM no ramo VNet é mostrada aqui:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1     1 ms     1 ms     1 ms  10.10.30.142
  2     *        *        *     Request timed out.
  3     2 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

Neste traço de rota, o primeiro salto é o gateway VPN em Azure VPN Gateway do hub VNet. O segundo salto é a porta de entrada VPN do ramo VNet. O endereço IP da porta de entrada VPN da sucursal VNet não é anunciado no hub VNet. O terceiro salto é o VM no ramo VNet.

A seguinte figura mostra a vista de conexão gráfica do hub VNet e do ramo VNet na perspetiva do Network Watcher:

![2][2]

Para a mesma ligação, a seguinte figura mostra a vista da grelha no Observador de Rede:

![3][3]

### <a name="path-to-on-premises-location-1"></a>Caminho para o local localização 1

A saída traceroute de um hub VNet para um VM no local Localização 1 é mostrada aqui:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1     2 ms     2 ms     2 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     2 ms     2 ms     2 ms  10.2.30.10

Trace complete.
```

Neste percurso, o primeiro salto é o ponto final do túnel de gateway Azure ExpressRoute para um Microsoft Enterprise Edge Router (MSEE). O segundo e terceiro lúpulo são o router de borda do cliente (CE) e o local 1 LAN IPs. Estes endereços IP não são anunciados no hub VNet. O quarto salto é o VM no local 1.


### <a name="path-to-on-premises-location-2"></a>Caminho para o local 2

A saída traceroute de um hub VNet para um VM no local Localização 2 é mostrada aqui:

```console
C:\Users\rb>tracert 10.1.31.10

Tracing route to 10.1.31.10 over a maximum of 30 hops

  1    76 ms    75 ms    75 ms  10.10.30.134
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    75 ms    75 ms    75 ms  10.1.31.10

Trace complete.
```

Neste traço de rota, o primeiro salto é o ponto final do túnel de gateway ExpressRoute para um MSEE. O segundo e terceiro lúpulo são o router CE e o local 2 LAN IPs. Estes endereços IP não são anunciados no hub VNet. O quarto salto é o VM no local 2.

### <a name="path-to-the-remote-vnet"></a>Caminho para o VNet remoto

A saída traceroute de um hub VNet para um VM no VNet remoto é mostrada aqui:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1     2 ms     2 ms     2 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3    69 ms    68 ms    69 ms  10.17.30.4

Trace complete.
```

Neste traço de rota, o primeiro salto é o ponto final do túnel de gateway ExpressRoute para um MSEE. O segundo salto é o IP de gateway do VNet remoto. A segunda gama ip de lúpulo não é publicitada no hub VNet. O terceiro salto é o VM no VNet remoto.

## <a name="data-path-from-the-spoke-vnet"></a>Caminho de dados do VNet falado

O VNet falado partilha a visão de rede do hub VNet. Através do estojo VNet, o VNet falado utiliza a conectividade de gateway remota do hub VNet como se estivesse diretamente ligado ao VNet falado.

### <a name="path-to-the-hub-vnet"></a>Caminho para o hub VNet

A saída traceroute do VNet falado para um VM no hub VNet é mostrada aqui:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet"></a>Caminho para o ramo VNet

A saída traceroute do VNet falado para um VM no ramo VNet é mostrada aqui:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms  10.10.30.142
  2     *        *        *     Request timed out.
  3     3 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

Neste traço de rota, o primeiro salto é a porta de entrada VPN do hub VNet. O segundo salto é a porta de entrada VPN do ramo VNet. O endereço IP da porta de entrada VPN da sucursal VNet não é anunciado dentro do hub/spoke VNet. O terceiro salto é o VM no ramo VNet.

### <a name="path-to-on-premises-location-1"></a>Caminho para o local localização 1

A saída traceroute do VNet falado para um VM no local Localização 1 é mostrada aqui:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1    24 ms     2 ms     3 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     3 ms     2 ms     2 ms  10.2.30.10

Trace complete.
```

Neste traço de rota, o primeiro salto é o ponto final do túnel expressRoute do hub VNet para um MSEE. O segundo e terceiro lúpulo são o router CE e o local 1 LAN IPs. Estes endereços IP não são anunciados no hub/spoke VNet. O quarto salto é o VM no local 1.

### <a name="path-to-on-premises-location-2"></a>Caminho para o local 2

A saída traceroute do VNet falado para um VM no local localização 2 é mostrada aqui:

```console
C:\Users\rb>tracert 10.1.31.10

Tracing route to 10.1.31.10 over a maximum of 30 hops

  1    76 ms    75 ms    76 ms  10.10.30.134
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    75 ms    75 ms    75 ms  10.1.31.10

Trace complete.
```

Neste traço de rota, o primeiro salto é o ponto final do túnel expressRoute do hub VNet para um MSEE. O segundo e terceiro lúpulo são o router CE e o local 2 LAN IPs. Estes endereços IP não são anunciados no hub/spoke VNets. O quarto salto é o VM no local 2.

### <a name="path-to-the-remote-vnet"></a>Caminho para o VNet remoto

A saída traceroute do VNet falado para um VM no VNet remoto é mostrada aqui:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1     2 ms     1 ms     1 ms  10.10.30.133
  2     *        *        *     Request timed out.
  3    71 ms    70 ms    70 ms  10.17.30.4

Trace complete.
```

Neste traço de rota, o primeiro salto é o ponto final do túnel expressRoute do hub VNet para um MSEE. O segundo salto é o IP de gateway do VNet remoto. A segunda gama ip de lúpulo não é publicitada no hub/spoke VNet. O terceiro salto é o VM no VNet remoto.

## <a name="data-path-from-the-branch-vnet"></a>Caminho de dados do ramo VNet

### <a name="path-to-the-hub-vnet"></a>Caminho para o hub VNet

A saída traceroute do ramo VNet para um VM no hub VNet é mostrada aqui:

```console
C:\Windows\system32>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     4 ms     3 ms     3 ms  10.10.30.4

Trace complete.
```

Neste traço de rota, o primeiro salto é a porta de entrada VPN do ramo VNet. O segundo salto é a porta de entrada VPN do hub VNet. O endereço IP da porta de entrada VPN do hub VNet não é anunciado no VNet remoto. O terceiro salto é o VM no hub VNet.

### <a name="path-to-the-spoke-vnet"></a>Caminho para o VNet falado

A saída traceroute do ramo VNet para um VM no VNet falado é mostrada aqui:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1     1 ms    <1 ms     1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     4 ms     3 ms     2 ms  10.11.30.4

Trace complete.
```

Neste traço de rota, o primeiro salto é a porta de entrada VPN do ramo VNet. O segundo salto é a porta de entrada VPN do hub VNet. O endereço IP da porta de entrada VPN do hub VNet não é anunciado no VNet remoto. O terceiro salto é o VM no VNet falado.

### <a name="path-to-on-premises-location-1"></a>Caminho para o local localização 1

A saída traceroute do ramo VNet para um VM no local Localização 1 é mostrada aqui:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     3 ms     2 ms     2 ms  10.2.30.125
  4     *        *        *     Request timed out.
  5     3 ms     3 ms     3 ms  10.2.30.10

Trace complete.
```

Neste traço de rota, o primeiro salto é a porta de entrada VPN do ramo VNet. O segundo salto é a porta de entrada VPN do hub VNet. O endereço IP da porta de entrada VPN do hub VNet não é anunciado no VNet remoto. O terceiro salto é o ponto de terminação do túnel VPN no router ce primário. O quarto salto é um endereço IP interno do local 1. Este endereço IP lan não é anunciado fora do router CE. O quinto salto é o destino VM no local local 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Caminho para o local localização 2 e o VNet remoto

Como discutimos na análise do plano de controlo, o ramo VNet não tem visibilidade nem para o local localização 2 ou para o VNet remoto pela configuração da rede. Os seguintes resultados do ping confirmam: 

```console
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
```

## <a name="data-path-from-on-premises-location-1"></a>Percurso de dados a partir do local localização 1

### <a name="path-to-the-hub-vnet"></a>Caminho para o hub VNet

A saída traceroute do local localização 1 para um VM no hub VNet é mostrada aqui:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5     2 ms     2 ms     2 ms  10.10.30.4

Trace complete.
```

Neste rasto, os dois primeiros lúpulos fazem parte da rede de instalações. O terceiro salto é a interface MSEE primária que enfrenta o router CE. O quarto salto é a porta de entrada ExpressRoute do hub VNet. A gama IP da porta de entrada ExpressRoute do hub VNet não é publicitada para a rede no local. O quinto salto é o destino VM.

O Network Watcher fornece apenas uma visão centrada no Azure. Para uma perspetiva no local, usamos o Monitor de Desempenho da Rede Azure. O Monitor de Desempenho da Rede fornece agentes que pode instalar em servidores em redes fora do Azure para análise de trajetórias de dados.

A seguinte figura mostra a vista topologia da conectividade localização 1 VM no local localização 1 VM para o VM no hub VNet via ExpressRoute:

![4][4]

Como discutido anteriormente, a configuração do teste utiliza uma VPN site-to-site como conectividade de backup para ExpressRoute entre o local localização 1 e o hub VNet. Para testar o caminho dos dados de backup, induziremos uma falha de ligação ExpressRoute entre o router CE principal localização 1 no local e o MSEE correspondente. Para induzir uma falha de ligação ExpressRoute, desligue a interface CE que enfrenta o MSEE:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3     3 ms     2 ms     3 ms  10.10.30.4

Trace complete.
```

A seguinte figura mostra a vista topologia da conectividade localização 1 VM no local do VM no hub VNet através da conectividade VPN local-local quando a conectividade ExpressRoute está em baixo:

![5][5]

### <a name="path-to-the-spoke-vnet"></a>Caminho para o VNet falado

A saída de traceroute do local localização 1 para um VM no VNet falado é mostrada aqui:

Vamos trazer de volta a conectividade primária ExpressRoute para fazer a análise do caminho de dados para o VNet falado:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5     3 ms     2 ms     2 ms  10.11.30.4

Trace complete.
```

Traga a conectividade primária expressRoute 1 para o restante da análise do percurso de dados.

### <a name="path-to-the-branch-vnet"></a>Caminho para o ramo VNet

A saída traceroute do local localização 1 para um VM no ramo VNet é mostrada aqui:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3     3 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

### <a name="path-to-on-premises-location-2"></a>Caminho para o local 2

Como discutimos na análise do plano de [controlo,][Control-Analysis]o local 1 não tem visibilidade para o local localização 2 por configuração de rede. Os seguintes resultados do ping confirmam: 

```console
C:\Users\rb>ping 10.1.31.10

Pinging 10.1.31.10 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.1.31.10:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```

### <a name="path-to-the-remote-vnet"></a>Caminho para o VNet remoto

A saída traceroute do local localização 1 para um VM no VNet remoto é mostrada aqui:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2     2 ms     5 ms     7 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5    69 ms    70 ms    69 ms  10.17.30.4

Trace complete.
```

## <a name="data-path-from-on-premises-location-2"></a>Percurso de dados a partir do local localização 2

### <a name="path-to-the-hub-vnet"></a>Caminho para o hub VNet

A saída traceroute do local localização 2 para um VM no hub VNet é mostrada aqui:

```console
C:\Windows\system32>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.31.3
  2    <1 ms    <1 ms    <1 ms  192.168.31.4
  3    <1 ms    <1 ms    <1 ms  192.168.31.22
  4     *        *        *     Request timed out.
  5    75 ms    74 ms    74 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-spoke-vnet"></a>Caminho para o VNet falado

A saída de traceroute do local localização 2 para um VM no VNet falado é mostrada aqui:

```console
C:\Windows\system32>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops
  1    <1 ms    <1 ms     1 ms  10.1.31.3
  2    <1 ms    <1 ms    <1 ms  192.168.31.0
  3    <1 ms    <1 ms    <1 ms  192.168.31.18
  4     *        *        *     Request timed out.
  5    75 ms    74 ms    74 ms  10.11.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Caminho para o ramo VNet, local localização 1 e o VNet remoto

Como discutimos na análise do plano de [controlo,][Control-Analysis]o local 1 não tem visibilidade para o ramo VNet, para o local localização 1, ou para o VNet remoto por configuração de rede. 

## <a name="data-path-from-the-remote-vnet"></a>Caminho de dados a partir do VNet remoto

### <a name="path-to-the-hub-vnet"></a>Caminho para o hub VNet

A saída traceroute do VNet remoto para um VM no hub VNet é mostrada aqui:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    65 ms    65 ms    65 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3    69 ms    68 ms    68 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-spoke-vnet"></a>Caminho para o VNet falado

A saída traceroute do VNet remoto para um VM no VNet falado é mostrada aqui:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1    67 ms    67 ms    67 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3    71 ms    69 ms    69 ms  10.11.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Caminho para o ramo VNet e local localização 2

Como discutimos na análise do [plano de controlo,][Control-Analysis]o VNet remoto não tem visibilidade para o ramo VNet ou para o local localização 2 por configuração de rede. 

### <a name="path-to-on-premises-location-1"></a>Caminho para o local localização 1

A saída traceroute do VNet remoto para um VM no local Localização 1 é mostrada aqui:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1    67 ms    67 ms    67 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    69 ms    69 ms    69 ms  10.2.30.10

Trace complete.
```

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute e conectividade VPN site-to-site em conjunto

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN site-to-site sobre ExpressRoute

Pode configurar uma VPN site-to-site utilizando o ExpressRoute Microsoft olhando para trocar dados privados entre a sua rede no local e os seus VNets Azure. Com esta configuração, pode trocar dados com confidencialidade, autenticidade e integridade. A troca de dados também é anti-repetição. Para obter mais informações sobre como configurar um IPsec VPN site-to-site no modo túnel utilizando o olho da Microsoft ExpressRoute, consulte [o VPN site-to-site sobre o expressRoute Microsoft olhando][S2S-Over-ExR]. 

A limitação primária de configurar uma VPN site-to-site que utiliza o esprevamento da Microsoft é a produção. A entrada sobre o túnel IPsec é limitada pela capacidade de gateway VPN. A entrada de gateway VPN é inferior à produção expressRoute. Neste cenário, a utilização do túnel IPsec para tráfego altamente seguro e a utilização de um espremia privado para todos os outros tráfegos ajuda a otimizar a utilização da largura de banda ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN site-to-site como um caminho de falha seguro para ExpressRoute

O ExpressRoute serve como um par de circuitos redundantes para garantir uma elevada disponibilidade. Pode configurar a conectividade ExpressRoute geo-redundante em diferentes regiões do Azure. Além disso, como demonstrado na nossa configuração de teste, dentro de uma região de Azure, você pode usar uma VPN site-to-site para criar um caminho de failover para a sua conectividade ExpressRoute. Quando os mesmos prefixos são anunciados tanto sobre o ExpressRoute como sobre uma VPN site-to-site, a Azure prioriza o ExpressRoute. Para evitar o encaminhamento assimétrico entre o ExpressRoute e a VPN local-a-local, a configuração da rede no local também deve retribuir utilizando a conectividade ExpressRoute antes de utilizar a conectividade VPN site-to-site.

Para obter mais informações sobre como configurar as ligações coexistindo para o ExpressRoute e uma VPN local-a-local, consulte [o ExpressRoute e a coexistência local- local.][ExR-S2S-CoEx]

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Alargar a conectividade de back-end a VNets falados e localizações de filiais

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Conectividade falada VNet usando o peering VNet

Hub e a arquitetura VNet falada é amplamente usada. O hub é um VNet em Azure que funciona como um ponto central de conectividade entre os seus VNets falados e a sua rede no local. Os porta-vozes são VNets que se juntam ao hub, e que você pode usar para isolar cargas de trabalho. O tráfego flui entre o datacenter no local e o hub através de uma ligação ExpressRoute ou VPN. Para obter mais informações sobre a arquitetura, consulte [Implementar uma topologia de rede de eixos em Azure.][Hub-n-Spoke]

Em VNet espreitando dentro de uma região, os VNets falados podem usar gateways hub VNet (ambos gateways VPN e ExpressRoute) para comunicar com redes remotas.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Conectividade branch VNet utilizando VPN site-to-site

Você pode querer VNets de ramo, que estão em diferentes regiões, e redes no local para comunicar uns com os outros através de um hub VNet. A solução Azure nativa para esta configuração é a conectividade VPN local-a-local utilizando uma VPN. Uma alternativa é utilizar um aparelho virtual de rede (NVA) para encaminhamento no centro.

Para mais informações, veja [o que é VPN Gateway e][VPN] [implemente um NVA altamente disponível][Deploy-NVA].


## <a name="next-steps"></a>Passos seguintes

Consulte as [FAQ expressRoute][ExR-FAQ] para:
-   Saiba quantos circuitos ExpressRoute pode ligar a um gateway ExpressRoute.
-   Saiba quantos gateways ExpressRoute pode ligar a um circuito ExpressRoute.
-   Saiba mais sobre outros limites de escala do ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Visão do Observador de Rede da conectividade de um hub VNet para um VNet falado"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Visão do Observador de Rede da conectividade de um hub VNet para um VNet de ramo"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "Vista da grelha do Observador de Rede da conectividade de um hub VNet para um VNet de ramo"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Monitor de desempenho de rede vista da conectividade da localização 1 VM para o hub VNet via ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Monitor de desempenho de rede vista da conectividade da localização 1 VM para o hub VNet através de uma VPN site-to-site"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[Configuration]: ./connectivty-interoperability-configuration.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: ./connectivty-interoperability-configuration.md
[Control-Analysis]: ./connectivty-interoperability-control-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md