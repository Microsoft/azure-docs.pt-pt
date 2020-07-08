---
title: Utilização do Azure ExpressRoute para suportar utilizadores remotos
description: Esta página descreve como pode aproveitar o Azure ExpressRoute para ativar o funcionamento remotamente devido à pandemia COVID-19.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 18b30380ede0c95c7d039749a4d47f65e5824058
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84738078"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Utilizar o Azure ExpressRoute para criar conectividade híbrida para suportar utilizadores remotos

Este artigo descreve como pode aproveitar o ExpressRoute, Azure, a rede Microsoft e o ecossistema parceiro Azure para funcionar remotamente.

## <a name="connecting-to-azure-services-with-expressroute"></a>Ligação aos serviços da Azure com o ExpressRoute

>[!NOTE]
>Este artigo descreve como pode aproveitar o ExpressRoute, Azure, a rede Microsoft e o ecossistema parceiro Azure para trabalhar remotamente e mitigar problemas de rede que está a enfrentar devido à crise covid-19.
>

[O ExpressRoute](expressroute-introduction.md) é um serviço Azure que permite a conectividade privada entre datacenters da Microsoft e infraestruturas que estão nas suas instalações ou numa instalação de coinstalação. As ligações do ExpressRoute não passam para a Internet pública. Oferecem conectividade, fiabilidade e velocidades seguras, com latências mais baixas e consistentes do que as ligações típicas através da Internet.

## <a name="useful-links"></a>Ligações úteis

* [Como aumentar a largura de banda para o circuito ExpressRoute existente](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [Monitorização, métricas e alertas ExpressRoute](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Otimização da Rota sobre ExpressRoute](expressroute-optimize-routing.md)
* [Azure ExpressRoute para O365](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Considerações de encaminhamento assimétrico](expressroute-asymmetric-routing.md)
* [Como abrir um pedido de apoio no portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>Resolução de problemas

* [Verify ExpressRoute Connectivity](expressroute-troubleshooting-expressroute-overview.md) (Verificar a Conectividade do ExpressRoute)
* Obtenção de tabela ARP em [Gestor de Recursos](expressroute-troubleshooting-arp-resource-manager.md) e [Clássico](expressroute-troubleshooting-arp-classic.md)
* [Reinicie um circuito falhado](reset-circuit.md)
* [Desempenho da rede de resolução de problemas](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre os modelos de conectividade ExpressRoute](expressroute-connectivity-models.md)
* Saiba mais sobre ligações ExpressRoute e domínios de encaminhamento. Ver [circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md)
* Encontre um fornecedor de serviços. Ver [parceiros ExpressRoute e locais de observação](expressroute-locations.md)
* Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).
* [Criar e modificar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
* [Criar e modificar o peering de um circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Ligar uma rede virtual a um circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
