---
title: 'Azure VPN Gateway: Sobre o encaminhamento P2S'
description: Este artigo ajuda-o a entender como o encaminhamento de VPN ponto-a-local se comporta.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: anzaman
ms.openlocfilehash: 4821f2eb694a36cf0570008b3e62ce39999c58d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80239752"
---
# <a name="about-point-to-site-vpn-routing"></a>Acerca do encaminhamento VPN de Ponto a Site

Este artigo ajuda-o a entender como o encaminhamento VPN Azure Point-to-Site se comporta. O comportamento de encaminhamento p2S VPN depende do cliente OS, do protocolo utilizado para a ligação VPN e da forma como as redes virtuais (VNets) estão ligadas entre si.

Atualmente, a Azure suporta dois protocolos de acesso remoto, IKEv2 e SSTP. O IKEv2 é suportado em muitos sistemas operativos de clientes, incluindo Windows, Linux, MacOS, Android e iOS. O SSTP só é suportado no Windows. Se fizer uma alteração à topoologia da sua rede e tiver clientes VPN do Windows, o pacote de clientes VPN para clientes Windows deve ser descarregado e instalado novamente para que as alterações sejam aplicadas ao cliente.

> [!NOTE]
> Este artigo aplica-se apenas ao IKEv2.
>

## <a name="about-the-diagrams"></a><a name="diagrams"></a>Sobre os diagramas

Há uma série de diagramas diferentes neste artigo. Cada secção mostra uma topologia ou configuração diferente. Para efeitos deste artigo, as ligações Site-to-Site (S2S) e VNet-to-VNet funcionam da mesma forma, uma vez que ambos são túneis IPsec. Todos os gateways VPN neste artigo são baseados em rotas.

## <a name="one-isolated-vnet"></a><a name="isolatedvnet"></a>Uma VNet isolada

A ligação de gateway Point-to-Site VPN neste exemplo é para um VNet que não esteja ligado ou empeerado com qualquer outra rede virtual (VNet1). Neste exemplo, os clientes podem aceder ao VNet1.

![encaminhamento isolado vNet](./media/vpn-gateway-about-point-to-site-routing/1.jpg "encaminhamento isolado vNet")

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Acesso

* Clientes windows podem aceder ao VNet1

* Clientes não Windows podem aceder ao VNet1

## <a name="multiple-peered-vnets"></a><a name="multipeered"></a>VNets com múltiplas espreitadas

Neste exemplo, a ligação de gateway Point-to-Site VPN é para VNet1. VNet1 é espreitar com VNet2. VNet 2 é espreitado com VNet3. VNet1 é espreitar com VNet4. Não há um epeering direto entre vNet1 e VNet3. A VNet1 tem "Allow gateway transit" e vNet2 e VNet4 têm "Use gateways remotos" ativados.

Os clientes que utilizam o Windows podem aceder diretamente a VNets com par, mas o cliente VPN deve ser descarregado novamente se forem feitas alterações para o peering VNet ou para a topologia da rede. Os clientes não Windows podem aceder diretamente a VNets. O acesso não é transitório e limita-se apenas a VNets diretamente espreitados.

![VNets multipares](./media/vpn-gateway-about-point-to-site-routing/2.jpg "VNets multipares")

### <a name="address-space"></a>Espaço de endereço:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 10.2.0.0.0/16, 10.4.0.0.0/16, 192.168.0.0/24

### <a name="access"></a>Acesso

* Os clientes windows podem aceder a VNet1, VNet2 e VNet4, mas o cliente VPN deve ser descarregado novamente para quaisquer alterações de topologia para fazer efeito.

* Clientes não Windows podem aceder a VNet1, VNet2 e VNet4

## <a name="multiple-vnets-connected-using-an-s2s-vpn"></a><a name="multis2s"></a>Múltiplos VNets conectados usando uma VPN S2S

Neste exemplo, a ligação de gateway Point-to-Site VPN é para VNet1. O VNet1 está ligado ao VNet2 utilizando uma ligação VPN site-to-site. O VNet2 está ligado ao VNet3 utilizando uma ligação VPN site-to-site. Não existe uma ligação VPN direta ou local-a-local entre vNet1 e VNet3. Todas as ligações Site-to-Site não estão a executar BGP para o encaminhamento.

Os clientes que usam o Windows, ou outro SISTEMA suportado, só podem aceder ao VNet1. Para aceder a VNets adicionais, o BGP deve ser utilizado.

![múltiplos VNets e S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "múltiplos VNets e S2S")

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 10.2.0.0.0/16, 192.168.0.0/24

### <a name="access"></a>Acesso

* Os clientes windows só podem aceder ao VNet1

* Clientes não Windows só podem aceder ao VNet1

## <a name="multiple-vnets-connected-using-an-s2s-vpn-bgp"></a><a name="multis2sbgp"></a>Múltiplos VNets ligados usando um S2S VPN (BGP)

Neste exemplo, a ligação de gateway Point-to-Site VPN é para VNet1. O VNet1 está ligado ao VNet2 utilizando uma ligação VPN site-to-site. O VNet2 está ligado ao VNet3 utilizando uma ligação VPN site-to-site. Não existe uma ligação VPN direta ou local-a-local entre vNet1 e VNet3. Todas as ligações Site-to-Site estão a executar BGP para encaminhamento.

Os clientes que utilizam o Windows, ou outro SISTEMA suportado, podem aceder a todos os VNets que estejam ligados através de uma ligação VPN site-to-site, mas as rotas para VNets conectados têm de ser adicionadas manualmente aos clientes do Windows.

![múltiplos VNets e S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "múltiplos VNets e S2S BGP")

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes windows: 10.1.0.0/16

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 10.2.0.0.0/16, 10.3.0.0.0/16, 192.168.0.0/24

### <a name="access"></a>Acesso

* Os clientes windows podem aceder a VNet1, VNet2 e VNet3, mas as rotas para VNet2 e VNet3 terão de ser adicionadas manualmente.

* Clientes não Windows podem aceder a VNet1, VNet2 e VNet3

## <a name="one-vnet-and-a-branch-office"></a><a name="vnetbranch"></a>Um VNet e uma filial

Neste exemplo, a ligação de gateway Point-to-Site VPN é para VNet1. O VNet1 não está ligado/empeerado com qualquer outra rede virtual, mas está ligado a um site no local através de uma ligação VPN site-to-site que não está a funcionar bGP.

Os clientes Windows e não Windows só podem aceder ao VNet1.

![encaminhamento com um VNet e uma sucursal](./media/vpn-gateway-about-point-to-site-routing/5.jpg "encaminhamento com um VNet e uma sucursal")

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* Sítio1: 10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Acesso

* Os clientes windows só podem aceder ao VNet1

* Clientes não Windows só podem aceder ao VNet1

## <a name="one-vnet-and-a-branch-office-bgp"></a><a name="vnetbranchbgp"></a>Uma VNet e uma sucursal (BGP)

Neste exemplo, a ligação de gateway Point-to-Site VPN é para VNet1. O VNet1 não está ligado ou embebedado com qualquer outra rede virtual, mas está ligado a um site no local (Site1) através de uma ligação VPN site-to-site que executa bGP.

Os clientes windows podem aceder ao VNet e à sucursal (Site1), mas as rotas para o Site1 devem ser adicionadas manualmente ao cliente. Os clientes não Windows podem aceder ao VNet, bem como à sucursal no local.

![uma VNet e uma sucursal (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "um VNet e uma sucursal")

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* Sítio1: 10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Acesso

* Os clientes do Windows podem aceder ao VNet1 e ao Site1, mas as rotas para o Site1 terão de ser adicionadas manualmente.

* Os clientes não Windows podem aceder ao VNet1 e ao Site1.


## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office"></a><a name="multivnets2sbranch"></a>Múltiplos VNets conectados usando S2S e uma sucursal

Neste exemplo, a ligação de gateway Point-to-Site VPN é para VNet1. O VNet1 está ligado ao VNet2 utilizando uma ligação VPN site-to-site. O VNet2 está ligado ao VNet3 utilizando uma ligação VPN site-to-site. Não existe um desvio direto ou um túnel VPN local-a-local entre as redes VNet1 e VNet3. O VNet3 está ligado a uma sucursal (Site1) utilizando uma ligação VPN site-to-site. Todas as ligações VPN não estão a funcionar com BGP.

Todos os clientes podem aceder apenas ao VNet1.

![multi-VNet S2S e sucursal](./media/vpn-gateway-about-point-to-site-routing/7.jpg "multi-VNet S2S e sucursal")

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Sítio1: 10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 10.2.0.0.0/16, 10.3.0.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Acesso

* Os clientes windows só podem aceder ao VNet1

* Clientes não Windows só podem aceder ao VNet1

## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office-bgp"></a><a name="multivnets2sbranchbgp"></a>Múltiplos VNets ligados usando S2S e uma sucursal (BGP)

Neste exemplo, a ligação de gateway Point-to-Site VPN é para VNet1. O VNet1 está ligado ao VNet2 utilizando uma ligação VPN site-to-site. O VNet2 está ligado ao VNet3 utilizando uma ligação VPN site-to-site. Não existe um desvio direto ou um túnel VPN local-a-local entre as redes VNet1 e VNet3. O VNet3 está ligado a uma sucursal (Site1) utilizando uma ligação VPN site-to-site. Todas as ligações VPN estão a executar BGP.

Os clientes que utilizam o Windows podem aceder a VNets e sites que estejam ligados utilizando uma ligação VPN site-to-site, mas as rotas para VNet2, VNet3 e Site1 devem ser adicionadas manualmente ao cliente. Os clientes não Windows podem aceder a VNets e sites que estejam ligados usando uma ligação VPN site-to-site sem qualquer intervenção manual. O acesso é transitório e os clientes podem aceder a recursos em todos os VNets e sites conectados (no local).

![multi-VNet S2S e sucursal](./media/vpn-gateway-about-point-to-site-routing/8.jpg "multi-VNet S2S e sucursal")

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Sítio1: 10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas a clientes não Windows: 10.1.0.0/16, 10.2.0.0.0/16, 10.3.0.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Acesso

* Os clientes windows podem aceder a VNet1, VNet2, VNet3 e Site1, mas as rotas para VNet2, VNet3 e Site1 devem ser adicionadas manualmente ao cliente.

* Os clientes não Windows podem aceder a VNet1, Vnet2, VNet3 e Site1.

## <a name="next-steps"></a>Passos seguintes

Consulte [Criar uma VPN P2S utilizando o portal Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para começar a criar o seu P2S VPN.
