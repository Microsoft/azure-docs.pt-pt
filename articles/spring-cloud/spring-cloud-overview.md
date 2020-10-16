---
title: Introdução à Nuvem de primavera de Azure
description: Aprenda as funcionalidades e benefícios da Azure Spring Cloud para implementar e gerir aplicações java Spring em Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 159851f5b1ca38cc34da785b39229a6a91e1e4dd
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090697"
---
# <a name="what-is-azure-spring-cloud"></a>O que é o Azure Spring Cloud?

O Azure Spring Cloud facilita a implementação de aplicações de microserviços baseadas em Boot de Mola para o Azure com alterações de código zero.  A Azure Spring Cloud gere a infraestrutura das aplicações Spring Cloud, para que os desenvolvedores possam focar-se no seu código.  A Cloud spring fornece gestão de ciclo de vida utilizando monitorização e diagnósticos abrangentes, gestão de configuração, descoberta de serviços, integração de CI/CD, implementações verde-azuladas e muito mais.

A Azure Spring Cloud suporta tanto as aplicações Java [Spring Boot](https://spring.io/projects/spring-boot) como ASP.NET Core [Steeltoe.](https://steeltoe.io/) O apoio da Steeltoe é atualmente oferecido como uma pré-visualização pública. As ofertas de pré-visualização públicas permitem-lhe experimentar novas funcionalidades antes do seu lançamento oficial.  As funcionalidades e serviços de pré-visualização do público não se destinam ao uso da produção.  Para mais informações, consulte as [FAQ](https://azure.microsoft.com/support/faq/) ou apresente um [pedido de Apoio.](../azure-portal/supportability/how-to-create-azure-support-request.md)

Como parte do ecossistema Azure, a Azure Spring Cloud permite uma ligação fácil a outros serviços Azure, incluindo armazenamento, bases de dados, monitorização e muito mais.

Esta introdução descreve as seguintes capacidades de Azure Spring Cloud:

* Servidor de configuração
* Implantações azuis/verdes
* Escalagem de aplicações
* Integração com o Azure DevOps
* Monitorização da aplicação

## <a name="spring-cloud-config-server"></a>Servidor Config de Nuvem de primavera

O Azure Spring Cloud Config Server fornece uma configuração externa num sistema distribuído com suporte ao servidor e ao cliente.  O Azure Spring Cloud Config Server é uma localização central para gerir propriedades de aplicações em todos os ambientes. Para mais informações consulte a [referência do Servidor Config da Nuvem de primavera](https://spring.io/projects/spring-cloud-config). 

## <a name="bluegreen-deployments"></a>Implantações azuis/verdes

A Azure Spring Cloud suporta implementações azuis/verdes para a libertação e atualização do código para ambientes de produção.  Este padrão de gestão de alterações permite que os desenvolvedores implementem funcionalidades e alterações de código com a segurança de um recuo imediato quando necessário.  Os desenvolvedores podem concentrar-se em escrever código com vários ambientes de produção para atualizar ou reverter alterações de código sem interromper a aplicação.  Para saber mais sobre ambientes de encenação e implantações azuis/verdes, visite este [artigo Como-Para.](spring-cloud-howto-staging-environment.md)

## <a name="cicd-pipeline-automation"></a>Automação do gasoduto CI/CD

A Azure Spring Cloud proporciona integração com Azure DevOps usando o Azure CLI.  Utilizando Azure DevOps, pode automatizar a integração de códigos e a sua implementação na sua aplicação primavera.  Para saber mais, visite este [artigo.](spring-cloud-howto-cicd.md)

## <a name="application-scaling"></a>Escalagem de aplicações

O Azure Spring Cloud permite-lhe escalar facilmente os micro-serviços no seu painel Azure Spring Cloud.  Tanto o número de VCPUs como a quantidade de memória disponível para os seus micro-serviços podem ser dimensionado para cima ou para baixo de acordo com os seus requisitos.  A escalação entra em vigor em segundos e não requer alterações de código ou redistribuição.  Para saber mais, complete este [tutorial.](spring-cloud-tutorial-scale-manual.md)

## <a name="application-monitoring"></a>Monitorização da aplicação

As ferramentas de rastreio distribuídas da Spring Cloud permitem aos desenvolvedores depurar e monitorizar as complexas interligações entre microserviços numa aplicação.  Ao integrar [a Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) com o Azure's Application [Insights,](../azure-monitor/insights/insights-overview.md)o Azure fornece uma poderosa capacidade de rastreio distribuído diretamente do portal Azure.  Para saber mais, complete este [tutorial.](spring-cloud-tutorial-distributed-tracing.md)

## <a name="next-steps"></a>Passos seguintes

Para começar, complete o [quickstart da Cloud da primavera](spring-cloud-quickstart.md)

As amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).