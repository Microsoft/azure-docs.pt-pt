---
title: Perguntas frequentes sobre o Azure Spring Cloud | Microsoft Docs
description: Examine as perguntas frequentes sobre o Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 12ef5586ac1e7ecf27824f95add3355451cf555a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163767"
---
# <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

Este artigo aborda perguntas frequentes sobre o Azure Spring Cloud. 

## <a name="general"></a>Geral

### <a name="why-azure-spring-cloud"></a>Por que o Azure Spring Cloud?

O Azure Spring Cloud fornece uma PaaS (plataforma como serviço) para desenvolvedores Spring. O Azure Spring Cloud gerencia sua infraestrutura de aplicativo para que você possa se concentrar no código do aplicativo e na lógica de negócios. Recursos principais incorporados ao Azure Spring Cloud, incluem Eureka, servidor de configuração, servidor de registro de serviço, serviço de compilação dinâmica, implantações de verde-azulado e muito mais. Esse serviço também permite que os desenvolvedores associem seus aplicativos aos serviços do Azure, como CosmosDB, MySQL e cache do Azure para Redis.

O Azure Spring Cloud aprimora a experiência do Application Diagnostics para os desenvolvedores e operadores integrando Azure Monitor, Application Insights e Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Quais planos de serviço o Azure Spring Cloud oferece?

O Azure Spring Cloud oferece um plano de serviço durante o período de versão prévia.  Uma implantação do Spring Cloud contém 16 núcleos de vCPU e 32 GB de memória.  O limite superior para cada instância de microserviço em uma implantação é de 4 núcleos vCPU com 8 GB de memória.

Recurso | Montante
------- | -------
Instâncias de aplicativo por aplicativo Spring | 20
Total de instâncias de aplicativo por instância de serviço de nuvem do Azure Spring | 50 *
Instâncias de serviço de nuvem do Azure Spring por região por assinatura | 2
Volumes persistentes | 10 x 50 GBytes

*_abra um [tíquete de suporte](https://azure.microsoft.com/support/faq/) para aumentar o limite._

Para obter mais detalhes, consulte [perguntas frequentes sobre o suporte do Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Quão seguro é o Azure Spring Cloud?

Segurança e privacidade são uma das principais prioridades para os clientes do Azure e do Azure Spring Cloud. O Azure garante que apenas o cliente tenha acesso a dados, logs ou configurações de aplicativos, criptografando com segurança todos esses dados. Todas as instâncias de serviço no Azure Spring Cloud são isoladas umas das outras.

O Azure Spring Cloud fornece gerenciamento de certificados e SSL completo.

Os patches de segurança críticos para os tempos de execução OpenJDK e Spring Cloud são aplicados ao Azure Spring Cloud assim que possível.

### <a name="which-regions-azure-spring-cloud-are-available"></a>Quais regiões o Azure Spring Cloud está disponível?

Leste dos EUA, oeste dos EUA 2, Europa Ocidental e sudeste asiático.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Quais são as limitações conhecidas do Azure Spring Cloud

Aqui estão as limitações conhecidas do Azure Spring Cloud enquanto o serviço está em versão prévia.

* `spring.application.name` será substituído pelo nome do aplicativo usado para criar cada aplicativo.
* `server.port` não é permitido no arquivo de configuração do repositório git. Adicioná-lo ao arquivo de configuração provavelmente fará com que seu aplicativo não possa ser acessado de outros aplicativos ou Internet.
* Os modelos portal do Azure e Resource Manager não dão suporte ao carregamento de pacotes de aplicativos. Isso só pode ser feito por meio da implantação do aplicativo por CLI do Azure.
* Para limitações de cotas, consulte [quais planos de serviço o Azure Spring Cloud oferece](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Como posso fornecer comentários e relatar problemas?

Se você encontrar problemas com o Azure Spring Cloud, crie uma [solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Para solicitações de recursos, acesse os [comentários do Azure](https://feedback.azure.com/forums/34192--general-feedback) para solicitar recursos ou para fornecer comentários.

## <a name="development"></a>Desenvolvimento

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>Sou um desenvolvedor Spring, mas novo no Azure, qual é a maneira mais rápida de aprender a desenvolver o aplicativo as Spring Cloud?

A maneira mais rápida de começar a usar o Azure Spring Cloud é seguir [este guia de início rápido](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Em qual tempo de execução do Java o Azure Spring Cloud dá suporte?

O Azure Spring Cloud dá suporte a Java 8 e 11.

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>Onde posso ver meus logs e métricas de aplicativos Spring?

Encontre métricas na guia Visão geral do aplicativo e na guia [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) .

O Azure Spring Cloud dá suporte à exportação de métricas e logs de aplicativo Spring para o armazenamento do Azure, o EventHub e o [log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). O nome da tabela em Log Analytics é `AppPlatformLogsforSpring`. Para habilitá-lo, leia este artigo sobre nossos [serviços de diagnóstico](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>O Azure Spring Cloud dá suporte ao rastreamento distribuído?

Sim, visite [rastreamento distribuído](spring-cloud-tutorial-distributed-tracing.md) para obter mais detalhes.

### <a name="what-resource-types-does-service-binding-support"></a>Para quais tipos de recursos a associação de serviço dá suporte?

Atualmente, há suporte para três serviços: Azure Cosmos DB, banco de dados do Azure para MySQL e cache do Azure para Redis.

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>Posso exibir/adicionar/mover volumes persistentes de dentro de meus aplicativos?
Sim.

## <a name="deployment"></a>Implementação

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>O Azure Spring Cloud dá suporte à implantação azul-verde?
Sim, visite o [Guia do ambiente de preparo](spring-cloud-howto-staging-environment.md) para obter mais detalhes.

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Posso acessar o kubernetes para manipular os contêineres do meu aplicativo?

Não.  O Azure Spring Cloud abstrai o desenvolvedor da arquitetura subjacente, permitindo que você se concentre no código do aplicativo e na lógica de negócios.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>O Azure Spring Cloud dá suporte à criação de contêineres da origem?

Sim, visite [implantar da fonte](spring-cloud-launch-from-source.md) para obter mais detalhes.

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>O Azure Spring Cloud dá suporte ao dimensionamento automático em instâncias de aplicativo?

Não.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>Quais são as práticas recomendadas para migrar microserviços de Spring existentes para o Azure Spring Cloud?

Antes de migrar os microserviços Spring existentes para o Azure Spring Cloud,
* Todas as dependências de aplicativo precisam ser resolvidas.
* Prepare suas entradas de configuração, variáveis de ambiente e parâmetros JVM para que você possa compará-las com a implantação no Azure Spring Cloud.
* Se você quiser usar a associação de serviço, passe pelos serviços do Azure e verifique se você definiu as permissões de acesso apropriadas.
* Recomendamos que você remova ou desabilite serviços inseridos que podem entrar em conflito com os serviços gerenciados pelo Azure Spring Cloud, como nosso serviço de descoberta de serviço, servidor de configuração, etc..
*-* Recomendamos que você use bibliotecas dinâmicas e estáveis do Spring pivot. Versões não oficiais, beta ou bifurcadas de bibliotecas de Spring dinâmicos não têm suporte de SLA.

Após a migração, monitore as métricas de CPU/RAM e o tráfego de rede para garantir que as instâncias do aplicativo sejam dimensionadas adequadamente.

## <a name="next-steps"></a>Passos seguintes

[Confira o guia de solução de problemas se você tiver mais dúvidas](spring-cloud-troubleshoot.md).