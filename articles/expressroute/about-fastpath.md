---
title: Sobre o Azure ExpressRoute FastPath
description: Saiba mais sobre o Azure ExpressRoute FastPath para enviar tráfego de rede contornando o portal
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 265004b1171d1df95b3090676d5836b951c28a28
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80282856"
---
# <a name="about-expressroute-fastpath"></a>Acerca do ExpressRoute FastPath

O gateway da rede virtual ExpressRoute foi concebido para trocar rotas de rede e tráfego de rede de rotas. O FastPath foi concebido para melhorar o desempenho do caminho de dados entre a sua rede no local e a sua rede virtual. Quando ativado, o FastPath envia o tráfego de rede diretamente para máquinas virtuais na rede virtual, contornando o portal.

## <a name="requirements"></a>Requisitos

### <a name="circuits"></a>Circuitos

O FastPath está disponível em todos os circuitos ExpressRoute.

### <a name="gateways"></a>Gateways

O FastPath ainda requer a criação de um portal de rede virtual para trocar rotas entre a rede virtual e a rede no local. Para obter mais informações sobre gateways de rede virtual e ExpressRoute, incluindo informações sobre desempenho e gateway SKUs, consulte [gateways de rede virtual ExpressRoute](expressroute-about-virtual-network-gateways.md).

Para configurar o FastPath, o gateway da rede virtual deve ser:

* Ultra Performance
* Ergw3Az

## <a name="supported-features"></a>Funcionalidades suportadas

Embora o FastPath suporte a maioria das configurações, não suporta as seguintes funcionalidades:

* UDR na sub-rede gateway: Se aplicar um UDR à subnet de gateway da sua rede virtual, o tráfego de rede a partir da sua rede no local continuará a ser enviado para o portal da rede virtual.

* VNet Peering: Se tiver outras redes virtuais empeeradas com a que está ligada ao ExpressRoute, o tráfego de rede da sua rede no local para outras redes virtuais (ou seja, os chamados VNets "Spoke") continuará a ser enviado para o portal da rede virtual. A seleção é ligar diretamente todas as redes virtuais ao circuito ExpressRoute.

* Equilíbrio de carga básico: Se implementar um equilíbrio de carga interna Básico na sua rede virtual ou no serviço Azure PaaS que implementa na sua rede virtual utiliza um equilíbrio de carga interna Básico, o tráfego de rede da sua rede no local para os IPs virtuais alojados no equilíbrio de carga Basic será enviado para o gateway da rede virtual. A solução é atualizar o equilíbrio de carga Basic para um [equilibrador](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)de carga Standard .

* Link Privado: Se ligar a um [ponto final privado](../private-link/private-link-overview.md) na sua rede virtual a partir da sua rede no local, a ligação passará pelo portal de rede virtual.
 
## <a name="next-steps"></a>Passos seguintes

Para ativar o FastPath, consulte [Link uma rede virtual para expressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
