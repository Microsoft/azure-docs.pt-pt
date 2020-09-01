---
title: Introdução à Nuvem de primavera de Azure
description: Aprenda as funcionalidades e benefícios da Azure Spring Cloud para implementar e gerir aplicações java Spring em Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: aa3f1032301224701f5bfc08807e89194f263da6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255244"
---
# <a name="what-is-azure-spring-cloud"></a>O que é o Azure Spring Cloud?

O Azure Spring Cloud facilita a implementação de aplicações de microserviços baseadas em Boot de Mola para o Azure com alterações de código zero.  A Azure Spring Cloud gere a infraestrutura das aplicações Spring Cloud, para que os desenvolvedores possam focar-se no seu código.  A Cloud spring fornece gestão de ciclo de vida utilizando monitorização e diagnósticos abrangentes, gestão de configuração, descoberta de serviços, integração de CI/CD, implementações verde-azuladas e muito mais.

Como parte do ecossistema Azure, a Azure Spring Cloud permite uma ligação fácil a outros serviços Azure, incluindo armazenamento, bases de dados, monitorização e muito mais.

Esta introdução descreve o servidor de configuração Azure Spring Cloud, como ativar implementações azuis/verdes, aplicações de escala e como monitorizar o desempenho da aplicação.

## <a name="spring-cloud-config-server"></a>Servidor Config de Nuvem de primavera

O Azure Spring Cloud Config Server fornece uma configuração externa num sistema distribuído com suporte ao servidor e ao cliente.  O Azure Spring Cloud Config Server é uma localização central para gerir propriedades de aplicações em todos os ambientes. Para mais informações consulte a [referência do Servidor Config da Nuvem de primavera](https://spring.io/projects/spring-cloud-config.md). 

## <a name="enable-bluegreen-deployments"></a>Ativar implantações azuis/verdes

A Azure Spring Cloud suporta implementações azuis/verdes para a libertação e atualização do código para ambientes de produção.  Este padrão de gestão de alterações permite que os desenvolvedores implementem funcionalidades e alterações de código com a segurança de um recuo imediato quando necessário.  Os desenvolvedores podem concentrar-se em escrever código com vários ambientes de produção para atualizar ou reverter alterações de código sem interromper a aplicação.  Para saber mais sobre ambientes de encenação e implantações azuis/verdes, visite este [artigo Como-Para.](spring-cloud-howto-staging-environment.md)

## <a name="automate-cicd-pipelines"></a>Automatizar os gasodutos CI/CD

A Azure Spring Cloud proporciona integração com Azure DevOps usando o Azure CLI.  Utilizando Azure DevOps, pode automatizar a integração de códigos e a sua implementação na sua aplicação primavera.  Para saber mais, visite este [artigo.](spring-cloud-howto-cicd.md)

## <a name="scale-your-application"></a>Dimensione a sua aplicação

O Azure Spring Cloud permite-lhe escalar facilmente os micro-serviços no seu painel Azure Spring Cloud.  Tanto o número de VCPUs como a quantidade de memória disponível para os seus micro-serviços podem ser dimensionado para cima ou para baixo de acordo com os seus requisitos.  A escalação entra em vigor em segundos e não requer alterações de código ou redistribuição.  Para saber mais, complete este [tutorial.](spring-cloud-tutorial-scale-manual.md)

## <a name="application-monitoring"></a>Monitorização da aplicação

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Monitorize a sua aplicação utilizando o rastreio distribuído e o Azure App Insights

As ferramentas de rastreio distribuídas da Spring Cloud permitem aos desenvolvedores depurar e monitorizar as complexas interligações entre microserviços numa aplicação.  Ao integrar [a Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) com o Azure's Application [Insights,](../azure-monitor/insights/insights-overview.md)o Azure fornece uma poderosa capacidade de rastreio distribuído diretamente do portal Azure.  Para saber mais, complete este [tutorial.](spring-cloud-tutorial-distributed-tracing.md)

## <a name="next-steps"></a>Passos seguintes
Para começar, complete o quickstart da Cloud da primavera:
> [!div class="nextstepaction"]
> [Quickstart: Implemente a sua primeira aplicação Azure Spring Cloud](spring-cloud-quickstart.md)

Mais amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
