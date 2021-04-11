---
title: Introdução à Nuvem de primavera de Azure
description: Aprenda as funcionalidades e benefícios da Azure Spring Cloud para implementar e gerir aplicações java Spring em Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 12/02/2020
ms.author: brendm
ms.custom: devx-track-java, contperf-fy21q2
customer intent: As an Azure Cloud user, I want to deploy, run, and monitor Spring Boot microservices.
ms.openlocfilehash: a92e535cb1edeb0eeaa285e442b4b24766e20ead
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879311"
---
# <a name="what-is-azure-spring-cloud"></a>O que é o Azure Spring Cloud?

O Azure Spring Cloud facilita a implementação de aplicações de microserviços Spring Boot para o Azure sem alterações de código.  O serviço gere a infraestrutura das aplicações Spring Cloud para que os desenvolvedores possam focar-se no seu código.  A Azure Spring Cloud fornece gestão de ciclo de vida utilizando monitorização e diagnósticos abrangentes, gestão de configuração, descoberta de serviços, integração de CI/CD, implementações verde-azuladas, e muito mais.

## <a name="why-use-azure-spring-cloud"></a>Porquê usar a Nuvem de primavera de Azure?

A implementação de aplicações para a Azure Spring Cloud tem muitos benefícios.  Pode:
* Migrar eficientemente as aplicações de primavera existentes e gerir o dimensionamento de nuvens e os custos.
* Modernizar aplicativos com padrões de Cloud spring para melhorar a agilidade e velocidade de entrega.
* Corra Java à escala de nuvens e conduza um uso mais alto sem infraestruturas complicadas.
* Desenvolver e implantar rapidamente sem dependências de contentorização.
* Monitorize as cargas de trabalho de produção de forma eficiente e sem esforço.

A Azure Spring Cloud suporta tanto as aplicações Java [Spring Boot](https://spring.io/projects/spring-boot) como ASP.NET Core [Steeltoe.](https://steeltoe.io/) O apoio da Steeltoe é atualmente oferecido como uma pré-visualização pública. As ofertas de pré-visualização públicas permitem-lhe experimentar novas funcionalidades antes do seu lançamento oficial. As funcionalidades e serviços de pré-visualização do público não se destinam ao uso da produção. Para mais informações, consulte as [FAQ](https://azure.microsoft.com/support/faq/) ou apresente um [pedido de Apoio.](../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="service-overview"></a>Descrição geral do serviço

Como parte do ecossistema Azure, a Azure Spring Cloud permite uma ligação fácil a outros serviços Azure, incluindo armazenamento, bases de dados, monitorização e muito mais.  

  ![Visão geral da nuvem de primavera de Azure](media/spring-cloud-principles/azure-spring-cloud-overview.png)

* O Azure Spring Cloud é um serviço totalmente gerido para aplicações Spring Boot que permite focar-se na construção e execução de apps sem o incómodo de gerir infraestruturas.

* Basta implementar os seus JARs ou código, e o Azure Spring Cloud irá ligar automaticamente as suas aplicações com o tempo de funcionamento do serviço Spring e o ciclo de vida da aplicação incorporada.

* A monitorização é simples. Após a implementação, pode monitorizar o desempenho da aplicação, corrigir erros e melhorar rapidamente as aplicações. 

* Integração total dos ecossistemas e serviços da Azure.

* A Azure Spring Cloud é uma empresa pronta com infraestruturas totalmente geridas, gestão de ciclo de vida incorporado e facilidade de monitorização.

## <a name="documentation-overview"></a>Visão geral da documentação
Esta documentação inclui secções que explicam como começar e alavancar os serviços da Azure Spring Cloud.

* Introdução
    * [Iniciar a sua primeira aplicação](spring-cloud-quickstart.md)
    * [Fornecimento de um serviço Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md)
    * [Configurar o servidor de configuração]()
    * [Construir e implementar aplicativos](spring-cloud-quickstart-deploy-apps.md)
    * [Use métricas de troncos e rastreio](spring-cloud-quickstart-logs-metrics-tracing.md)
* Procedimentos
    * [Desenvolver:](how-to-prepare-app-deployment.md)Preparar uma aplicação java spring existente para implantação na Nuvem de primavera de Azure. Quando configurado corretamente, o Azure Spring Cloud fornece serviços robustos para monitorizar, escalar e atualizar aplicações Java Spring Cloud.
    * [Implementar](spring-cloud-howto-staging-environment.md): Como configurar uma colocação de encenação utilizando o padrão de implantação azul-verde na Nuvem de primavera de Azure. A implementação azul/verde é um padrão de Entrega Contínua de DevOps do Azure que se baseia em manter uma versão já existente ativa (azul) enquanto é implementada uma nova.
    * [Configure aplicativos](spring-cloud-howto-start-stop-delete.md): Iniciar, parar e eliminar as suas aplicações Azure Spring Cloud. Altere o estado de uma aplicação em Azure Spring Cloud utilizando o portal Azure ou o Azure CLI.
    * [Escala](spring-cloud-howto-scale-manual.md): Dimensione qualquer aplicação de microserviço utilizando o painel Azure Spring Cloud no portal Azure ou utilizando configurações de autoescala. Os IPs públicos estão disponíveis para comunicar com recursos externos, tais como bases de dados, armazenamento e cofres-chave.
    * [Aplicações monitoras](spring-cloud-howto-distributed-tracing.md): Ferramentas de rastreio distribuídas para depurar e monitorizar facilmente problemas complexos. AZure Spring Cloud integra a Spring Cloud Sleuth com os Insights de Aplicação da Azure. Esta integração fornece uma poderosa capacidade de rastreio distribuído a partir do portal Azure.
    * [Aplicações seguras](spring-cloud-howto-enable-system-assigned-managed-identity.md): Os recursos Azure fornecem uma identidade gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código.
    * [Integração com outros serviços Azure](spring-cloud-howto-bind-cosmos.md): Em vez de configurar manualmente as suas aplicações Boot de Mola, pode ligar automaticamente os serviços Azure selecionados às suas aplicações, por exemplo, vinculando a sua aplicação a uma base de dados DB Azure Cosmos.
    * [Automatizar:](spring-cloud-howto-cicd.md)Ferramentas de integração contínua e entrega contínua permitem-lhe implementar rapidamente atualizações para aplicações existentes com o mínimo de esforço e risco. A Azure DevOps ajuda a organizar e controlar estas tarefas-chave. 
    * [Resolução de problemas](spring-cloud-howto-self-diagnose-solve.md): Os diagnósticos Azure Spring Cloud proporcionam uma experiência interativa para ajudar a resolver aplicações. Não é necessária qualquer configuração. Quando encontra problemas, o diagnóstico Azure Spring Cloud identifica problemas e guia-o para informações que ajudam a resolver problemas e resolver problemas.
    * [Migrar](/azure/developer/java/migration/migrate-spring-boot-to-azure-spring-cloud): Como migrar uma aplicação de Mola Cloud ou uma aplicação de Boot spring existente para funcionar na Azure Spring Cloud.

 Para começar, consulte:

> [!div class="nextstepaction"]
> [Início rápido da Nuvem de primavera](spring-cloud-quickstart.md)

As amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).