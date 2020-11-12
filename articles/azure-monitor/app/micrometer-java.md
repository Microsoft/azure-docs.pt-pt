---
title: Como utilizar o Micrometro com Azure Application Insights Java SDK
description: Um guia passo a passo sobre a utilização do Micrometro com as aplicações De Mola Insights de Aplicação e aplicações de arranque não-Mola.
ms.topic: conceptual
author: lgayhardt
ms.custom: devx-track-java
ms.author: lagayhar
ms.date: 11/01/2018
ms.openlocfilehash: 534452d57884045f90c1d8d3ff44aadc3578cd4f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542539"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Como utilizar o Micrometro com Azure Application Insights Java SDK

> [!IMPORTANT]
> A abordagem recomendada para monitorizar as aplicações java é utilizar a auto-instrumentação sem alterar o código. A telemetria de micrometros é recolhida automaticamente com o agente Application Insights Java 3.0 - siga as orientações para o [agente Desconsetecção de Aplicações Java 3.0](./java-in-process-agent.md).

> [!NOTE]
> Application Insights Java SDK não suporta o Webflux de primavera - use [o agente Application Insights Java 3.0.](./java-in-process-agent.md) 
>
> Tanto o Webflux como o Micrometro são suportados no [agente Application Insights Java 3.0](./java-on-premises.md) que não necessita de instrumentação. 

A monitorização da aplicação de micrometros mede as métricas do código de aplicação baseado em JVM e permite exportar os dados para os seus sistemas de monitorização favoritos. Este artigo irá ensiná-lo a usar o Micrometro com Insights de Aplicação para aplicações de Boot de primavera e não-Spring Boot.

## <a name="using-spring-boot-15x"></a>Usando a Bota de Mola 1.5x
Adicione as seguintes dependências ao seu ficheiro pom.xml ou build.gradle: 
* [Application Insights início de arranque](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter) 2.5.0 ou mais tarde
* Micrometro Registo Azure 1.1.0 ou superior
* [Micrometro Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 ou superior (isto retroporta o código autoconfig na estrutura da Mola).
* [Recurso ApplicationInsights](./create-new-resource.md)

Passos

1. Atualize o ficheiro pom.xml da sua aplicação Boot de primavera e adicione as seguintes dependências:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>2.5.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. Atualize o ficheiro.propriedades ou ficheiro yml com a chave de Instrumentação de Insights de Aplicação utilizando a seguinte propriedade:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Construa a sua aplicação e corra
2. O acima deve levá-lo a funcionar com métricas pré-agregadas recolhidas para O Azure Monitor. Para obter detalhes sobre como afinar o arranque do Boot De primavera do Application Insights Consulte o [readme no GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Usando a primavera 2.x

Adicione as seguintes dependências ao seu ficheiro pom.xml ou build.gradle:

* Insights de aplicação Início de arranque 2.1.2 ou superior
* Azure-spring-boot-métricas-arranques 2.0.7 ou mais tarde
* [Insights de Aplicação](./create-new-resource.md)

Passos:

1. Atualize o ficheiro pom.xml da sua aplicação Boot Spring e adicione a seguinte dependência:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Atualize o ficheiro.propriedades ou ficheiro yml com a chave de Instrumentação de Insights de Aplicação utilizando a seguinte propriedade:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Construa a sua aplicação e corra
4. O acima deve fazê-lo funcionar com métricas pré-agregadas recolhidas para o Azure Monitor. Para obter detalhes sobre como afinar o arranque do Boot De primavera do Application Insights Consulte o [readme no GitHub](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Métricas padrão:

*    Métricas configuradas automaticamente para Tomcat, JVM, Métricas de Logback, métricas log4J, métricas de uptime, Métricas do Processador, FileDescriptorMetrics.
*    Por exemplo, se a Netflix Hystrix estiver presente no caminho da classe, também obtemos essas métricas. 
*    As seguintes métricas podem estar disponíveis adicionando respetivos feijões. 
        - CacheMetrics (CafeínaCache, EhCache2, GuavaCache, HazelcastCache, JCache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - Métricas OkHttp3 
        - Métricas kafka 

 

Como desligar a recolha automática de métricas: 
 
- Métricas JVM: 
    - management.metrics.binders.jvm.enabled=false 
- Métricas de recuo: 
    - management.metrics.binders.logback.enabled=false
- Métricas de tempo de compensação: 
    - management.metrics.binders.uptime.enabled=false 
- Métricas do processador:
    -  management.metrics.binders.processor.enabled=false 
- ArquivoDescriptorMétrico:
    - management.metrics.binders.files.enabled=false 
- Hystrix Metrics se biblioteca em classe: 
    - management.metrics.binders.hystrix.enabled=false 
- Métricas AspectJ se biblioteca em classe: 
    - spring.aop.enabled=falso 

> [!NOTE]
> Especificar as propriedades acima no ficheiro application.properties ou application.yml da sua aplicação Spring Boot

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Use micrometro com aplicações web não-Spring Boot

Adicione as seguintes dependências ao seu ficheiro pom.xml ou build.gradle:

* Insights de Aplicação Web Auto 2.5.0 ou posterior
* Micrometro Registo Azure 1.1.0 ou superior
* [Insights de Aplicação](./create-new-resource.md)

Passos:

1. Adicione as seguintes dependências no seu ficheiro pom.xml ou build.gradle:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web-auto</artifactId>
            <version>2.5.0</version>
        </dependency>
     ```

2. Coloque `ApplicationInsights.xml` o ficheiro na pasta de recursos:

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
    
       <!-- The key from the portal: -->
       <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
    
       <!-- HTTP request component (not required for bare API) -->
       <TelemetryModules>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
       </TelemetryModules>
    
       <!-- Events correlation (not required for bare API) -->
       <!-- These initializers add context data to each event -->
       <TelemetryInitializers>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
       </TelemetryInitializers>
    
    </ApplicationInsights>
    ```

3. Classe Servlet da amostra (emite uma métrica do temporizador):

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {
    
          private static final long serialVersionUID = -4751096228274971485L;
    
          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
    
            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");
    
        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }
    
        }
    
    ```

4. Classe de configuração de amostras:

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {
     
           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {
     
         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}
     
             @Override
             public boolean enabled() {
                 return false;
             }
         };
     
      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);
     
             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);
     
           }
     
           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {
     
           }
         }
    ```

Para saber mais sobre as métricas, consulte a documentação do [Micrometro.](https://micrometer.io/docs/)

Outros códigos de amostra sobre como criar diferentes tipos de métricas podem ser encontrados[no repo oficial do Micrometro GitHub](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Como ligar a recolha de métricas adicionais

### <a name="springbootspring"></a>SpringBoot/primavera

Criar um feijão da respetiva categoria métrica. Por exemplo, digamos que precisamos de métricas de cache de Guava:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Existem várias métricas que não são habilitadas por defeito, mas podem ser ligadas na moda acima. Para obter uma lista completa, consulte [o micrometro oficial GitHub repo](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Aplicativos não-primavera
Adicione o seguinte código de ligação ao ficheiro de configuração:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o Micrometro, consulte a documentação oficial do [Micrometro.](https://micrometer.io/docs)
* Para saber mais sobre a primavera em Azure, consulte a documentação oficial [da primavera sobre Azure.](/java/azure/spring-framework/?view=azure-java-stable)