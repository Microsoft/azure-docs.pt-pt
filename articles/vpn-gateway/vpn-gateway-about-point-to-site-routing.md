---
title: Sobre o roteamento de ponto a site do Azure | Microsoft Docs
description: Este artigo ajuda você a entender como o roteamento de VPN ponto a site se comporta.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/08/2019
ms.author: anzaman
ms.openlocfilehash: cb5969ccb4ee9780b597326a3811395c3b7d9971
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168469"
---
# <a name="about-point-to-site-vpn-routing"></a>Acerca do encaminhamento VPN de Ponto a Site

Este artigo ajuda você a entender como o roteamento de VPN ponto a site do Azure se comporta. O comportamento de roteamento de VPN P2S depende do sistema operacional do cliente, do protocolo usado para a conexão VPN e de como as redes virtuais (VNets) estão conectadas entre si.

Atualmente, o Azure dá suporte a dois protocolos para acesso remoto, IKEv2 e SSTP. O IKEv2 tem suporte em muitos sistemas operacionais clientes, incluindo Windows, Linux, MacOS, Android e iOS. O SSTP só tem suporte no Windows. Se você fizer uma alteração na topologia da rede e tiver clientes VPN do Windows, o pacote do cliente VPN para clientes Windows deverá ser baixado e instalado novamente para que as alterações sejam aplicadas ao cliente.

> [!NOTE]
> Este artigo se aplica somente a IKEv2.
>

## <a name="diagrams"></a>Sobre os diagramas

Há vários diagramas diferentes neste artigo. Cada seção mostra uma topologia ou configuração diferente. Para os fins deste artigo, as conexões site a site (S2S) e VNet a VNet funcionam da mesma maneira, pois ambas são túneis IPsec. Todos os gateways de VPN neste artigo são baseados em rota.

## <a name="isolatedvnet"></a>Uma VNet isolada

A conexão de gateway de VPN ponto a site neste exemplo é para uma VNet que não está conectada ou emparelhada com qualquer outra rede virtual (VNet1). Neste exemplo, os clientes podem acessar o VNet1.

(./media/vpn-gateway-about-point-to-site-routing/1.jpg "Roteamento de vnet") isolado de ![Roteamento de rede virtual isolada]

### <a name="address-space"></a>Espaço de endereços

* VNet1:10.1.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas a clientes Windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes do Windows podem acessar o VNet1

* Os clientes não Windows podem acessar o VNet1

## <a name="multipeered"></a>Vários VNets emparelhados

Neste exemplo, a conexão de gateway de VPN ponto a site é para VNet1. VNet1 é emparelhado com VNet2. A VNet 2 é emparelhada com VNet3. VNet1 é emparelhado com VNet4. Não há emparelhamento direto entre VNet1 e VNet3. VNet1 tem "permitir trânsito de gateway" e VNet2 tem "usar gateways remotos" habilitado.

Os clientes que usam o Windows podem acessar VNets emparelhadas diretamente, mas o cliente VPN deve ser baixado novamente se alguma alteração for feita no emparelhamento VNet ou na topologia de rede. Os clientes não Windows podem acessar VNets emparelhadas diretamente. O acesso não é transitiva e é limitado apenas a VNets emparelhadas diretamente.

![vários emparelhados VNets](./media/vpn-gateway-about-point-to-site-routing/2.jpg "vários VNets emparelhados")

### <a name="address-space"></a>Espaço de endereço:

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* VNet4:10.4.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas a clientes Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes do Windows podem acessar VNet1, VNet2 e VNet4, mas o cliente VPN deve ser baixado novamente para que as alterações de topologia entrem em vigor.

* Os clientes não Windows podem acessar VNet1, VNet2 e VNet4

## <a name="multis2s"></a>Vários VNets conectados usando uma VPN S2S

Neste exemplo, a conexão de gateway de VPN ponto a site é para VNet1. VNet1 é conectado ao VNet2 usando uma conexão VPN site a site. VNet2 é conectado ao VNet3 usando uma conexão VPN site a site. Não há nenhuma conexão de VPN site a site ou emparelhamento direto entre VNet1 e VNet3. Todas as conexões site a site não estão executando o BGP para roteamento.

Os clientes que usam o Windows, ou outro sistema operacional com suporte, só podem acessar VNet1. Para acessar VNets adicionais, o BGP deve ser usado.

![vários VNets e S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "vários VNets e S2S")

### <a name="address-space"></a>Espaço de endereços

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas a clientes Windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes Windows só podem acessar VNet1

* Os clientes não Windows podem acessar somente VNet1

## <a name="multis2sbgp"></a>Vários VNets conectados usando uma VPN S2S (BGP)

Neste exemplo, a conexão de gateway de VPN ponto a site é para VNet1. VNet1 é conectado ao VNet2 usando uma conexão VPN site a site. VNet2 é conectado ao VNet3 usando uma conexão VPN site a site. Não há nenhuma conexão de VPN site a site ou emparelhamento direto entre VNet1 e VNet3. Todas as conexões site a site estão executando o BGP para roteamento.

Os clientes que usam o Windows, ou outro sistema operacional com suporte, podem acessar todos os VNets conectados usando uma conexão VPN site a site, mas as rotas para VNets conectadas precisam ser adicionadas manualmente aos clientes Windows.

![vários VNets e S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "vários VNETS e S2S BGP")

### <a name="address-space"></a>Espaço de endereços

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas a clientes Windows: 10.1.0.0/16

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes do Windows podem acessar VNet1, VNet2 e VNet3, mas as rotas para VNet2 e VNet3 precisarão ser adicionadas manualmente.

* Os clientes não Windows podem acessar VNet1, VNet2 e VNet3

## <a name="vnetbranch"></a>Uma VNet e uma filial

Neste exemplo, a conexão de gateway de VPN ponto a site é para VNet1. O VNet1 não está conectado/emparelhado com nenhuma outra rede virtual, mas está conectado a um site local por meio de uma conexão VPN site a site que não está executando o BGP.

Os clientes Windows e não Windows só podem acessar VNet1.

![Roteamento com uma vnet e um](./media/vpn-gateway-about-point-to-site-routing/5.jpg "Roteamento de filial com uma vnet e uma filial")

### <a name="address-space"></a>Espaço de endereços

* VNet1:10.1.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas a clientes Windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes Windows podem acessar apenas VNet1

* Os clientes não Windows podem acessar somente VNet1

## <a name="vnetbranchbgp"></a>Uma VNet e uma filial (BGP)

Neste exemplo, a conexão de gateway de VPN ponto a site é para VNet1. O VNet1 não está conectado ou emparelhado com qualquer outra rede virtual, mas está conectado a um site local (site1) por meio de uma conexão VPN site a site que executa o BGP.

Os clientes do Windows podem acessar a VNet e a filial (site1), mas as rotas para site1 devem ser adicionadas manualmente ao cliente. Os clientes não Windows podem acessar a VNet, bem como a filial local.

![uma vnet e uma filial (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "uma vnet e uma filial")

### <a name="address-space"></a>Espaço de endereços

* VNet1:10.1.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas a clientes Windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes do Windows podem acessar VNet1 e site1, mas as rotas para site1 precisarão ser adicionadas manualmente.

* Os clientes não Windows podem acessar VNet1 e site1.


## <a name="multivnets2sbranch"></a>Vários VNets conectados usando S2S e uma filial

Neste exemplo, a conexão de gateway de VPN ponto a site é para VNet1. VNet1 é conectado ao VNet2 usando uma conexão VPN site a site. VNet2 é conectado ao VNet3 usando uma conexão VPN site a site. Não há nenhum túnel de VPN site a site ou emparelhamento direto entre as redes VNet1 e VNet3. O VNet3 é conectado a uma filial (site1) usando uma conexão VPN site a site. Todas as conexões VPN não estão executando o BGP.

Todos os clientes podem acessar apenas VNet1.

![multi-VNET S2S e filiais](./media/vpn-gateway-about-point-to-site-routing/7.jpg "multi-vnet S2S e Branch Office")

### <a name="address-space"></a>Espaço de endereços

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Roteia clientes adicionados: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes Windows só podem acessar VNet1

* Os clientes não Windows podem acessar somente VNet1

## <a name="multivnets2sbranchbgp"></a>Vários VNets conectados usando S2S e uma filial (BGP)

Neste exemplo, a conexão de gateway de VPN ponto a site é para VNet1. VNet1 é conectado ao VNet2 usando uma conexão VPN site a site. VNet2 é conectado ao VNet3 usando uma conexão VPN site a site. Não há nenhum túnel de VPN site a site ou emparelhamento direto entre as redes VNet1 e VNet3. O VNet3 é conectado a uma filial (site1) usando uma conexão VPN site a site. Todas as conexões VPN estão executando o BGP.

Os clientes que usam o Windows podem acessar VNets e sites que estão conectados usando uma conexão VPN site a site, mas as rotas para VNet2, VNet3 e site1 devem ser adicionadas manualmente ao cliente. Os clientes não Windows podem acessar VNets e sites que estão conectados usando uma conexão VPN site a site sem nenhuma intervenção manual. O acesso é transitiva e os clientes podem acessar recursos em todos os VNets e sites conectados (local).

![multi-VNET S2S e filiais](./media/vpn-gateway-about-point-to-site-routing/8.jpg "multi-vnet S2S e Branch Office")

### <a name="address-space"></a>Espaço de endereços

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Roteia clientes adicionados: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes do Windows podem acessar VNet1, VNet2, VNet3 e site1, mas as rotas para VNet2, VNet3 e site1 devem ser adicionadas manualmente ao cliente.

* Os clientes não Windows podem acessar VNet1, Vnet2, VNet3 e site1.

## <a name="next-steps"></a>Passos seguintes

Consulte [criar uma VPN P2S usando o portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para começar a criar sua VPN P2S.
