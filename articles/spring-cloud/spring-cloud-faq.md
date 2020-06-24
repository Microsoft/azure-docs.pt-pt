---
title: Perguntas frequentes sobre Azure Spring Cloud Microsoft Docs
description: Este artigo responde frequentemente a perguntas sobre Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: e3c38a67b13a6b5c12767d38ecf2297d2417ebdb
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/16/2020
ms.locfileid: "84808415"
---
# <a name="azure-spring-cloud-faq"></a>FAQ de nuvem de primavera de Azure

Este artigo responde frequentemente a perguntas sobre Azure Spring Cloud.

## <a name="general"></a>Geral

### <a name="why-azure-spring-cloud"></a>Por que Azure Spring Cloud?

A Azure Spring Cloud fornece uma plataforma como um serviço (PaaS) para desenvolvedores de Spring Cloud. O Azure Spring Cloud gere a sua infraestrutura de aplicações para que possa focar-se no código de aplicação e na lógica de negócio. As funcionalidades centrais incorporadas na Nuvem de primavera do Azure incluem Eureka, Config Server, Service Registry Server, Pivotal Build Service, implementações verde-azuladas e muito mais. Este serviço também permite que os desenvolvedores liguem as suas aplicações a outros serviços Azure, tais como Azure Cosmos DB, Azure Database for MySQL e Azure Cache para Redis.

O Azure Spring Cloud melhora a experiência de diagnóstico de aplicações para desenvolvedores e operadores, integrando o Azure Monitor, Application Insights e Log Analytics.

### <a name="how-secure-is-azure-spring-cloud"></a>Quão segura é a Nuvem de primavera de Azure?

A segurança e a privacidade estão entre as principais prioridades para os clientes Azure e Azure Spring Cloud. O Azure ajuda a garantir que apenas os clientes têm acesso a dados de aplicações, registos ou configurações encriptando de forma segura todos estes dados. Todas as instâncias de serviço em Azure Spring Cloud estão isoladas umas das outras.

A Azure Spring Cloud fornece a gestão completa de TLS/SSL e certificados.

Patches de segurança críticos para os tempos de execução OpenJDK e Spring Cloud são aplicados à Nuvem de primavera de Azure o mais rapidamente possível.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Em que regiões está disponível a Nuvem de primavera de Azure?

Leste dos EUA, Oeste dos EUA 2, Europa Ocidental e Sudeste Asiático.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Quais são as limitações conhecidas de Azure Spring Cloud?

Durante o lançamento de pré-visualização, a Azure Spring Cloud tem as seguintes limitações conhecidas:

* `spring.application.name`será ultrapassado pelo nome de aplicação que é usado para criar cada aplicação.
* `server.port`não é permitido no ficheiro de configuração do repo Git. A adicioná-lo ao ficheiro de configuração provavelmente tornará a sua aplicação inacessível a partir de outras aplicações ou da internet.
* Os modelos do portal Azure e do Gestor de Recursos Azure não suportam o upload de pacotes de aplicações. Só é possível carregar pacotes de aplicações através do Azure CLI.

### <a name="what-pricing-tiers-are-available"></a>Que níveis de preços estão disponíveis? 
Qual devo usar e quais são os limites dentro de cada nível?
* A Azure Spring Cloud oferece dois níveis de preços: Básico e Standard. O nível básico é direcionado para Dev/Test e experimentando Azure Spring Cloud. O nível Standard está otimizado para executar o tráfego de produção para fins gerais. Consulte [os detalhes dos preços da Cloud Azure Spring](https://azure.microsoft.com/pricing/details/spring-cloud/) para obter limites e comparação de nível de recurso.

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Como posso fornecer feedback e reportar problemas?

Se encontrar algum problema com a Nuvem de primavera de Azure, crie um [Pedido de Apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Para submeter um pedido de recurso ou fornecer feedback, aceda ao [Feedback Azure](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Desenvolvimento

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Sou um desenvolvedor de Spring Cloud, mas novo em Azure. Qual é a maneira mais rápida de aprender a desenvolver uma aplicação Azure Spring Cloud?

Para obter a forma mais rápida de começar com a Azure Spring Cloud, siga as instruções em [Quickstart: Lance uma aplicação Azure Spring Cloud utilizando o portal Azure](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Que tempo de execução java suporta a Azure Spring Cloud?

Azure Spring Cloud suporta Java 8 e 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Onde posso ver os registos e métricas da minha aplicação da Cloud Spring Cloud?

Encontre métricas no separador Visão Geral da App e no [separador Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics)

A Azure Spring Cloud suporta a exportação de registos e métricas de aplicações da Spring Cloud para Azure Storage, EventHub e [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). O nome da tabela no Log Analytics é *AppPlatformLogsforSpring*. Para aprender a capacitá-lo, consulte [os serviços de Diagnóstico.](diagnostic-services.md)

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>O Azure Spring Cloud suporta o rastreio distribuído?

Sim. Para obter mais informações, consulte [Tutorial: Use Rastreio Distribuído com Nuvem de primavera Azure](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>Que tipos de recursos suporta o suporte de ligação de serviço?

Atualmente são suportados três serviços: Azure Cosmos DB, Azure Database for MySQL e Azure Cache para Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Posso ver, adicionar ou mover volumes persistentes de dentro das minhas aplicações?

Sim.

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>Quando apagar/mover uma instância de serviço Azure Spring Cloud, os seus recursos de extensão também serão eliminados/movidos?

Depende das lógicas dos fornecedores de recursos a que pertencem os recursos de extensão. Os recursos de extensão de uma `Microsoft.AppPlatform` instância não pertencem ao mesmo espaço de nome, pelo que os comportamentos variam de acordo com diferentes fornecedores de recursos. Por exemplo, a operação de eliminação/movimento não se cascata para os recursos **de definição de diagnóstico.** Se uma nova instância Azure Spring Cloud for aprovisionada com o mesmo ID de recurso que o eliminado, ou se a instância anterior da Azure Spring Cloud for recaída, os recursos de **definições** de diagnóstico anteriores continuam a alargá-lo.

## <a name="deployment"></a>Implementação

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>A Azure Spring Cloud suporta a implantação azul-esverdeado?
Sim. Para obter mais informações, consulte [Configurar um ambiente de preparação.](spring-cloud-howto-staging-environment.md)

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Posso aceder a Kubernetes para manipular os meus recipientes de aplicação?

Não.  Azure Spring Cloud abstrata o desenvolvedor da arquitetura subjacente, permitindo-lhe concentrar-se no código de aplicação e na lógica de negócio.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>A Azure Spring Cloud suporta contentores de construção de fonte?

Sim. Para obter mais informações, consulte [lançar a sua aplicação Cloud Spring a partir do código fonte](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>O Azure Spring Cloud suporta autoscaling em instâncias de aplicações?

Não.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Quais são as melhores práticas para migrar microserviços da Nuvem de primavera existentes para Azure Spring Cloud?

À medida que está a migrar microserviços de Nuvem de primavera para Azure Spring Cloud, é uma boa ideia observar as seguintes boas práticas:
* Todas as dependências de aplicações têm de ser resolvidas.
* Prepare as suas entradas de configuração, variáveis ambientais e parâmetros JVM para que possa compará-las com a implementação na Nuvem de primavera de Azure.
* Se quiser utilizar a Ligação de Serviço, aceda aos seus serviços Azure e certifique-se de que definiu as permissões de acesso adequadas.
* Recomendamos que remova ou desative quaisquer serviços incorporados que possam entrar em conflito com os serviços geridos pela Azure Spring Cloud, como o nosso serviço Service Discovery, Config Server, e assim por diante.
* Recomendamos que utilize bibliotecas oficiais e estáveis da primavera Pivotal. Versões não oficiais, beta ou forquilhadas de bibliotecas da primavera Pivotal não têm suporte a acordo de nível de serviço (SLA).

Após a migração, monitorize as suas métricas de CPU/RAM e o tráfego de rede para garantir que as instâncias de aplicação são dimensionadas adequadamente.

## <a name="next-steps"></a>Passos seguintes

Se tiver mais perguntas, consulte o [guia de resolução de problemas da Nuvem de primavera de Azure](spring-cloud-troubleshoot.md).
