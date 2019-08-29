---
title: Introdução ao Ambiente do Serviço de Aplicativo v1 – Azure
description: Saiba mais sobre o recurso Ambiente do Serviço de Aplicativo v1 que fornece unidades de escala seguras e associadas à VNet para executar todos os seus aplicativos.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 32450d0c5fbb5599b286921b9653ae68faf40ecf
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070146"
---
# <a name="introduction-to-app-service-environment-v1"></a>Introdução ao Ambiente do Serviço de Aplicativo v1

> [!NOTE]
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v1.  Há uma versão mais recente do Ambiente do Serviço de Aplicativo que é mais fácil de usar e é executada em uma infraestrutura mais potente. Para saber mais sobre a nova versão, comece com a [introdução ao ambiente do serviço de aplicativo](intro.md).

## <a name="overview"></a>Descrição geral

Um Ambiente do Serviço de Aplicativo é uma opção de plano de serviço [Premium][PremiumTier] do [serviço de Azure app](../overview.md) que fornece um ambiente totalmente isolado e dedicado para execução segura de aplicativos de serviço do Azure app em alta escala, incluindo aplicativos Web, aplicativos móveis e aplicativos de API.  

Os ambientes de serviço de aplicativo são ideais para cargas de trabalho de aplicativo que exigem:

* Escala muito alta
* Isolamento e acesso seguro à rede

Os clientes podem criar vários ambientes de serviço de aplicativo em uma única região do Azure, bem como em várias regiões do Azure.  Isso torna os ambientes do serviço de aplicativo ideais para dimensionar horizontalmente as camadas de aplicativo sem estado para dar suporte a cargas de trabalho de RPS altas.

Os ambientes do serviço de aplicativo são isolados para executar apenas os aplicativos de um único cliente e são sempre implantados em uma rede virtual.  Os clientes têm controle refinado sobre o tráfego de rede do aplicativo de entrada e saída, e os aplicativos podem estabelecer conexões seguras de alta velocidade em redes virtuais para recursos corporativos locais.

Para obter uma visão geral de como os ambientes do serviço de aplicativo permitem o acesso de alta escala e segurança à rede, consulte o aprofundamento [AzureCon][AzureConDeepDive] nos ambientes do serviço de aplicativo!

Para obter uma análise aprofundada sobre o dimensionamento horizontal usando vários ambientes de serviço de aplicativo, consulte o artigo sobre como configurar uma [superfície de aplicativo distribuído geograficamente][GeodistributedAppFootprint].

Para ver como a arquitetura de segurança mostrada no aprofundamento do AzureCon foi configurada, consulte o artigo sobre como implementar uma [arquitetura de segurança em camadas](app-service-app-service-environment-layered-security.md) com ambientes do serviço de aplicativo.

Os aplicativos em execução nos ambientes do serviço de aplicativo podem ter seu acesso restringido por dispositivos upstream, como WAF (firewalls do aplicativo Web).  O artigo sobre como [configurar um WAF para ambientes do serviço de aplicativo](app-service-app-service-environment-web-application-firewall.md) abrange esse cenário.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Recursos de computação dedicados

Todos os recursos de computação em um Ambiente do Serviço de Aplicativo são dedicados exclusivamente a uma única assinatura, e um Ambiente do Serviço de Aplicativo pode ser configurado com até 50 (50) recursos de computação para uso exclusivo por um único aplicativo.

Um Ambiente do Serviço de Aplicativo é composto por um pool de recursos de computação de front-end, bem como de um a três pools de recursos de computação de trabalho.

O pool de front-end contém recursos de computação responsáveis pela terminação SSL, bem como balanceamento de carga automático de solicitações de aplicativo dentro de um Ambiente do Serviço de Aplicativo.

Cada pool de trabalho contém recursos de computação alocados para [planos do serviço de aplicativo][AppServicePlan]que, por sua vez, contêm um ou mais aplicativos de serviço de Azure app.  Como pode haver até três pools de trabalho diferentes em um Ambiente do Serviço de Aplicativo, você tem a flexibilidade de escolher recursos de computação diferentes para cada pool de trabalho.  

Por exemplo, isso permite que você crie um pool de trabalho com recursos de computação menos poderosos para planos do serviço de aplicativo destinados a aplicativos de desenvolvimento ou teste.  Um segundo pool de trabalho (ou até mesmo terceiro) poderia usar recursos de computação mais poderosos destinados a planos do serviço de aplicativo que executam aplicativos de produção.

Para obter mais detalhes sobre a quantidade de recursos de computação disponíveis para os pools de front-end e de trabalho, consulte [como configurar um ambiente do serviço de aplicativo][HowToConfigureanAppServiceEnvironment].  

Para obter detalhes sobre os tamanhos de recursos de computação disponíveis com suporte em um Ambiente do Serviço de Aplicativo, consulte a página de [preços do serviço de aplicativo][AppServicePricing] e examine as opções disponíveis para ambientes do serviço de aplicativo no tipo de preço premium.

## <a name="virtual-network-support"></a>Suporte de rede virtual

Um Ambiente do Serviço de Aplicativo pode ser criado em uma rede virtual Azure Resource Manager **ou** em uma rede virtual do modelo de implantação clássico ([mais informações sobre redes virtuais][MoreInfoOnVirtualNetworks]).  Como uma Ambiente do Serviço de Aplicativo sempre existe em uma rede virtual e com mais precisão em uma sub-rede de uma rede virtual, você pode aproveitar os recursos de segurança das redes virtuais para controlar as comunicações de rede de entrada e de saída.  

Um Ambiente do Serviço de Aplicativo pode ser voltado para a Internet com um endereço IP público ou destinado internamente com apenas um endereço de Load Balancer (ILB) interno do Azure.

Você pode usar [grupos de segurança de rede][NetworkSecurityGroups] para restringir as comunicações de rede de entrada para a sub-rede onde reside um ambiente do serviço de aplicativo.  Isso permite que você execute aplicativos por trás de dispositivos e serviços de upstream, como firewalls de aplicativos Web e provedores de SaaS de rede.

As aplicações também precisam de aceder frequentemente a recursos da empresa, tais como bases de dados internas e serviços Web.  Uma abordagem comum é tornar esses pontos de extremidade disponíveis somente para tráfego de rede interno que flui dentro de uma rede virtual do Azure.  Quando uma Ambiente do Serviço de Aplicativo é unida à mesma rede virtual que os serviços internos, os aplicativos em execução no ambiente podem acessá-los, incluindo pontos de extremidade acessíveis via conexões [site a site][SiteToSite] e [do Azure ExpressRoute][ExpressRoute] .

Para obter mais detalhes sobre como os ambientes do serviço de aplicativo funcionam com redes virtuais e redes locais, consulte os seguintes artigos sobre [arquitetura de rede][NetworkArchitectureOverview], controle de [tráfego de entrada][ControllingInboundTraffic]e [conexão segura com back-ends][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Introdução

Para começar a usar os ambientes do serviço de aplicativo, consulte [como criar um ambiente do serviço de aplicativo][HowToCreateAnAppServiceEnvironment]

Para obter uma visão geral da arquitetura de rede Ambiente do Serviço de Aplicativo, consulte o artigo [visão geral da arquitetura de rede][NetworkArchitectureOverview] .

Para obter detalhes sobre como usar um Ambiente do Serviço de Aplicativo com o ExpressRoute, consulte o seguinte artigo sobre os [ambientes de rota expressa e serviço de aplicativo][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: ../overview-hosting-plans.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: https://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->