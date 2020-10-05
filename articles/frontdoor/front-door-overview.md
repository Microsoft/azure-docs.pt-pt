---
title: Porta frontal Azure ! Microsoft Docs
description: Este artigo apresenta uma descrição geral do Azure Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2020
ms.author: duau
ms.openlocfilehash: 039e5b94bbd9d3b6c3edcb92eff88e7a9931205d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91448738"
---
# <a name="what-is-azure-front-door"></a>O que é o Azure Front Door?

Azure Front Door é um ponto de entrada global e escalável que utiliza a rede de arestas globais da Microsoft para criar aplicações web rápidas, seguras e amplamente escaláveis. Com a Front Door, pode transformar as suas aplicações globais de consumidores e empresas em aplicações modernas robustas e personalizadas de alto desempenho com conteúdos que atingem um público global através do Azure.

:::image type="content" source="media/front-door-overview/front-door-visual-diagram.png#center" alt-text="Arquitetura da porta da frente":::

A Porta Frontal funciona na camada 7 (camada HTTP/HTTPS) utilizando qualquer protocolo decast com TCP dividido e a rede global da Microsoft para melhorar a conectividade global. Com base no seu método de encaminhamento, pode garantir que a Porta Frontal encaminhará os pedidos do seu cliente para o backend de aplicações mais rápido e disponível. Os back-ends de aplicação são serviços com acesso à Internet alojados dentro ou fora do Azure. A Porta Frontal fornece uma gama de [métodos de encaminhamento de tráfego](front-door-routing-methods.md) e opções de [monitorização de saúde de backend](front-door-health-probes.md) para atender às diferentes necessidades de aplicação e cenários automáticos de failover. À semelhança [do Traffic Manager,](../traffic-manager/traffic-manager-overview.md)a Porta frontal é resiliente a falhas, incluindo falhas em toda uma região do Azure.

>[!NOTE]
> O Azure oferece um conjunto de soluções de balanceamento de carga totalmente geridas para os seus cenários. 
> * Se estiver a procurar fazer o encaminhamento global baseado em DNS e **não** tiver requisitos para a rescisão do protocolo de Segurança da Camada de Transporte (TLS) ("descarga SSL"), por pedido HTTP/HTTPS ou processamento de camadas de aplicação, reveja [o Gestor de Tráfego.](../traffic-manager/traffic-manager-overview.md) 
> * Se pretender carregar o equilíbrio entre os seus servidores numa região na camada de aplicação, reveja o [Gateway de Aplicações](../application-gateway/application-gateway-introduction.md)
> * Para equilibrar a carga da camada de rede, reveja [o Balanceador de Carga](../load-balancer/load-balancer-overview.md). 
> 
> Os seus cenários de ponta a ponta podem beneficiar de combinar estas soluções conforme necessário.
> Para uma comparação de opções de equilíbrio de carga Azure, consulte [a visão geral das opções de equilíbrio de carga em Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="why-use-azure-front-door"></a>Por que usar a porta da frente Azure?

Com a Porta Frontal pode construir, operar e escalar a sua aplicação web dinâmica e conteúdo estático. A Porta Frontal permite-lhe definir, gerir e monitorizar o encaminhamento global para o seu tráfego web, otimizando para o desempenho e fiabilidade do utilizador final de topo através de uma rápida falha global.

Principais funcionalidades incluídas com porta da frente:

* Desempenho acelerado da aplicação utilizando **[o protocolo](front-door-routing-architecture.md#anycast)** de qualquercast baseado em **[TCP dividido](front-door-routing-architecture.md#splittcp)**.

* Monitorização inteligente da sonda de **[saúde](front-door-health-probes.md)** para obter recursos de backend.

*  **[Encaminhamento baseado em caminhos URL](front-door-route-matching.md)** para pedidos.

* Permite hospedar vários websites para uma infraestrutura de aplicação eficiente. 

* Afinidade da sessão baseada em **[cookies.](front-door-routing-methods.md#affinity)**

* **[Gestão de descargas](front-door-custom-domain-https.md)** e certificados SSL.

* Defina o seu próprio **[domínio personalizado.](front-door-custom-domain.md)** 

* Segurança de aplicações com regras personalizadas **[de Firewall de Aplicações Web (WAF)](../web-application-firewall/overview.md)** e **[Proteção DDoS Azure](../virtual-network/ddos-protection-overview.md)**.

* Redirecione o tráfego HTTP PARA HTTPS com **[o redirecionamento de URL](front-door-url-redirect.md)**.

* Caminho de encaminhamento personalizado com **[reescrita de URL](front-door-url-rewrite.md)**.

* Suporte nativo da conectividade IPv6 e **[protocolo HTTP/2](front-door-http2.md)**.

## <a name="pricing"></a>Preços

Para obter informações sobre preços, veja [Preços do Front Door](https://azure.microsoft.com/pricing/details/frontdoor/). Ver [SLA para Azure Front Door](https://azure.microsoft.com/en-us/support/legal/sla/frontdoor/v1_0/).

## <a name="whats-new"></a>Novidades

Subscreva o feed RSS e veja as últimas atualizações da funcionalidade Azure Front Door na página [Azure Updates.](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
