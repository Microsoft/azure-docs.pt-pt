---
title: Introdução ao ASE v1
description: Saiba mais sobre as funcionalidades de App Service Environment v1. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: stefsch
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 878cef39ade0487c03dba83c9e3631fe3c72088f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962388"
---
# <a name="introduction-to-app-service-environment-v1"></a>Introdução ao Ambiente de Serviço de Aplicações v1

> [!NOTE]
> Este artigo é sobre o App Service Environment v1.  Existe uma versão mais recente do App Service Environment que é mais fácil de usar e funciona em infraestruturas mais poderosas. Para saber mais sobre a nova versão comece com a [Introdução ao Ambiente de Serviço de Aplicações.](intro.md)

## <a name="overview"></a>Descrição geral

Um App Service Environment é uma opção de plano de serviço [Premium][PremiumTier] do [Azure App Service](../overview.md) que fornece um ambiente totalmente isolado e dedicado para executar de forma segura aplicações do Azure App Service em alta escala, incluindo Aplicações Web, Aplicações Móveis e Aplicações API.  

Os Ambientes de Serviço de Aplicações são ideais para cargas de trabalho de aplicações que requerem:

* Escala muito alta
* Isolamento e acesso seguro à rede

Os clientes podem criar vários Ambientes de Serviço de Aplicações numa única região de Azure, bem como em várias regiões do Azure.  Isto torna os Ambientes de Serviço de Aplicações ideais para escalar horizontalmente os níveis de aplicação sem estado em suporte a altas cargas de trabalho RPS.

Os Ambientes de Serviço de Aplicações estão isolados para executar apenas as aplicações de um único cliente, e são sempre implantados numa rede virtual.  Os clientes têm um controlo fino sobre o tráfego da rede de aplicações de entrada e saída, e as aplicações podem estabelecer ligações seguras de alta velocidade sobre redes virtuais para recursos corporativos no local.

Para uma visão geral de como os Ambientes de Serviço de Aplicações permitem acesso de rede de alta escala e segurança, consulte o [AzureCon Deep Dive][AzureConDeepDive] em Ambientes de Serviço de Aplicações!

Para um mergulho profundo na escala horizontal utilizando vários Ambientes de Serviço de Aplicações, consulte o artigo sobre como configurar uma [pegada de aplicação geo-distribuída][GeodistributedAppFootprint].

Para ver como a arquitetura de segurança mostrada no AzureCon Deep Dive foi configurada, veja o artigo sobre a implementação de uma arquitetura de [segurança em camadas](app-service-app-service-environment-layered-security.md) com Ambientes de Serviço de Aplicações.

As aplicações em execução em Ambientes de Serviço de Aplicações podem ter o seu acesso fechado por dispositivos a montante, como firewalls de aplicações web (WAF).  O artigo sobre [a configuração de um WAF para Ambientes de Serviço de Aplicações](app-service-app-service-environment-web-application-firewall.md) cobre este cenário.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Recursos Compute dedicados

Todos os recursos compute num Ambiente de Serviço de Aplicações são dedicados exclusivamente a uma única subscrição, e um Ambiente de Serviço de Aplicações pode ser configurado com até 50 (50) recursos de computação para uso exclusivo através de uma única aplicação.

Um Ambiente de Serviço de Aplicações é composto por um conjunto de recursos computacional front-end, bem como um a três conjuntos de recursos de computação de trabalhadores.

O pool frontal contém recursos computativos responsáveis pela rescisão de TLS, bem como o equilíbrio automático de carga dos pedidos de aplicações dentro de um Ambiente de Serviço de Aplicações.

Cada grupo de trabalhadores contém recursos de computação atribuídos a [Planos de Serviço de Aplicações,][AppServicePlan]que por sua vez contêm uma ou mais aplicações do Azure App Service.  Uma vez que pode haver até três diferentes piscinas de trabalhadores num Ambiente de Serviço de Aplicações, você tem a flexibilidade para escolher diferentes recursos computacional para cada piscina de trabalhadores.  

Por exemplo, isto permite criar um pool de trabalhadores com recursos computacional menos potentes para planos de serviço de aplicações destinados ao desenvolvimento ou a aplicações de teste.  Um segundo (ou mesmo terceiro) pool de trabalhadores poderia usar recursos de computação mais potentes destinados a Planos de Serviço de Aplicações que executam aplicações de produção.

Para obter mais detalhes sobre a quantidade de recursos de computação disponíveis para os pools front-end e trabalhadores, consulte [Como Configurar um Ambiente de Serviço de Aplicações.][HowToConfigureanAppServiceEnvironment]  

Para obter mais informações sobre os tamanhos de recursos de computação disponíveis suportados num Ambiente de Serviço de Aplicações, consulte a página de Preços do [Serviço de Aplicações][AppServicePricing] e reveja as opções disponíveis para Ambientes de Serviço de Aplicação no nível de preços Premium.

## <a name="virtual-network-support"></a>Suporte virtual à rede

Um Ambiente de Serviço de Aplicações pode ser criado **numa** rede virtual Azure Resource Manager **ou** numa rede virtual de modelos de implementação clássica[(mais informações em redes virtuais).][MoreInfoOnVirtualNetworks]  Uma vez que existe sempre um Ambiente de Serviço de Aplicações numa rede virtual e, mais precisamente, dentro de uma sub-rede de uma rede virtual, pode aproveitar as funcionalidades de segurança das redes virtuais para controlar as comunicações de rede de entrada e saída.  

Um Ambiente de Serviço de Aplicações pode ser virado para a Internet com um endereço IP público, ou virado para o interior apenas com um endereço Azure Internal Load Balancer (ILB).

Pode utilizar [grupos de segurança][NetworkSecurityGroups] de rede para restringir as comunicações de rede de entrada à sub-rede onde reside um Ambiente de Serviço de Aplicações.  Isto permite-lhe executar aplicações por trás de dispositivos e serviços a montante, como firewalls de aplicações web e fornecedores de redes SaaS.

As aplicações também precisam de aceder frequentemente a recursos da empresa, tais como bases de dados internas e serviços Web.  Uma abordagem comum consiste em disponibilizar estes pontos finais apenas ao tráfego interno da rede que flui dentro de uma rede virtual Azure.  Uma vez que um Ambiente de Serviço de Aplicações é associado à mesma rede virtual que os serviços internos, as aplicações em execução no ambiente podem aceder-lhes, incluindo pontos finais alcançáveis através das ligações [Site-to-Site][SiteToSite] e [Azure ExpressRoute.][ExpressRoute]

Para obter mais detalhes sobre como os Ambientes de Serviço de Aplicações funcionam com redes virtuais e redes no local consulte os seguintes artigos sobre [Arquitetura de Rede,][NetworkArchitectureOverview] [Controle de Tráfego de Entrada][ControllingInboundTraffic]e [Ligação Segura a Backends][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Introdução

Para começar com ambientes de serviço de [aplicações,][HowToCreateAnAppServiceEnvironment] consulte como criar um ambiente de serviço de aplicações

Para uma visão geral da arquitetura da rede de ambiente de serviço de aplicações, consulte o artigo [De visão geral da Rede Arquitetura.][NetworkArchitectureOverview]

Para obter mais informações sobre a utilização de um Ambiente de Serviço de Aplicações com ExpressRoute, consulte o seguinte artigo sobre [Ambientes de Rota Expresso e Serviço de Aplicações.][NetworkConfigDetailsForExpressRoute]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: ../../virtual-network/virtual-networks-faq.md
[AppServicePlan]: ../overview-hosting-plans.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: ../../logic-apps/logic-apps-overview.md
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->