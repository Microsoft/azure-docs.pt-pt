---
title: 'Gateway Azure VPN: Sobre o encaminhamento P2S'
description: Saiba mais sobre o encaminhamento VPN Azure Point-to-Site para diferentes sistemas operativos, protocolos de acesso remoto e configurações de rede virtual.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 0b9b8ba555cddd56c49c750709e69ec180291c95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91827204"
---
# <a name="about-point-to-site-vpn-routing"></a>Acerca do encaminhamento VPN de Ponto a Site

Este artigo ajuda-o a entender como o encaminhamento VPN Azure Point-to-Site se comporta. O comportamento de encaminhamento P2S VPN depende do cliente OS, do protocolo utilizado para a ligação VPN e de como as redes virtuais (VNets) estão ligadas entre si.

A Azure suporta atualmente dois protocolos para acesso remoto, IKEv2 e SSTP. O IKEv2 é suportado em muitos sistemas operativos clientes, incluindo Windows, Linux, macOS, Android e iOS. O SSTP só é suportado no Windows. Se fizer uma alteração na topologia da sua rede e tiver clientes Windows VPN, o pacote de clientes VPN para clientes Windows deve ser descarregado e instalado novamente para que as alterações sejam aplicadas ao cliente.

> [!NOTE]
> Este artigo aplica-se apenas ao IKEv2.
>

## <a name="about-the-diagrams"></a><a name="diagrams"></a>Sobre os diagramas

Há uma série de diagramas diferentes neste artigo. Cada secção mostra uma topologia ou configuração diferente. Para efeitos deste artigo, as ligações Site-to-Site (S2S) e VNet-to-VNet funcionam da mesma forma, uma vez que ambas são túneis IPsec. Todos os gateways VPN neste artigo são baseados em rotas.

## <a name="one-isolated-vnet"></a><a name="isolatedvnet"></a>Um VNet isolado

A ligação de gateway VPN ponto-a-local neste exemplo é para um VNet que não está conectado ou espreitado com qualquer outra rede virtual (VNet1). Neste exemplo, os clientes podem aceder ao VNet1.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/isolated.jpg" alt-text="Encaminhamento VNet isolado" lightbox="./media/vpn-gateway-about-point-to-site-routing/isolated.jpg":::

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Clientes do Windows podem aceder ao VNet1

* Clientes não Windows podem aceder ao VNet1

## <a name="multiple-peered-vnets"></a><a name="multipeered"></a>Múltiplos VNets espreitados

Neste exemplo, a ligação de gateway VPN ponto-a-local é para VNet1. VNet1 é espremiado com VNet2. VNet 2 é espremiado com VNet3. VNet1 é espremiado com VNet4. Não existe um olhar direto entre o VNet1 e o VNet3. O VNet1 tem "Allow gateway transit" e o VNet2 e o VNet4 têm "Use gateways remotos" ativados.

Os clientes que usam o Windows podem aceder diretamente a VNets, mas o cliente VPN deve ser descarregado novamente se forem feitas alterações ao estojo VNet ou à topologia da rede. Os clientes não-Windows podem aceder diretamente a VNets. O acesso não é transitório e limita-se apenas a VNets diretamente espreitados.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple.jpg" alt-text="Encaminhamento VNet isolado" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple.jpg":::

### <a name="address-space"></a>Espaço de endereço:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes windows podem aceder a VNet1, VNet2 e VNet4, mas o cliente VPN deve ser descarregado novamente para que quaisquer alterações de topologia entrem em vigor.

* Os clientes não Windows podem aceder a VNet1, VNet2 e VNet4

## <a name="multiple-vnets-connected-using-an-s2s-vpn"></a><a name="multis2s"></a>Vários VNets ligados usando uma VPN S2S

Neste exemplo, a ligação de gateway VPN ponto-a-local é para VNet1. O VNet1 está ligado ao VNet2 utilizando uma ligação VPN site-to-site. O VNet2 está ligado ao VNet3 utilizando uma ligação VPN site-to-site. Não existe um espreitamento direto ou ligação VPN site-to-site entre VNet1 e VNet3. Todas as ligações Site-To-Site não estão a executar BGP para encaminhamento.

Os clientes que usam o Windows, ou outro SISTEMA suportado, só podem aceder ao VNet1. Para aceder a VNets adicionais, o BGP deve ser utilizado.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple-s2s.jpg" alt-text="Encaminhamento VNet isolado" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple-s2s.jpg":::

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes do Windows só podem aceder ao VNet1

* Os clientes não Windows só podem aceder ao VNet1

## <a name="multiple-vnets-connected-using-an-s2s-vpn-bgp"></a><a name="multis2sbgp"></a>Vários VNets ligados com uma VPN S2S (BGP)

Neste exemplo, a ligação de gateway VPN ponto-a-local é para VNet1. O VNet1 está ligado ao VNet2 utilizando uma ligação VPN site-to-site. O VNet2 está ligado ao VNet3 utilizando uma ligação VPN site-to-site. Não existe um espreitamento direto ou ligação VPN site-to-site entre VNet1 e VNet3. Todas as ligações Site-To-Site estão a executar BGP para encaminhamento.

Os clientes que usam o Windows, ou outro SISTEMA suportado, podem aceder a todos os VNets que estão ligados através de uma ligação VPN site-to-site, mas as rotas para VNets conectados têm de ser adicionadas manualmente aos clientes windows.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple-bgp.jpg" alt-text="Encaminhamento VNet isolado" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple-bgp.jpg":::

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes windows: 10.1.0.0/16

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes do Windows podem aceder a VNet1, VNet2 e VNet3, mas as rotas para VNet2 e VNet3 terão de ser adicionadas manualmente.

* Os clientes não Windows podem aceder a VNet1, VNet2 e VNet3

## <a name="one-vnet-and-a-branch-office"></a><a name="vnetbranch"></a>Um VNet e uma sucursal

Neste exemplo, a ligação de gateway VPN ponto-a-local é para VNet1. O VNet1 não está conectado/ espreitado com qualquer outra rede virtual, mas está ligado a um site no local através de uma ligação VPN site-to-site que não está a executar BGP.

Os clientes windows e não-Windows só podem aceder ao VNet1.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/branch-office.jpg" alt-text="Encaminhamento VNet isolado" lightbox="./media/vpn-gateway-about-point-to-site-routing/branch-office.jpg":::

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Clientes do Windows só podem aceder ao VNet1

* Os clientes não Windows só podem aceder ao VNet1

## <a name="one-vnet-and-a-branch-office-bgp"></a><a name="vnetbranchbgp"></a>Um VNet e uma sucursal (BGP)

Neste exemplo, a ligação de gateway VPN ponto-a-local é para VNet1. O VNet1 não está conectado ou espremiado com qualquer outra rede virtual, mas está ligado a um site no local (Site1) através de uma ligação VPN site-to-site que executa BGP.

Os clientes do Windows podem aceder ao VNet e à sucursal (Site1), mas as rotas para o Site1 devem ser adicionadas manualmente ao cliente. Os clientes não-Windows podem aceder ao VNet, bem como à sucursal no local.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/branch-bgp.jpg" alt-text="Encaminhamento VNet isolado" lightbox="./media/vpn-gateway-about-point-to-site-routing/branch-bgp.jpg":::

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes do Windows podem aceder ao VNet1 e ao Site1, mas as rotas para o Site1 terão de ser adicionadas manualmente.

* Os clientes não Windows podem aceder ao VNet1 e ao Site1.


## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office"></a><a name="multivnets2sbranch"></a>Vários VNets ligados usando S2S e uma sucursal

Neste exemplo, a ligação de gateway VPN ponto-a-local é para VNet1. O VNet1 está ligado ao VNet2 utilizando uma ligação VPN site-to-site. O VNet2 está ligado ao VNet3 utilizando uma ligação VPN site-to-site. Não existe um perspório direto ou um túnel VPN local entre as redes VNet1 e VNet3. O VNet3 está ligado a uma sucursal (Site1) utilizando uma ligação VPN site-to-site. Todas as ligações VPN não estão a funcionar BGP.

Todos os clientes podem aceder apenas ao VNet1.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multi-branch.jpg" alt-text="Encaminhamento VNet isolado" lightbox="./media/vpn-gateway-about-point-to-site-routing/multi-branch.jpg":::

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas aos clientes não Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes windows só podem aceder ao VNet1

* Os clientes não Windows só podem aceder ao VNet1

## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office-bgp"></a><a name="multivnets2sbranchbgp"></a>Vários VNets ligados com S2S e uma sucursal (BGP)

Neste exemplo, a ligação de gateway VPN ponto-a-local é para VNet1. O VNet1 está ligado ao VNet2 utilizando uma ligação VPN site-to-site. O VNet2 está ligado ao VNet3 utilizando uma ligação VPN site-to-site. Não existe um perspório direto ou um túnel VPN local entre as redes VNet1 e VNet3. O VNet3 está ligado a uma sucursal (Site1) utilizando uma ligação VPN site-to-site. Todas as ligações VPN estão a funcionar BGP.

Os clientes que utilizam o Windows podem aceder a VNets e sites que estejam ligados através de uma ligação VPN site-to-site, mas as rotas para VNet2, VNet3 e Site1 devem ser adicionadas manualmente ao cliente. Os clientes não Windows podem aceder a VNets e sites que estejam conectados utilizando uma ligação VPN site-to-site sem qualquer intervenção manual. O acesso é transitório, e os clientes podem aceder a recursos em todos os VNets e sites conectados (no local).

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multi-branch-bgp.jpg" alt-text="Encaminhamento VNet isolado" lightbox="./media/vpn-gateway-about-point-to-site-routing/multi-branch-bgp.jpg":::

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas aos clientes não Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes windows podem aceder a VNet1, VNet2, VNet3 e Site1, mas as rotas para VNet2, VNet3 e Site1 devem ser adicionadas manualmente ao cliente.

* Os clientes não Windows podem aceder a VNet1, Vnet2, VNet3 e Site1.

## <a name="next-steps"></a>Passos seguintes

Consulte [Criar uma VPN P2S utilizando o portal Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para começar a criar o seu P2S VPN.
