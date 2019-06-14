---
title: Sobre o Azure ponto a Site encaminhamento | Documentos da Microsoft
description: Este artigo ajuda-o a compreender como se comporta a Point-to-Site VPN de encaminhamento.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/28/2019
ms.author: anzaman
ms.openlocfilehash: 486a910226db5dc7b36aaf873e7bb8115eb78805
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60653617"
---
# <a name="about-point-to-site-vpn-routing"></a>Acerca do encaminhamento VPN de Ponto a Site

Este artigo ajuda-o a compreender o comportamento do encaminhamento de VPN do Azure ponto a Site. Comportamento de encaminhamento de VPN de P2S é dependente do cliente do sistema operacional, o protocolo utilizado para a ligação VPN e como as redes virtuais (VNets) estão ligadas entre si.

Atualmente, o Azure suporta dois protocolos para acesso remoto, IKEv2 e SSTP. IKEv2 é suportado em muitos sistemas de operativos de cliente, incluindo Windows, Linux, MacOS, Android e iOS. O SSTP só é suportado no Windows. Se fizer uma alteração para a topologia da sua rede e que os clientes de VPN do Windows, o pacote de cliente VPN para clientes do Windows tem de ser transferido e instalado novamente para que as alterações sejam aplicadas ao cliente.

> [!NOTE]
> Este artigo aplica-se apenas para IKEv2.
>

## <a name="diagrams"></a>Sobre os diagramas

Há uma série de diagramas diferentes neste artigo. Cada seção mostra uma topologia diferente ou de configuração. Para os fins deste artigo, Site-Site (S2S) e ligações de VNet a VNet funcionam da mesma forma, pois ambos são túneis IPsec. Todos os gateways VPN neste artigo são baseadas na rota.

## <a name="isolatedvnet"></a>Uma VNet isolada

A ligação de gateway VPN ponto a Site neste exemplo destina-se uma VNet que está ligada ou não em modo de peering com qualquer outra rede virtual (VNet1). Neste exemplo, os clientes que utilizam o SSTP ou IKEv2 podem aceder a VNet1.

![encaminhamento da VNet em ambiente isolado](./media/vpn-gateway-about-point-to-site-routing/1.jpg "isoladas de encaminhamento de VNet")

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes do Windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas aos clientes não-Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Clientes Windows podem aceder a VNet1

* Os clientes do Windows não podem aceder a VNet1

## <a name="multipeered"></a>Várias VNets em modo de peering

Neste exemplo, a ligação de gateway VPN ponto a Site é para a VNet1. VNet1 é agrupada com VNet2. VNet 2 está em modo de peering com VNet3. VNet1 é agrupada com VNet4. Não existe nenhum peering direta entre VNet1 e VNet3. Tem de VNet1 "Permitir que o trânsito de gateway" e VNet2 tem "utilizar gateways remotos" ativada.

Clientes que utilizam Windows podem aceder diretamente em modo de peering de VNets, mas o cliente VPN tem de ser transferido novamente se a todas as alterações efetuadas ao peering de VNet ou a topologia de rede. Clientes de não-Windows podem aceder diretamente em modo de peering de VNets. Acesso não é transitivo e está limitado a apenas VNets em modo de peering diretamente.

![VNets em modo de peering de vários](./media/vpn-gateway-about-point-to-site-routing/2.jpg "VNets em modo de peering de vários")

### <a name="address-space"></a>Espaço de endereços:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes do Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Rotas adicionadas aos clientes não-Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Clientes Windows podem aceder a VNet1, VNet2 e VNet4, mas o cliente VPN tem de ser transferido novamente para efetuar quaisquer alterações de topologia entrar em vigor.

* Os clientes do Windows não podem aceder a VNet1, VNet2 e VNet4

## <a name="multis2s"></a>Várias VNets ligadas através de uma VPN S2S

Neste exemplo, a ligação de gateway VPN ponto a Site é para a VNet1. VNet1 está ligado para a VNet2 usando uma conexão VPN de Site a Site. VNet2 está ligado a VNet3 usando uma conexão VPN de Site a Site. Não existe nenhum peering direto ou a ligação VPN de Site a Site entre VNet1 e VNet3. Todas as ligações Site a Site não estão executando o BGP para o encaminhamento.

Clientes que utilizam o Windows ou outro sistema operacional suportado, só podem aceder a VNet1. Para aceder às VNets adicionais, tem de utilizar o BGP.

![várias VNets e S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "várias VNets e S2S")

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes do Windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas aos clientes não-Windows: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes do Windows só podem aceder a VNet1

* Os clientes do Windows não podem aceder apenas VNet1

## <a name="multis2sbgp"></a>Várias VNets ligados através de uma VPN S2S (BGP)

Neste exemplo, a ligação de gateway VPN ponto a Site é para a VNet1. VNet1 está ligado para a VNet2 usando uma conexão VPN de Site a Site. VNet2 está ligado a VNet3 usando uma conexão VPN de Site a Site. Não existe nenhum peering direto ou a ligação VPN de Site a Site entre VNet1 e VNet3. Todas as ligações Site a Site executem o BGP para o encaminhamento.

Clientes que utilizam o Windows ou outro sistema operacional suportado, podem aceder a todas as VNets que estão ligadas através de uma ligação VPN de Site a Site, mas tem de ser adicionados manualmente para os clientes Windows rotas para VNets ligadas.

![várias VNets e S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "várias VNets e BGP de S2S")

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes do Windows: 10.1.0.0/16

* Rotas adicionadas aos clientes não-Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Clientes Windows podem aceder a VNet1, VNet2 e VNet3, mas rotas para a VNet2 e VNet3 terão que ser adicionados manualmente.

* Os clientes do Windows não podem aceder a VNet1, VNet2 e VNet3

## <a name="vnetbranch"></a>Uma VNet e uma filial

Neste exemplo, a ligação de gateway VPN ponto a Site é para a VNet1. VNet1 não está ligado / em modo de peering com outra rede virtual, mas está ligado a um site no local através de uma ligação de VPN de Site a Site que não está a executar o BGP.

Clientes Windows e não Windows só podem aceder a VNet1.

![roteamento com uma VNet e uma filial](./media/vpn-gateway-about-point-to-site-routing/5.jpg "roteamento com uma VNet e uma filial")

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes do Windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas aos clientes não-Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Clientes de Windows podem aceder apenas VNet1

* Os clientes do Windows não podem aceder apenas VNet1

## <a name="vnetbranchbgp"></a>Uma VNet e uma filial (BGP)

Neste exemplo, a ligação de gateway VPN ponto a Site é para a VNet1. VNet1 não está ligado ou em modo de peering com outra rede virtual, mas está ligado a um site no local (Site1) através de uma ligação de VPN de Site a Site com o BGP.

Clientes de Windows podem aceder a VNet e o escritório da filial (Site1), mas as rotas para Site1 devem ser adicionadas manualmente para o cliente. Clientes de não-Windows podem aceder a VNet, bem como o escritório da filial no local.

![uma VNet e uma filial (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "uma VNet e uma filial")

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes do Windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas aos clientes não-Windows: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Clientes Windows podem aceder a VNet1 e Site1, mas rotas para Site1 terão que ser adicionados manualmente.

* Os clientes do Windows não podem acessar VNet1 e Site1.


## <a name="multivnets2sbranch"></a>Várias VNets ligados através de S2S e para uma filial

Neste exemplo, a ligação de gateway VPN ponto a Site é para a VNet1. VNet1 está ligado para a VNet2 usando uma conexão VPN de Site a Site. VNet2 está ligado a VNet3 usando uma conexão VPN de Site a Site. Não existe nenhum peering direto ou o túnel VPN de Site a Site entre as redes VNet1 e VNet3. VNet3 está ligado a uma filial (Site1) usando uma conexão VPN de Site a Site. Todas as ligações VPN não estão a executar o BGP.

Todos os clientes podem aceder a VNet1 apenas.

![office de S2S e o ramo do multi-VNet](./media/vpn-gateway-about-point-to-site-routing/7.jpg "office de S2S e o ramo do multi-VNet")

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* As rotas adicionadas clientes: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas aos clientes não-Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes Windows podem aceder apenas VNet1

* Os clientes do Windows não podem aceder apenas VNet1

## <a name="multivnets2sbranchbgp"></a>Várias VNets ligados através de S2S e para uma filial (BGP)

Neste exemplo, a ligação de gateway VPN ponto a Site é para a VNet1. VNet1 está ligado para a VNet2 usando uma conexão VPN de Site a Site. VNet2 está ligado a VNet3 usando uma conexão VPN de Site a Site. Não existe nenhum peering direto ou o túnel VPN de Site a Site entre as redes VNet1 e VNet3. VNet3 está ligado a uma filial (Site1) usando uma conexão VPN de Site a Site. Todas as ligações VPN estão a executar o BGP.

Clientes que utilizam Windows podem aceder a VNets e sites que estão ligados através de uma ligação VPN de Site a Site, mas as rotas para a VNet2, VNet3 e Site1 devem ser adicionados manualmente para o cliente. Os clientes do Windows não podem acessar VNets e sites que estão ligados através de uma ligação de VPN de Site a Site sem qualquer intervenção manual. O acesso é transitivo, e os clientes podem aceder a recursos em VNets tudo ligado e de sites (no local).

![office de S2S e o ramo do multi-VNet](./media/vpn-gateway-about-point-to-site-routing/8.jpg "office de S2S e o ramo do multi-VNet")

### <a name="address-space"></a>Espaço de endereços

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* As rotas adicionadas clientes: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas aos clientes não-Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes Windows podem aceder a VNet1, VNet2, VNet3 e Site1, mas rotas para a VNet2, VNet3 e Site1 devem ser adicionadas manualmente para o cliente.

* Clientes de não-Windows podem aceder a VNet1, Vnet2, VNet3 e Site1.

## <a name="next-steps"></a>Passos Seguintes

Ver [criar uma VPN de P2S com o portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para começar a criar a VPN P2S.
