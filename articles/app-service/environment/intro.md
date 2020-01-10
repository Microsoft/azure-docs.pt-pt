---
title: Introdução
description: Saiba como os ambientes de serviço Azure App ajudam a dimensionar, proteger e otimizar seus aplicativos em um ambiente totalmente isolado e dedicado.
author: ccompy
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.topic: overview
ms.date: 04/19/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0fc5c32570e5a5920d1616a522120ea5d5b251d8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768332"
---
# <a name="introduction-to-the-app-service-environments"></a>Introdução aos Ambientes de Serviço de Aplicações #
 
## <a name="overview"></a>Visão geral ##

O Ambiente de Serviço de Aplicações do Azure é uma funcionalidade do Serviço de Aplicações do Azure que fornece um ambiente totalmente isolado e dedicado para executar com segurança as aplicações do Serviço de Aplicações em grande escala. Esta capacidade pode alojar:

* Aplicações Web do Windows
* Aplicações Web do Linux 
* Contentores do Docker
* Aplicações Móveis
* Funções

Os ambientes de Serviço de Aplicações (ASEs) são adequados para cargas de trabalho de aplicações que exigem:

* Dimensionamento muito elevado.
* Isolamento e acesso a uma rede segura.
* Utilização de memória elevada.

Os clientes podem criar vários ASEs numa única região do Azure ou em várias regiões do Azure. Esta flexibilidade torna os ASEs ideais para dimensionar horizontalmente camadas de aplicações sem monitorização de estado para suportar cargas de trabalho RPS elevadas.

Os ASEs são isolados para executarem apenas as aplicações de um único cliente e são sempre implementados numa rede virtual. Os clientes têm um controlo otimizado sobre o tráfego de rede de aplicações de entrada e saída. As aplicações podem estabelecer ligações seguras de alta velocidade em VPNs aos recursos da empresa no local.

* O ASE vem com o seu próprio escalão de preço. Saiba como a [Oferta isolada](https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment) ajuda a orientar a segurança e a hiperescala.
* Os [Ambientes do Serviço de Aplicações v2](https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment) fornecem um ambiente para proteger as aplicações numa sub-rede da sua rede e fornece a sua própria implementação privada do Serviço de Aplicações do Azure.
* É possível utilizar múltiplos ASEs para dimensionar horizontalmente. Para obter mais informações, veja [como configurar requisitos de espaço de uma aplicação geodistribuída](app-service-app-service-environment-geo-distributed-scale.md).
* Os ASEs podem ser utilizados para configurar a arquitetura de segurança, conforme apresentado no AzureCon Deep Dive. Para ver como a arquitetura de segurança apresentada no AzureCon Deep Dive foi configurada, veja o [artigo sobre como implementar uma arquitetura de segurança por camadas](app-service-app-service-environment-layered-security.md) com ambientes de Serviço de Aplicações.
* As aplicações em execução em ASEs podem ter o seu acesso protegido por dispositivos a montante, tais como firewalls de aplicações Web (WAFs). Para obter mais informações, consulte [Firewall do aplicativo Web (WAF)][AppGW].
* Ambientes de serviço de aplicativo podem ser implantados em Zonas de Disponibilidade (AZ) usando a fixação de zona.  Consulte [ambiente do serviço de aplicativo suporte para zonas de disponibilidade][ASEAZ] para obter mais detalhes.

## <a name="dedicated-environment"></a>Ambiente dedicado ##

Um ASE é dedicado exclusivamente a uma subscrição individual e pode alojar 100 instâncias do Plano do Serviço de Aplicações. O intervalo pode abranger de 100 instâncias num único plano do Serviço de Aplicações até 100 planos do Serviço de Aplicações de instância única, e tudo o que está dentro deste intervalo.

Um ASE é composto por front-ends e trabalhos. Os front-ends são responsáveis pela terminação de HTTP/HTTPS e pelo balanceamento de carga automático de pedidos de aplicações num ASE. Os front-ends são adicionados automaticamente à medida que os planos do Serviço de Aplicações no ASE são aumentados horizontalmente.

Os trabalhos são funções que alojam aplicações de cliente. Os trabalhos estão disponíveis em três tamanhos fixos:

* Uma vCPU/3,5 GB de RAM
* Duas vCPU/7 GB de RAM
* Quatro vCPU/14 GB de RAM

Os clientes não precisam de gerir os front-ends e os trabalhos. Toda a infraestrutura é automaticamente adicionada à medida que os clientes aumentam horizontalmente os respetivos planos do Serviço de Aplicações. À medida que os planos do Serviço de Aplicações são criados ou reduzidos verticalmente num ASE, a infraestrutura necessária é adicionada ou removida, conforme adequado.

Existe uma tarifa mensal fixa para um ASE que paga a infraestrutura e não muda com base no tamanho do ASE. Além disso, existe um custo por vCPU do plano do Serviço de Aplicações. Todas as aplicações alojadas num ASE estão na SKU de preços Isolada. Para obter informações sobre os preços de um ASE, consulte a página [preços do serviço de aplicativo][Pricing] e examine as opções disponíveis para ases.

## <a name="virtual-network-support"></a>Suporte de rede virtual ##

O recurso ASE é uma implantação do serviço de Azure App diretamente na rede virtual Azure Resource Manager de um cliente. Para saber mais sobre as redes virtuais do Azure, veja as [Perguntas Frequentes sobre as redes virtuais do Azure](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/). Um ASE existe sempre numa rede virtual e, mais precisamente, numa sub-rede de uma rede virtual. Pode utilizar as funcionalidades de segurança das redes virtuais para controlar as comunicações de rede de entrada e saída para as suas aplicações.

Um ASE pode ter acesso à Internet com um endereço IP público ou ter acesso interno apenas com um endereço do balanceador de carga interno (ILB) do Azure.

Os [grupos de segurança de rede][NSGs] restringem as comunicações de rede de entrada para a sub-rede em que reside um ASE. Pode utilizar NSGs para executar aplicações atrás de dispositivos e serviços a montante, tais como WAFs e fornecedores de SaaS de rede.

As aplicações também precisam de aceder frequentemente a recursos da empresa, tais como bases de dados internas e serviços Web. Se implementar o ASE numa rede virtual que tenha uma ligação VPN à rede no local, as aplicações no ASE podem aceder aos recursos no local. Esta capacidade é verdadeira, independentemente de a VPN ser uma [rede de VPNs](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site) ou uma VPN [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Para obter mais informações sobre como o ASEs funciona com redes virtuais e redes locais, consulte [ambiente do serviço de aplicativo considerações de rede][ASENetwork].

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud/player]

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicações v1 ##

O Ambiente de Serviço de Aplicações tem duas versões: ASEv1 e ASEv2. As informações anteriores tiveram como base a versão ASEv2. Esta secção mostra as diferenças entre as versões ASEv1 e ASEv2. 

No ASEv1, é necessário gerir todos os recursos manualmente. Tal inclui os front-ends, os trabalhos e os endereços IP utilizados para SSL baseado em IP. Para poder aumentar horizontalmente o seu plano do Serviço de Aplicações, primeiro tem de aumentar horizontalmente o conjunto de processos de trabalho onde pretende alojá-lo.

O ASEv1 utiliza um modelo de preços diferente do ASEv2. No ASEv1, paga por cada vCPU alocada. Tal inclui as vCPUs utilizadas para front-ends ou trabalhos que não estejam a alojar quaisquer cargas de trabalho. No ASEv1, o tamanho de dimensionamento máximo predefinido de um ASE é 55 anfitriões no total. Tal inclui os trabalhos e os front-ends. Uma vantagem do ASEv1 é que pode ser implementado numa rede virtual clássica e numa rede virtual do Resource Manager. Para saber mais sobre o ASEv1, consulte [introdução ao ambiente do serviço de aplicativo v1][ASEv1Intro].

<!--Links-->
[App Service Environments v2]: https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment
[Isolated offering]: https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/waf-overview.md
[ASEAZ]: https://azure.github.io/AppService/2019/12/12/App-Service-Environment-Support-for-Availability-Zones.html
