---
title: Sobre o Azure ExpressRoute FastPath
description: Saiba mais sobre o Azure ExpressRoute FastPath para enviar tráfego de rede contornando o gateway
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: duau
ms.openlocfilehash: c953668d6b2e364e6e703b1769317f1c520317ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654378"
---
# <a name="about-expressroute-fastpath"></a>Acerca do ExpressRoute FastPath

O Gateway de rede virtual ExpressRoute foi concebido para trocar rotas de rede e tráfego de rede de rotas. O FastPath foi concebido para melhorar o desempenho do caminho de dados entre a sua rede no local e a sua rede virtual. Quando ativado, o FastPath envia tráfego de rede diretamente para máquinas virtuais na rede virtual, contornando o gateway.

## <a name="requirements"></a>Requisitos

### <a name="circuits"></a>Circuitos

O FastPath está disponível em todos os circuitos ExpressRoute.

### <a name="gateways"></a>Gateways

O FastPath ainda requer a criação de uma porta de entrada de rede virtual para trocar rotas entre a rede virtual e a rede no local. Para obter mais informações sobre gateways de rede virtuais e ExpressRoute, incluindo informações de desempenho e GATEWAY SKUs, consulte [gateways de rede virtual ExpressRoute](expressroute-about-virtual-network-gateways.md).

Para configurar o FastPath, o gateway de rede virtual deve ser:

* Ultra Performance
* ErGw3AZ

> [!IMPORTANT]
> Se planeia utilizar o FastPath com o IPv6 com base em espreitamento privado sobre o ExpressRoute, certifique-se de selecionar ErGw3AZ para **SKU**. Note que este só está disponível para circuitos que utilizem ExpressRoute Direct.
> 
>

## <a name="limitations"></a>Limitações

Embora o FastPath suporte a maioria das configurações, não suporta as seguintes funcionalidades:

* UDR na sub-rede gateway: Este UDR não tem qualquer impacto no tráfego de rede que o FastPath envia diretamente da sua rede no local para as máquinas virtuais na rede virtual Azure. 

* VNet Peering: Se tiver outras redes virtuais espreitadas com a que está ligada ao ExpressRoute, o tráfego de rede da sua rede no local para as outras redes virtuais (isto é, os chamados VNets "Spoke") continuará a ser enviado para o gateway de rede virtual. A solução é ligar todas as redes virtuais diretamente ao circuito ExpressRoute.

* Balanceador de Carga Básica: Se implementar um balanceador de carga interno básico na sua rede virtual ou o serviço Azure PaaS que implementa na sua rede virtual utiliza um equilibrador de carga interno básico, o tráfego de rede da sua rede no local para os IPs virtuais alojados no balanceador de carga Básico será enviado para o portal de rede virtual. A solução é atualizar o balanceador de carga básico para um [balanceador de carga Standard](../load-balancer/load-balancer-overview.md).

* Link Privado: Se ligar a um [ponto final privado](../private-link/private-link-overview.md) na sua rede virtual a partir da sua rede no local, a ligação passará pelo gateway de rede virtual.
 
## <a name="next-steps"></a>Passos seguintes

Para ativar o FastPath, consulte [Link uma rede virtual ao ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
