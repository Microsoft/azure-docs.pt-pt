---
title: Introdução à ASE v1
description: Conheça as funcionalidades do App Service Environment v1. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: stefsch
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f655b7793bfbb5bbeddfc2f1f8e7bc973dabeb4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687366"
---
# <a name="introduction-to-app-service-environment-v1"></a>Introdução ao Ambiente de Serviço de Aplicações v1

> [!NOTE]
> Este artigo é sobre o App Service Environment v1.  Existe uma versão mais recente do App Service Environment que é mais fácil de usar e funciona em infraestruturas mais poderosas. Para saber mais sobre a nova versão comece com a Introdução ao Ambiente de Serviço de [Aplicações.](intro.md)

## <a name="overview"></a>Descrição geral

Um App Service Environment é uma opção de plano de serviço [Premium][PremiumTier] do [Azure App Service](../overview.md) que proporciona um ambiente totalmente isolado e dedicado para executar de forma segura aplicações do Serviço de Aplicações Azure em alta escala, incluindo Aplicações Web, Aplicações Móveis e Aplicações API.  

Os ambientes de serviço de aplicações são ideais para cargas horárias de aplicações que requerem:

* Muito alta escala
* Isolamento e acesso seguro à rede

Os clientes podem criar múltiplos Ambientes de Serviço de Aplicações numa única região do Azure, bem como em várias regiões do Azure.  Isto torna os Ambientes de Serviço de Aplicação ideais para escalar horizontalmente os níveis de aplicação sem estado em suporte a altas cargas de trabalho de RPS.

Os Ambientes de Serviço de Aplicações estão isolados para executar apenas as aplicações de um único cliente, e são sempre implantados numa rede virtual.  Os clientes têm um controlo fino sobre o tráfego da rede de aplicações de entrada e saída, e as aplicações podem estabelecer ligações seguras de alta velocidade sobre redes virtuais para recursos corporativos no local.

Para uma visão geral de como os Ambientes de Serviço de Aplicações permitem acesso de rede de alta escala e seguro, consulte o [AzureCon Deep Dive][AzureConDeepDive] em Ambientes de Serviço de Aplicações!

Para um mergulho profundo na escala horizontal utilizando vários Ambientes de Serviço de Aplicações, consulte o artigo sobre como configurar uma pegada de [aplicação geodistribuída][GeodistributedAppFootprint].

Para ver como foi configurada a arquitetura de segurança mostrada no AzureCon Deep Dive, consulte o artigo sobre a implementação de uma arquitetura de [segurança em camadas](app-service-app-service-environment-layered-security.md) com Ambientes de Serviço de Aplicações.

As aplicações em execução em Ambientes de Serviço de Aplicações podem ter o seu acesso fechado por dispositivos a montante, tais como firewalls de aplicações web (WAF).  O artigo sobre [a configuração de um WAF para Ambientes](app-service-app-service-environment-web-application-firewall.md) de Serviço de Aplicações cobre este cenário.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Recursos Compute dedicados

Todos os recursos computacionais de um App Service Environment são dedicados exclusivamente a uma única subscrição, e um App Service Environment pode ser configurado com até 50 (50) recursos de computação para uso exclusivo por uma única aplicação.

Um Ambiente de Serviço de Aplicações é composto por um conjunto de recursos computacionais frontais, bem como um a três conjuntos de recursos de computação de trabalhadores.

O pool frontal contém recursos computacionais responsáveis pela rescisão do SSL, bem como o equilíbrio automático de carga dos pedidos de aplicações dentro de um Ambiente de Serviço de Aplicações.

Cada grupo de trabalhadores contém recursos computacionais atribuídos aos Planos de Serviço de [Aplicações,][AppServicePlan]que por sua vez contêm uma ou mais aplicações do Serviço de Aplicações Azure.  Uma vez que pode haver até três piscinas de trabalhadores diferentes em um Ambiente de Serviço de Aplicações, você tem a flexibilidade para escolher diferentes recursos de computação para cada conjunto de trabalhadores.  

Por exemplo, isto permite criar um pool de trabalhadores com recursos de computação menos potentes para planos de serviço de aplicações destinados a aplicações de desenvolvimento ou teste.  Um segundo (ou mesmo terceiro) conjunto de trabalhadores poderia usar recursos computacionais mais poderosos destinados a Planos de Serviço de Aplicações que executam aplicações de produção.

Para mais detalhes sobre a quantidade de recursos computacionais disponíveis para as piscinas frontais e operárias, consulte Como Configurar um Ambiente de Serviço de [Aplicações.][HowToConfigureanAppServiceEnvironment]  

Para mais detalhes sobre os tamanhos de recursos de cálculo disponíveis suportados num Ambiente de Serviço de Aplicações, consulte a página de Preços do [Serviço de Aplicações][AppServicePricing] e reveja as opções disponíveis para ambientes de serviço de aplicações no nível de preços Premium.

## <a name="virtual-network-support"></a>Suporte de rede virtual

Um Ambiente de Serviço de Aplicações pode ser criado **numa** rede virtual do Azure Resource Manager, **ou** numa rede virtual modelo de implementação clássica[(mais informações em redes virtuais).][MoreInfoOnVirtualNetworks]  Uma vez que um App Service Environment existe sempre numa rede virtual, e mais precisamente dentro de uma subnet de uma rede virtual, pode aproveitar as funcionalidades de segurança das redes virtuais para controlar as comunicações de rede de entrada e saída.  

Um Ambiente de Serviço de Aplicações pode ser virado para a Internet com um endereço IP público, ou virado interno com apenas um endereço Azure Internal Load Balancer (ILB).

Pode utilizar [grupos][NetworkSecurityGroups] de segurança de rede para restringir as comunicações de rede de entrada à subnet onde reside um Ambiente de Serviço sinuoso.  Isto permite-lhe executar aplicações atrás de dispositivos e serviços a montante, tais como firewalls de aplicações web e fornecedores de redes SaaS.

As aplicações também precisam de aceder frequentemente a recursos da empresa, tais como bases de dados internas e serviços Web.  Uma abordagem comum é disponibilizar estes pontos finais apenas ao tráfego interno da rede que flui dentro de uma rede virtual Azure.  Uma vez que um App Service Environment é unido à mesma rede virtual que os serviços internos, as aplicações que executam no ambiente podem aceder aos mesmos, incluindo pontos finais acessíveis através das ligações [Site-to-Site][SiteToSite] e [Azure ExpressRoute.][ExpressRoute]

Para mais detalhes sobre como os ambientes de serviço de aplicação funcionam com redes virtuais e redes no local, consulte os seguintes artigos sobre Arquitetura de [Rede,][NetworkArchitectureOverview] [Controlando][ControllingInboundTraffic]o Tráfego de Entrada e [Conectando-se de forma segura a backends][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Introdução

Para começar com ambientes de serviço de aplicação, veja [como criar um ambiente de serviço de aplicações][HowToCreateAnAppServiceEnvironment]

Para uma visão geral da arquitetura da rede App Service Environment, consulte o artigo Visão Geral da [Arquitetura da Rede.][NetworkArchitectureOverview]

Para mais detalhes sobre a utilização de um Ambiente de Serviço de Aplicações com ExpressRoute, consulte o seguinte artigo sobre [ambientes][NetworkConfigDetailsForExpressRoute]de serviço express route e app .

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