---
title: Azure Porta da Frente | Microsoft Docs
description: Este artigo apresenta uma descrição geral do Azure Front Door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: 2a5c298c201aa2a1f968c2a96dcfd6963a410cf7
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149227"
---
# <a name="what-is-azure-front-door"></a>O que é o Azure Front Door?

> [!IMPORTANT]
> Esta documentação é para Azure Front Door. Procura informações sobre O Padrão/Premium da Porta Frontal Azure (Pré-visualização)? Ver [aqui](/standard-premium/overview.md).

Azure Front Door é um ponto de entrada global e escalável que utiliza a rede de arestas globais da Microsoft para criar aplicações web rápidas, seguras e amplamente escaláveis. Com a Front Door, pode transformar as suas aplicações globais de consumidores e empresas em aplicações modernas robustas e personalizadas de alto desempenho com conteúdos que atingem um público global através do Azure.

<p align="center">
  <img src="./media/front-door-overview/front-door-visual-diagram.png" alt="Front Door architecture" width="600" title="Azure Front Door">
</p>

A Porta Frontal funciona na camada 7 (camada HTTP/HTTPS) utilizando qualquer protocolo decast com TCP dividido e a rede global da Microsoft para melhorar a conectividade global. Com base no seu método de encaminhamento, pode garantir que a Porta Frontal encaminhará os pedidos do seu cliente para o backend de aplicações mais rápido e disponível. Os back-ends de aplicação são serviços com acesso à Internet alojados dentro ou fora do Azure. A Porta Frontal fornece uma gama de [métodos de encaminhamento de tráfego](front-door-routing-methods.md) e opções de [monitorização de saúde de backend](front-door-health-probes.md) para atender às diferentes necessidades de aplicação e cenários automáticos de failover. À semelhança [do Traffic Manager,](../traffic-manager/traffic-manager-overview.md)a Porta frontal é resiliente a falhas, incluindo falhas em toda uma região do Azure.

>[!NOTE]
> O Azure oferece um conjunto de soluções de balanceamento de carga totalmente geridas para os seus cenários. 
> * Se estiver a procurar fazer o encaminhamento global baseado em DNS e **não** tiver requisitos para a rescisão do protocolo de Segurança da Camada de Transporte (TLS) ("descarga SSL"), por pedido HTTP/HTTPS ou processamento de camadas de aplicação, reveja [o Gestor de Tráfego.](../traffic-manager/traffic-manager-overview.md) 
> * Se pretender carregar o equilíbrio entre os seus servidores numa região na camada de aplicação, reveja o [Gateway de Aplicações](../application-gateway/overview.md).
> * Para equilibrar a carga da camada de rede, reveja [o Balanceador de Carga](../load-balancer/load-balancer-overview.md). 
> 
> Os seus cenários de ponta a ponta podem beneficiar de combinar estas soluções conforme necessário.
> Para uma comparação de opções de equilíbrio de carga Azure, consulte [a visão geral das opções de equilíbrio de carga em Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

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

* Segurança da aplicação com firewall integrada  **[de aplicações web (WAF)](../web-application-firewall/overview.md)**.

* Redirecione o tráfego HTTP PARA HTTPS com **[o redirecionamento de URL](front-door-url-redirect.md)**.

* Caminho de encaminhamento personalizado com **[reescrita de URL](front-door-url-rewrite.md)**.

* Suporte nativo da conectividade IPv6 e **[protocolo HTTP/2](front-door-http2.md)**.

## <a name="pricing"></a>Preços

Para obter informações sobre preços, veja [Preços do Front Door](https://azure.microsoft.com/pricing/details/frontdoor/). Ver [SLA para Azure Front Door](https://azure.microsoft.com/en-us/support/legal/sla/frontdoor/v1_0/).

## <a name="whats-new"></a>Novidades?

Subscreva o feed RSS e veja as últimas atualizações da funcionalidade Azure Front Door na página [Azure Updates.](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
