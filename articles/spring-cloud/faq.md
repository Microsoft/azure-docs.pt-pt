---
title: Perguntas frequentes sobre Azure Spring Cloud | Microsoft Docs
description: Este artigo responde frequentemente a perguntas sobre Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: cee71557e60be9fdd94099453d9b8617c4b3b5ba
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878570"
---
# <a name="azure-spring-cloud-faq"></a>FAQ de nuvem de primavera de Azure

Este artigo responde frequentemente a perguntas sobre Azure Spring Cloud.

## <a name="general"></a>Geral

### <a name="why-azure-spring-cloud"></a>Por que Azure Spring Cloud?

A Azure Spring Cloud fornece uma plataforma como um serviço (PaaS) para desenvolvedores de Spring Cloud. O Azure Spring Cloud gere a sua infraestrutura de aplicações para que possa focar-se no código de aplicação e na lógica de negócio. As funcionalidades centrais incorporadas na Nuvem de primavera do Azure incluem Eureka, Config Server, Service Registry Server, Pivotal Build Service, Blue-Green deployment, entre outros. Este serviço também permite que os desenvolvedores liguem as suas aplicações a outros serviços Azure, tais como Azure Cosmos DB, Azure Database for MySQL e Azure Cache para Redis.

O Azure Spring Cloud melhora a experiência de diagnóstico de aplicações para desenvolvedores e operadores, integrando o Azure Monitor, Application Insights e Log Analytics.

### <a name="how-secure-is-azure-spring-cloud"></a>Quão segura é a Nuvem de primavera de Azure?

A segurança e a privacidade estão entre as principais prioridades para os clientes Azure e Azure Spring Cloud. O Azure ajuda a garantir que apenas os clientes têm acesso a dados de aplicações, registos ou configurações encriptando de forma segura todos estes dados. 

* As instâncias de serviço em Azure Spring Cloud estão isoladas umas das outras.
* A Azure Spring Cloud fornece a gestão completa de TLS/SSL e certificados.
* Patches de segurança críticos para os tempos de execução OpenJDK e Spring Cloud são aplicados à Nuvem de primavera de Azure o mais rapidamente possível.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Em que regiões está disponível a Nuvem de primavera de Azure?

Leste dos EUA, Leste dos EUA 2, Central EUA, Central Norte-Americano, Norte-Americano, Oeste dos EUA, Oeste dos EUA 2, Europa Ocidental, Europa do Norte, Reino Unido Sul, Sudeste Asiático, Austrália Oriental, Canadá Central, Uae North, Índia Central, Coreia Central, Ásia Oriental e China Oriental 2 (Mooncake). [Saiba mais](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud)

### <a name="is-any-customer-data-stored-outside-of-the-specified-region"></a>Os dados dos clientes são armazenados fora da região especificada?

Azure Spring Cloud é um serviço regional. Todos os dados dos clientes em Azure Spring Cloud são armazenados em várias regiões dentro do mesmo geo da região especificada para redundância. Para saber mais sobre geo e região, consulte [a residência de Dados em Azure.](https://azure.microsoft.com/global-infrastructure/data-residency/)

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Quais são as limitações conhecidas de Azure Spring Cloud?

Azure Spring Cloud tem as seguintes limitações conhecidas:
    
* `spring.application.name` será ultrapassado pelo nome de aplicação que é usado para criar cada aplicação.
* `server.port` incumprimentos para a porta 1025. Se for aplicado qualquer outro valor, será ultrapassado. Respeite também esta definição e não especifique a porta do servidor no seu código.
* Os modelos do portal Azure e do Gestor de Recursos Azure não suportam o upload de pacotes de aplicações. Só é possível carregar pacotes de aplicações através do Azure CLI.

### <a name="what-pricing-tiers-are-available"></a>Que níveis de preços estão disponíveis? 
Qual devo usar e quais são os limites dentro de cada nível?
* A Azure Spring Cloud oferece dois níveis de preços: Básico e Standard. O nível básico é direcionado para Dev/Test e experimentando Azure Spring Cloud. O nível Standard está otimizado para executar o tráfego de produção para fins gerais. Consulte [os detalhes dos preços da Cloud Azure Spring](https://azure.microsoft.com/pricing/details/spring-cloud/) para obter limites e comparação de nível de recurso.

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Como posso fornecer feedback e reportar problemas?

Se encontrar algum problema com a Nuvem de primavera de Azure, crie um [Pedido de Apoio Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). Para submeter um pedido de recurso ou fornecer feedback, aceda ao [Feedback Azure](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Desenvolvimento

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Sou um desenvolvedor de Spring Cloud, mas novo em Azure. Qual é a maneira mais rápida de aprender a desenvolver uma aplicação Azure Spring Cloud?

Para obter a forma mais rápida de começar com a Azure Spring Cloud, siga as instruções em [Quickstart: Lance uma aplicação Azure Spring Cloud utilizando o portal Azure](spring-cloud-quickstart.md).

::: zone pivot="programming-language-java"
### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Que tempo de execução java suporta a Azure Spring Cloud?

Azure Spring Cloud suporta Java 8 e 11. Ver [versões java runtime e OS](#java-runtime-and-os-versions)

### <a name="is-spring-boot-24x-supported"></a>A Bota de primavera 2.4.x está suportada?
Identificamos um problema com a Bota de primavera 2.4 e estamos atualmente a trabalhar com a comunidade da primavera para o resolver. Entretanto, por favor, inclua estas duas dependências para ativar a autenticação TLS entre as suas apps e Eureka.

```xml
<dependency> 
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-client</artifactId>
    <version>1.19.4</version>
</dependency>
<dependency>
    <groupId>com.sun.jersey.contribs</groupId>
    <artifactId>jersey-apache-client4</artifactId>
    <version>1.19.4</version>
</dependency>
```

::: zone-end

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Onde posso ver os registos e métricas da minha aplicação da Cloud Spring Cloud?

Encontre métricas no separador Visão Geral da App e no [separador Azure Monitor.](../azure-monitor/essentials/data-platform-metrics.md#metrics-explorer)

A Azure Spring Cloud suporta a exportação de registos e métricas de aplicações da Spring Cloud para Azure Storage, EventHub e [Log Analytics](../azure-monitor/logs/data-platform-logs.md). O nome da tabela no Log Analytics é *AppPlatformLogsforSpring*. Para aprender a capacitá-lo, consulte [os serviços de Diagnóstico.](diagnostic-services.md)

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>O Azure Spring Cloud suporta o rastreio distribuído?

Sim. Para obter mais informações, consulte [Tutorial: Use Rastreio Distribuído com Nuvem de primavera Azure](spring-cloud-howto-distributed-tracing.md).

::: zone pivot="programming-language-java"
### <a name="what-resource-types-does-service-binding-support"></a>Que tipos de recursos suporta o suporte de ligação de serviço?

Atualmente, três serviços são suportados:
* Azure Cosmos DB
* Base de Dados do Azure para MySQL
* Azure Cache para Redis.
::: zone-end

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Posso ver, adicionar ou mover volumes persistentes de dentro das minhas aplicações?

Sim.

### <a name="how-many-outbound-public-ip-addresses-does-an-azure-spring-cloud-instance-have"></a>Quantos endereços IP públicos de saída tem uma instância Azure Spring Cloud?

O número de endereços IP públicos de saída pode variar de acordo com os níveis e outros fatores. 

| Tipo de instância de nuvem de primavera Azure | Número predefinido de endereços IP públicos de saída |
| -------------------------------- | ---------------------------------------------- |
| Instâncias de nível básico             | 1                                              |
| Instâncias standard Tier          | 2                                              |
| Instâncias de injeção VNet         | 1                                              |


### <a name="can-i-increase-the-number-of-outbound-public-ip-addresses"></a>Posso aumentar o número de endereços IP públicos de saída?

Sim, você pode abrir um [bilhete de apoio](https://azure.microsoft.com/support/faq/)  para solicitar mais endereços IP públicos de saída.

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>Quando apagar/mover uma instância de serviço Azure Spring Cloud, os seus recursos de extensão também serão eliminados/movidos?

Depende da lógica dos fornecedores de recursos que possuem os recursos de extensão. Os recursos de extensão de uma `Microsoft.AppPlatform` instância não pertencem ao mesmo espaço de nome, pelo que o comportamento varia conso pelo fornecedor de recursos. Por exemplo, a operação de eliminação/movimento não se cascata para os recursos **de definição de diagnóstico.** Se uma nova instância Azure Spring Cloud for aprovisionada com o mesmo ID de recurso que o eliminado, ou se a instância anterior da Azure Spring Cloud for recaída, os recursos de **definições** de diagnóstico anteriores continuam a alargá-lo.

Pode eliminar as definições de diagnóstico da Cloud da primavera utilizando o Azure CLI:

```azurecli
 az monitor diagnostic-settings delete --name $diagnosticSettingName --resource $azureSpringCloudResourceId
```

::: zone pivot="programming-language-java"
## <a name="java-runtime-and-os-versions"></a>Versões java runtime e OS

### <a name="which-versions-of-java-runtime-are-supported-in-azure-spring-cloud"></a>Que versões de java são suportadas em Azure Spring Cloud?

A azure Spring Cloud suporta versões Java LTS com as construções mais recentes, atualmente junho de 2020, Java 8 e Java 11 são suportados. Ver [Instalar o JDK para Azure e Azure Stack](/azure/developer/java/fundamentals/java-jdk-install)

### <a name="who-built-these-java-runtimes"></a>Quem construiu estes tempos de java?

Sistemas Azul. As compilações do OpenJDK Azul Zulu for Azure - Enterprise Edition são uma distribuição gratuita, multiplataforma e prontas para produção do OpenJDK para o Azure e o Azure Stack apoiada pela Microsoft e pela Azul Systems. Contêm todos os componentes para compilar e executar aplicações Java SE.

### <a name="how-often-will-java-runtimes-get-updated"></a>Quantas vezes os tempos de java serão atualizados?

Os lançamentos LTS e MTS JDK têm atualizações trimestrais de segurança, correções de erros e atualizações e patches fora da banda críticos, conforme necessário. Este suporte inclui backports para Java 7 e 8 de atualizações de segurança e correções de bugs reportadas em versões mais recentes de Java, como Java 11.

### <a name="how-long-will-java-8-and-java-11-lts-versions-be-supported"></a>Quanto tempo serão suportadas as versões Java 8 e Java 11 LTS?

Consulte [o suporte a longo prazo de Java para Azure e Azure Stack](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* Java 8 LTS será apoiado até dezembro de 2030.
* Java 11 LTS será apoiado até setembro de 2027.

### <a name="how-can-i-download-a-supported-java-runtime-for-local-development"></a>Como posso descarregar um tempo de java suportado para o desenvolvimento local?

Consulte [a Instalação do JDK para Azure e Azure Stack](/azure/developer/java/fundamentals/java-jdk-install).

### <a name="what-is-the-retire-policy-for-older-java-runtimes"></a>Qual é a política de aposentação para os tempos mais velhos de Java?

O aviso público será enviado 12 meses antes de qualquer versão antiga do tempo de execução ser retirada. Terá 12 meses para migrar para uma versão posterior.

* Os administradores de subscrição receberão notificação por e-mail quando retirarmos uma versão Java.
* A informação da aposentadoria será publicada na documentação.

### <a name="how-can-i-get-support-for-issues-at-the-java-runtime-level"></a>Como posso obter apoio para problemas a nível de tempo de java?

Você pode abrir um bilhete de apoio com suporte Azure.  Ver Como criar um pedido de [apoio Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

### <a name="what-is-the-operation-system-to-run-my-apps"></a>Qual é o sistema de operação para executar as minhas aplicações?

A versão mais recente do Ubuntu LTS é utilizada, atualmente [Ubuntu 20.04 LTS (Focal Fossa)](https://releases.ubuntu.com/focal/) é o SISTEMA padrão.

### <a name="how-often-are-os-security-patches-applied"></a>Com que frequência são aplicados patches de segurança OS?

Patches de segurança aplicáveis à Azure Spring Cloud são lançados para a produção mensalmente.
Patches de segurança críticos (pontuação CVE >= 9) aplicáveis à Nuvem de primavera de Azure são lançados o mais rapidamente possível.
::: zone-end

## <a name="deployment"></a>Implementação

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>A Azure Spring Cloud suporta a implantação azul-esverdeado?
Sim. Para obter mais informações, consulte [Configurar um ambiente de preparação.](spring-cloud-howto-staging-environment.md)

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Posso aceder a Kubernetes para manipular os meus recipientes de aplicação?

N.º  Azure Spring Cloud abstrata o desenvolvedor da arquitetura subjacente, permitindo-lhe concentrar-se no código de aplicação e na lógica de negócio.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>A Azure Spring Cloud suporta contentores de construção de fonte?

Sim. Para obter mais informações, consulte [lançar a sua aplicação Cloud Spring a partir do código fonte](spring-cloud-quickstart.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>O Azure Spring Cloud suporta autoscaling em instâncias de aplicações?

Sim.  Para obter mais informações, consulte [Configuração auto-escala](spring-cloud-tutorial-setup-autoscale.md).

::: zone pivot="programming-language-java"
### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Quais são as melhores práticas para migrar microserviços da Nuvem de primavera existentes para Azure Spring Cloud?

À medida que está a migrar microserviços de Nuvem de primavera para Azure Spring Cloud, é uma boa ideia observar as seguintes boas práticas:
* Todas as dependências de aplicações têm de ser resolvidas.
* Prepare as suas entradas de configuração, variáveis ambientais e parâmetros JVM para que possa compará-las com a implementação na Nuvem de primavera de Azure.
* Se quiser utilizar a Ligação de Serviço, aceda aos seus serviços Azure e certifique-se de que definiu as permissões de acesso adequadas.
* Recomendamos que remova ou desative quaisquer serviços incorporados que possam entrar em conflito com os serviços geridos pela Azure Spring Cloud, como o nosso serviço Service Discovery, Config Server, e assim por diante.
* Recomendamos que utilize bibliotecas oficiais e estáveis da primavera Pivotal. Versões não oficiais, beta ou forquilhadas de bibliotecas da primavera Pivotal não têm suporte a acordo de nível de serviço (SLA).

Após a migração, monitorize as suas métricas de CPU/RAM e o tráfego de rede para garantir que as instâncias de aplicação são dimensionadas adequadamente.
::: zone-end

::: zone pivot="programming-language-csharp"
## <a name="net-core-versions"></a>Versões .NET Core

### <a name="which-net-core-versions-are-supported"></a>Quais as versões .NET Core suportadas?

.NET Core 3.1 e versões posteriores.

### <a name="how-long-will-net-core-31-be-supported"></a>Quanto tempo será suportado .NET Core 3.1?

Até 3 de dezembro de 2022. Consulte [a política de suporte do núcleo .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).
::: zone-end


## <a name="troubleshooting"></a>Resolução de problemas

### <a name="what-are-the-impacts-of-service-registry-rarely-unavailable"></a>Quais são os impactos do registo de serviço raramente indisponíveis?

Em algum cenário raramente aconteceu, você pode ver alguns erros como 
```
RetryableEurekaHttpClient: Request execution failure with status code 401; retrying on another server if available
```
a partir dos seus registos de aplicações. Esta questão introduzida pelo quadro da primavera com uma taxa muito baixa devido a problemas de rede instáveis ou outros problemas de rede. 

Não deve haver impactos na experiência do utilizador, o cliente eureka tem simultaneamente batimentos cardíacos e política de retenção para cuidar disto. Pode considerá-lo um erro transitório e ignorá-lo com segurança.

Vamos melhorar esta parte e evitar este erro das aplicações dos utilizadores num futuro curto.


## <a name="next-steps"></a>Passos seguintes

Se tiver mais perguntas, consulte o [guia de resolução de problemas da Nuvem de primavera de Azure](spring-cloud-troubleshoot.md).
