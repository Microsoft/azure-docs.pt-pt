---
title: Introdução à Nuvem de primavera de Azure
description: Aprenda as funcionalidades e benefícios da Azure Spring Cloud para implementar e gerir aplicações java Spring em Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 3289233cb796d58972c2afd9a223075cedff265d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075984"
---
# <a name="what-is-azure-spring-cloud"></a>O que é o Azure Spring Cloud?

O Azure Spring Cloud facilita a implementação de aplicações de microserviços baseadas em Boot de Mola para o Azure com alterações de código zero.  A Azure Spring Cloud gere o ciclo de vida das aplicações Da Nuvem de primavera, para que os desenvolvedores possam focar-se no seu código.  A Cloud spring fornece gestão de ciclo de vida utilizando monitorização e diagnósticos abrangentes, gestão de configuração, descoberta de serviços, integração de CI/CD, implementações verde-azuladas e muito mais.

Como parte do ecossistema Azure, a Azure Spring Cloud permite uma ligação fácil a outros serviços Azure, incluindo armazenamento, bases de dados, monitorização e muito mais.

Azure Spring Cloud é atualmente oferecido como uma pré-visualização pública. As ofertas de pré-visualização públicas permitem que os clientes experimentem novas funcionalidades antes do seu lançamento oficial.  As funcionalidades e serviços de pré-visualização do público não se destinam ao uso da produção.  Para mais informações sobre o suporte durante as pré-visualizações, por favor reveja as nossas [PERGUNTAS Frequentes](https://azure.microsoft.com/support/faq/) ou preencha um [pedido de Apoio](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) para saber mais.

Para começar, complete o quickstart da Cloud spring usando o [Azure CLI,](spring-cloud-quickstart-launch-app-cli.md)o [portal Azure](spring-cloud-quickstart-launch-app-portal.md), ou [Maven](spring-cloud-quickstart-launch-app-maven.md).

Mais amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

## <a name="application-configuration"></a>Configuração de aplicação

### <a name="spring-cloud-config-server"></a>Servidor Config de Nuvem de primavera

O Azure Spring Cloud Config Server fornece uma configuração externa num sistema distribuído com suporte ao servidor e ao cliente.  O Config Server fornece uma localização central para gerir propriedades de aplicações em todos os ambientes.  Para saber mais, visite a referência do [Servidor Config da Nuvem de primavera](https://spring.io/projects/spring-cloud-config.md) e complete o tutorial.

### <a name="enable-bluegreen-deployments"></a>Ativar implantações azuis/verdes

A Azure Spring Cloud suporta implementações azuis/verdes para a libertação e atualização do código para ambientes de produção.  Aproveitar este padrão de gestão de alterações permite que os desenvolvedores implementem funcionalidades e alterações de código com a segurança de um recuo imediato quando necessário.  O Azure permite que os desenvolvedores se concentrem na escrita do código, gerindo vários ambientes de produção e facilitando a atualização ou reversão de alterações de código sem interromper a aplicação.  Para saber mais sobre ambientes de encenação e implantações azuis/verdes, visite este [artigo Como-Para.](spring-cloud-howto-staging-environment.md)

### <a name="automate-cicd-pipelines"></a>Automatizar os gasodutos CI/CD

A Azure Spring Cloud proporciona integração com Azure DevOps usando o Azure CLI.  Utilizando Azure DevOps, pode automatizar a integração de códigos e a sua implementação na sua aplicação primavera.  Para saber mais, visite este [artigo.](spring-cloud-howto-cicd.md)

### <a name="scale-your-application"></a>Dimensione a sua aplicação

O Azure Spring Cloud permite-lhe escalar facilmente os micro-serviços no seu painel Azure Spring Cloud.  Tanto o número de VCPUs como a quantidade de memória disponível para os seus micro-serviços podem ser dimensionado para cima ou para baixo de acordo com os seus requisitos.  A escalação entra em vigor em segundos e não requer alterações de código ou redistribuição.  Para saber mais, complete este [tutorial.](spring-cloud-tutorial-scale-manual.md)

## <a name="application-monitoring"></a>Monitorização de Aplicações

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Monitorize a sua aplicação utilizando o rastreio distribuído e o Azure App Insights

As ferramentas de rastreio distribuídas da Spring Cloud permitem aos desenvolvedores depurar e monitorizar as complexas interligações entre microserviços numa aplicação.  Ao integrar [a Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) com o Azure's Application [Insights,](../azure-monitor/insights/insights-overview.md)o Azure fornece uma poderosa capacidade de rastreio distribuído diretamente do portal Azure.  Para saber mais, complete este [tutorial.](spring-cloud-tutorial-distributed-tracing.md)

## <a name="next-steps"></a>Passos seguintes

- [Lance a sua aplicação Cloud primavera a partir do CLI](spring-cloud-quickstart-launch-app-cli.md)
