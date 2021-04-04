---
title: 'Azure ExpressRoute: Modelos de conectividade'
description: Reveja a conectividade entre a rede do cliente, o Microsoft Azure e os serviços Microsoft 365. Os clientes podem usar fornecedores MPLS, trocas de nuvens e Ethernet.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: duau
ms.openlocfilehash: fb35a03b5dd8780445eb27f485966e3c3452feea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91978764"
---
# <a name="expressroute-connectivity-models"></a>Modelos de conectividade do ExpressRoute
Pode criar uma ligação entre a sua rede no local e a nuvem microsoft de quatro maneiras diferentes, [CloudExchange Co-location](#CloudExchange), [Point-to-Point Ethernet Connection](#Ethernet), [Any-to-Any (IPVPN) Connection](#IPVPN), e [ExpressRoute Direct](#Direct). Os fornecedores de conectividade podem oferecer um ou mais modelos de conectividade. Pode trabalhar com o seu fornecedor de conectividade para escolher o modelo que funciona melhor para si.
<br><br>

:::image type="content" source="./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png" alt-text="Diagrama dos modelos de conectividade do ExpressRoute":::

## <a name="co-located-at-a-cloud-exchange"></a><a name="CloudExchange"></a>Localizado conjuntamente numa troca de nuvem
Se estiver co-localizado numa instalação com uma troca de nuvens, pode encomendar ligações cruzadas virtuais à nuvem da Microsoft através da troca de Ethernet do provedor de co-localização. Os fornecedores de localização conjunta podem oferecer ligações cruzadas de Camada 2 ou ligações cruzadas geridas de Camada 3 entre a sua infraestrutura na localização conjunto e a nuvem da Microsoft.

## <a name="point-to-point-ethernet-connections"></a><a name="Ethernet"></a>Ligações de Ethernet ponto a ponto
Pode ligar os seus centros de dados/escritórios no local à nuvem da Microsoft através de ligações de Ethernet ponto a ponto. Os fornecedores de Ethernet ponto a ponto podem oferecer ligações de Camada 2 ou ligações geridas de Camada 3 entre o seu site e a nuvem da Microsoft.

## <a name="any-to-any-ipvpn-networks"></a><a name="IPVPN"></a>Redes qualquer a qualquer (IPVPN)
Pode integrar a sua WAN com a nuvem da Microsoft. Os fornecedores de VPN de IP (normalmente VPN de MPLS) oferecem uma conectividade qualquer a qualquer entre as suas sucursais e os centros de dados. A nuvem da Microsoft pode ser interligada à sua WAN para torná-la mais semelhante a qualquer outra sucursal. Os fornecedores de WAN oferecem normalmente uma conectividade gerida de Camada 3. As funcionalidades e capacidades do ExpressRoute são idênticas em todos os modelos de conetividade acima.

## <a name="direct-from-expressroute-sites"></a><a name="Direct"></a>Diretamente dos sites ExpressRoute
Pode ligar-se diretamente à rede global da Microsoft num local de observação estrategicamente distribuído em todo o mundo. [O ExpressRoute Direct](expressroute-erdirect-about.md) fornece conectividade dual 100 Gbps ou 10-Gbps, que suporta a conectividade Ative/Ative à escala.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre ligações ExpressRoute e domínios de encaminhamento. Veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Saiba mais sobre funcionalidades do ExpressRoute. Consulte a [Descrição Geral Técnica do ExpressRoute](expressroute-introduction.md)
* Encontre um fornecedor de serviços. Veja [Parceiros e localizações de peering do ExpressRoute ](expressroute-locations.md).
* Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).
* Veja os requisitos para [Encaminhamento](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurar a ligação do ExpressRoute.
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configure o encaminhamento](expressroute-howto-routing-portal-resource-manager.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)