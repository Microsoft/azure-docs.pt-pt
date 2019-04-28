---
title: Introdução à aplicação de serviço ambiente v1 - Azure
description: Saiba mais sobre a funcionalidade de v1 do ambiente de serviço de aplicações que fornece a unidades de escala de seguro, associada a VNet e dedicado para executar todas as suas aplicações.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 2bb1a9c3922f435b6be78614aacff6e85bf475ff
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130743"
---
# <a name="introduction-to-app-service-environment-v1"></a>Introdução à aplicação v1 do ambiente de serviço

> [!NOTE]
> Este artigo é sobre o ambiente de serviço de aplicações v1.  Existe uma versão mais recente do ambiente de serviço de aplicação que é mais fácil de usar e é executado numa infraestrutura mais poderosa. Para saber mais sobre o novo início de versão com o [introdução ao ambiente de serviço de aplicações](intro.md).

## <a name="overview"></a>Descrição geral

Um ambiente de serviço de aplicações é uma [Premium] [ PremiumTier] da opção do plano de serviço [App Service do Azure](../overview.md) que fornece um ambiente totalmente isolado e dedicado para executar com segurança Aplicações de serviço de aplicações do Azure em grande escala, incluindo aplicações Web, aplicações móveis e API Apps.  

Ambientes de serviço de aplicações são ideais para cargas de trabalho de aplicação requerem:

* Dimensionamento muito elevado
* Isolamento e acesso de rede segura

Os clientes podem criar vários ambientes de serviço de aplicações numa única região do Azure, bem como em várias regiões do Azure.  Desta forma, ambientes de serviço de aplicações ideal para dimensionar horizontalmente camadas da aplicação de estado sem para oferecer suporte a cargas de trabalho RPS elevadas.

Ambientes de serviço de aplicações são isolados para executarem apenas as aplicações de um único cliente e são sempre implementados numa rede virtual.  Os clientes têm um controle refinado sobre o tráfego de rede aplicação de entrada e saída e as aplicações podem estabelecer conexões seguras de alta velocidade nas redes virtuais para recursos da empresa no local.

Para uma visão geral de como os ambientes do serviço de aplicações ativar escala elevada e proteger o acesso de rede, consulte a [AzureCon Deep Dive] [ AzureConDeepDive] em ambientes de serviço de aplicações!

Para uma análise detalhada sobre como aumentar horizontalmente a utilizar vários ambientes de serviço de aplicações Consulte o artigo sobre como configurar uma [requisitos de espaço de aplicação distribuída geograficamente][GeodistributedAppFootprint].

Para ver como a arquitetura de segurança apresentada no AzureCon Deep Dive foi configurada, consulte o artigo sobre como implementar um [disposto em camadas da arquitetura de segurança](app-service-app-service-environment-layered-security.md) com ambientes de serviço de aplicações.

Aplicações em execução em ambientes de serviço de aplicações podem ter seu acesso protegido por dispositivos a montante, tais como firewalls de aplicações web (WAF).  O artigo sobre [configurar uma WAF para ambientes de serviço de aplicações](app-service-app-service-environment-web-application-firewall.md) aborda este cenário.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Recursos de computação dedicados

Todos os recursos de computação num ambiente de serviço de aplicações são dedicados exclusivamente a uma subscrição individual e um ambiente de serviço de aplicações podem ser configurado com recursos de computação até cinquenta (50) para utilização exclusiva por um único aplicativo.

Um ambiente de serviço de aplicações é composto por um agrupamento de recursos de computação de front-end, bem como os agrupamentos de recursos de computação de trabalho de um a três.

O conjunto de front-end contém recursos de computação responsáveis pela terminação de SSL como balanceamento de carga bem automático de pedidos de aplicações dentro de um ambiente de serviço de aplicações.

Cada conjunto de trabalho contém recursos de computação alocados a [planos do serviço de aplicações][AppServicePlan], que por sua vez, contêm uma ou mais aplicações de serviço de aplicações do Azure.  Uma vez que pode haver até três conjuntos de trabalho diferentes num ambiente de serviço de aplicações, tem a flexibilidade para escolher os recursos de computação diferentes para cada conjunto de trabalho.  

Por exemplo, isto permite-lhe criar um conjunto de trabalho com menos poderosos recursos de computação para planos de serviço de aplicações destinadas a aplicações de desenvolvimento ou teste.  Um conjunto de trabalhos de segundo (ou até mesmo terceiro) poderia usar mais poderosos recursos de computação que se destina a planos de serviço de aplicações em execução de aplicações de produção.

Para obter mais detalhes sobre a quantidade de recursos de computação disponíveis para os conjuntos de front-end e de trabalho, consulte [como configurar um ambiente de serviço de aplicações][HowToConfigureanAppServiceEnvironment].  

Para obter detalhes sobre os tamanhos de recursos de computação disponíveis suportados num ambiente de serviço de aplicações, consulte a [preços do App Service] [ AppServicePricing] página e reveja as opções disponíveis para ambientes de serviço de aplicações no Escalão de preço Premium.

## <a name="virtual-network-support"></a>Suporte de rede virtual

Um ambiente de serviço de aplicações podem ser criado no **ambos** uma rede virtual do Azure Resource Manager, **ou** uma rede virtual do modelo de implementação clássica ([obter mais informações sobre redes virtuais] [MoreInfoOnVirtualNetworks]).  Uma vez que um ambiente de serviço de aplicações existe sempre numa rede virtual e, mais precisamente, numa sub-rede de uma rede virtual, pode aproveitar os recursos de segurança de redes virtuais para controlar as comunicações de rede de entrada e saída.  

Um ambiente de serviço de aplicações pode ser qualquer um com acesso com um endereço IP público ou interno com acesso com apenas um endereço de Balanceador de carga interno do Azure (ILB) de Internet.

Pode usar [grupos de segurança de rede] [ NetworkSecurityGroups] restringir a comunicação de rede de entrada para a sub-rede onde reside um ambiente de serviço de aplicações.  Isto permite-lhe executar aplicações atrás de dispositivos a montante e serviços, tais como firewalls de aplicações web e fornecedores de SaaS de rede.

As aplicações também precisam de aceder frequentemente a recursos da empresa, tais como bases de dados internas e serviços Web.  Uma abordagem comum é tornar estes pontos finais disponíveis apenas para tráfego de rede interno, que flui dentro de uma rede virtual do Azure.  Depois de um ambiente de serviço de aplicações é associado à mesma rede virtual que os serviços internos, as aplicações em execução no ambiente podem aceder aos mesmos, incluindo acessíveis através de pontos finais [Site a Site] [ SiteToSite] e [Azure ExpressRoute] [ ExpressRoute] ligações.

Para obter mais detalhes sobre como funcionam os ambientes do serviço de aplicações com redes virtuais e redes no local Consulte os artigos seguintes no [arquitetura de rede][NetworkArchitectureOverview], [controlar de entrada Tráfego][ControllingInboundTraffic], e [em segurança ao ligar ao back-ends][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Introdução

Para começar a utilizar com ambientes de serviço de aplicações, veja [como para criar uma aplicação de ambiente de serviço][HowToCreateAnAppServiceEnvironment]

Para uma descrição geral da arquitetura de rede do ambiente de serviço de aplicações, consulte a [descrição geral da arquitetura de rede] [ NetworkArchitectureOverview] artigo.

Para obter detalhes sobre como utilizar um ambiente de serviço de aplicações com o ExpressRoute, consulte o seguinte artigo no [Expressroute e ambientes de serviço de aplicações][NetworkConfigDetailsForExpressRoute].

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