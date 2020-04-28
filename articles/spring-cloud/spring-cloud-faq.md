---
title: Perguntas frequentes sobre a Nuvem de primavera de Azure [ Microsoft Docs
description: Este artigo responde frequentemente a perguntas sobre a Nuvem de primavera de Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 95260d9a15fdc32c9fddccbcf63ae9fa564fd36a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176775"
---
# <a name="azure-spring-cloud-faq"></a>FaQ da nuvem de primavera azure

Este artigo responde frequentemente a perguntas sobre a Nuvem de primavera de Azure.

## <a name="general"></a>Geral

### <a name="why-azure-spring-cloud"></a>Por que azure nuvem de primavera?

A Azure Spring Cloud fornece uma plataforma como serviço (PaaS) para desenvolvedores de Spring Cloud. A Azure Spring Cloud gere a sua infraestrutura de aplicação para que possa focar-se no código de aplicação e na lógica empresarial. As características centrais incorporadas na Nuvem de primavera Azure incluem Eureka, Config Server, Servidor de Registo de Serviços, Serviço de Construção Pivotal, Implementações azul-verde, e muito mais. Este serviço também permite que os desenvolvedores liguem as suas aplicações com outros serviços Azure, tais como Azure Cosmos DB, Azure Database for MySQL e Azure Cache for Redis.

A Azure Spring Cloud melhora a experiência de diagnóstico de aplicações para programadores e operadores, integrando o Monitor Azure, os Insights de Aplicação e o Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Que planos de serviço oferece a Azure Spring Cloud?

A Azure Spring Cloud oferece um plano de serviço durante o período de pré-visualização.  Uma implantação da Nuvem de primavera contém 16 núcleos vCPU e 32 gigabytes (GB) de memória.  O limite superior para cada instância de microserviço dentro de uma implantação é de 4 núcleos vCPU com 8 GB de memória.

Recurso | Montante
------- | -------
Instâncias de aplicativos por aplicação primavera | 20
Total de instâncias de aplicativos por exemplo de serviço Azure Spring Cloud | 500
Casos de serviço azure Spring Cloud por região por subscrição | 10
Volumes persistentes | 10 x 50 GBytes

\*_Para elevar o limite, abra um bilhete de [apoio.](https://azure.microsoft.com/support/faq/)_

Para mais informações, consulte [O Suporte Azure FAQ](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Quão segura é a Nuvem de primavera de Azure?

A segurança e a privacidade estão entre as principais prioridades para os clientes azure e Azure Spring Cloud. O Azure ajuda a garantir que apenas os clientes têm acesso a dados de aplicação, registos ou configurações, encriptando de forma segura todos estes dados. Todos os casos de serviço em Azure Spring Cloud estão isolados uns dos outros.

A Azure Spring Cloud fornece uma gestão completa de TLS/SSL e certificados.

Os patches de segurança críticos para os tempos de execução da Nuvem de primavera OpenJDK e Spring são aplicados à Nuvem de primavera Azure o mais rapidamente possível.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Em que regiões está disponível a Nuvem de primavera Azure?

Leste dos EUA, Oeste dos EUA 2, Europa Ocidental e Sudeste Asiático.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Quais são as limitações conhecidas da Nuvem de primavera de Azure?

Durante o lançamento da pré-visualização, a Nuvem de primavera Azure tem as seguintes limitações conhecidas:

* `spring.application.name`será ultrapassado pelo nome da aplicação que é usado para criar cada aplicação.
* `server.port`não é permitido no ficheiro de configuração do repo Git. Adicioná-lo ao ficheiro de configuração provavelmente tornará a sua aplicação incontactável a partir de outras aplicações ou da internet.
* Os modelos do portal Azure e do Gestor de Recursos Azure não suportam o upload de pacotes de aplicações. Só é possível fazer o upload dos pacotes de aplicações através da implementação da aplicação através do Azure CLI.
* Para saber sobre limitações de quotas, veja que planos de serviço oferece a [Azure Spring Cloud?](#what-service-plans-does-azure-spring-cloud-offer)

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Como posso fornecer feedback e reportar problemas?

Se encontrar algum problema com a Nuvem de primavera Azure, crie um Pedido de [Apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Para submeter um pedido de funcionalidade ou fornecer feedback, vá ao [Feedback Do Azure](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Desenvolvimento

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Sou um desenvolvedor de Nuvem de primavera, mas novo no Azure. Qual é a maneira mais rápida de aprender a desenvolver uma aplicação Azure Spring Cloud?

Para começar mais rapidamente com a Nuvem de primavera Azure, siga as instruções em [Quickstart: Lance uma aplicação Azure Spring Cloud utilizando o portal Azure](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Que tempo de funcionamento de Java suporta a Nuvem de primavera de Azure?

Azure Spring Cloud suporta Java 8 e 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Onde posso ver os meus registos e métricas de aplicação da Nuvem de primavera?

Encontre métricas no separador App Overview e no separador [Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics)

A Azure Spring Cloud suporta a exportação de registos e métricas de aplicações da Spring Cloud para o Armazenamento Azure, EventHub e [Log Analytics.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries) O nome da tabela no Log Analytics é *AppPlatformLogsforSpring*. Para aprender a capacitá-lo, consulte [os serviços](diagnostic-services.md)de Diagnóstico.

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>A Nuvem de primavera Azure suporta rastreios distribuídos?

Sim. Para mais informações, consulte [Tutorial: Use Rastreio Distribuído com Nuvem de Mola Azure](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>Que tipos de recursos suporta o serviço de ligação?

Atualmente são apoiados três serviços: Azure Cosmos DB, Azure Database for MySQL e Azure Cache for Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Posso ver, adicionar ou mover volumes persistentes de dentro das minhas aplicações?

Sim.

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>Quando eu apagar/mover uma instância de serviço Azure Spring Cloud, os seus recursos de extensão serão eliminados/movidos também?

Depende das lógicas dos fornecedores de recursos onde os recursos de extensão pertencem. Os recursos de `Microsoft.AppPlatform` extensão de uma instância não pertencem ao mesmo espaço de nome, pelo que os comportamentos variam de acordo com diferentes fornecedores de recursos. Por exemplo, a operação de eliminação/movimento não irá cascata para os recursos de definição de **diagnóstico.** Se uma nova instância da Nuvem de primavera Azure for aprovisionada com o mesmo ID de recurso que o eliminado, ou se a instância anterior da Nuvem de primavera do Azure for reposta, os recursos de **definição** de diagnóstico anteriores continuam a alargá-lo.

## <a name="deployment"></a>Implementação

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>A Nuvem de primavera Azure suporta a implantação azul-verde?
Sim. Para mais informações, consulte [Configurar um ambiente](spring-cloud-howto-staging-environment.md)de encenação .

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Posso aceder a Kubernetes para manipular os meus contentores de aplicação?

Não.  Azure Spring Cloud abstrae o desenvolvedor da arquitetura subjacente, permitindo-lhe concentrar-se no código de aplicação e na lógica empresarial.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>A Nuvem de primavera Azure suporta os contentores de construção de fonte?

Sim. Para mais informações, consulte [Lance a sua aplicação Spring Cloud a partir do código fonte](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>A Nuvem de primavera Azure suporta a autoscalcificação em instâncias de aplicações?

Não.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Quais são as melhores práticas para migrar os microserviços existentes da Nuvem de primavera para a Nuvem de primavera de Azure?

Como está a migrar os microserviços da Nuvem de primavera para a Nuvem de primavera de Azure, é uma boa ideia observar as seguintes boas práticas:
* Todas as dependências de aplicações têm de ser resolvidas.
* Prepare as suas entradas de configuração, variáveis ambientais e parâmetros JVM para que possa compará-las com a implementação em Azure Spring Cloud.
* Se quiser utilizar o Serviço de Ligação, aceda aos seus serviços Azure e certifique-se de que definiu as permissões de acesso adequadas.
* Recomendamos que remova ou desative quaisquer serviços incorporados que possam entrar em conflito com serviços geridos pela Azure Spring Cloud, como o nosso serviço de Service Discovery, Config Server, e assim por diante.
* Recomendamos que utilize bibliotecas oficiais e estáveis da primavera Pivotal. Versões não oficiais, beta ou bifurcadas das bibliotecas da Pivotal Spring não têm suporte a acordo de nível de serviço (SLA).

Após a migração, monitorize as métricas CPU/RAM e o tráfego de rede para garantir que as instâncias de aplicação sejam dimensionadas adequadamente.

## <a name="next-steps"></a>Passos seguintes

Se tiver mais perguntas, consulte o guia de resolução de problemas da Nuvem de [primavera azure](spring-cloud-troubleshoot.md).
