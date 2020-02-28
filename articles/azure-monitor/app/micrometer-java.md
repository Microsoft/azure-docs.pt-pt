---
title: Como utilizar o Micrometer com Insights de Aplicação Azure Java SDK
description: Um guia passo a passo na utilização do Micrometer com as suas aplicações De arranque de mola Insights de aplicação insights e de arranque não-spring boot.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 11/01/2018
ms.openlocfilehash: dd04087db32f0bbfa75dafa7e12c355e5ab7b515
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670071"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Como utilizar o Micrometer com Insights de Aplicação Azure Java SDK
A monitorização da aplicação de micrometro mede métricas para o código de aplicação baseado em JVM e permite-lhe exportar os dados para os seus sistemas de monitorização favoritos. Este artigo irá ensiná-lo a usar o Micrometer com Insights de Aplicação para aplicações de Boot Spring e não-Spring Boot.

## <a name="using-spring-boot-15x"></a>Usando a bota de mola 1.5x
Adicione as seguintes dependências ao seu ficheiro pom.xml ou build.gradle: 
* [Aplicação Insights spring-boot-starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter) 2.5.0 ou mais tarde
* Registo do Micrómetro Azure 1.1.0 ou superior
* [Micrometro Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 ou superior (isto reporta o código de autoconfig na estrutura da Mola).
* [Recursos ApplicationInsights](../../azure-monitor/app/create-new-resource.md )

Passos

1. Atualize o ficheiro pom.xml da sua aplicação Spring Boot e adicione as seguintes dependências:

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
2. Atualize o ficheiro application.properties ou yml com a chave de instrumentação de insights de aplicação utilizando a seguinte propriedade:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Construa a sua aplicação e corra
2. O acima deve levá-lo a funcionar com métricas pré-agregadas auto recolhidas para o Monitor Azure. Para obter mais detalhes sobre como afinar o arranque da Bota de Arranque de primavera Insights de Aplicação Insights consulte o [readme no GitHub](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Usando a Mola 2.x

Adicione as seguintes dependências ao seu ficheiro pom.xml ou build.gradle:

* Aplicação Insights Spring-boot-starter 2.1.2 ou superior
* Azure-spring-boot-metrics-starters 2.0.7 ou posterior
* [Recursos insights de aplicação](../../azure-monitor/app/create-new-resource.md )

Passos:

1. Atualize o ficheiro pom.xml da sua aplicação Spring Boot e adicione a seguinte dependência:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Atualize o ficheiro application.properties ou yml com a chave de instrumentação de insights de aplicação utilizando a seguinte propriedade:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Construa a sua aplicação e corra
4. O acima deve fazê-lo funcionar com métricas pré-agregadas auto recolhidas para o Monitor Azure. Para obter mais detalhes sobre como afinar o arranque da Bota de Arranque de primavera Insights de Aplicação Insights consulte o [readme no GitHub](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Métricas padrão:

*    Métricas configuradas automaticamente para Tomcat, JVM, Métricas de Logback, métricas Log4J, Métricas uptime, métricas de processador, FicheiroDescriptorMetrics.
*    Por exemplo, se o Netflix Hystrix estiver presente no caminho de classe, também obtemos essas métricas. 
*    As seguintes métricas podem estar disponíveis adicionando os respetivos feijões. 
        - CacheMetrics (CafeínaCache, EhCache2, GuavaCache, HazelcastCache, JCache)     
        - DataBaseTableMetrics 
        - HibernateMétrica 
        - Moltrimétrica 
        - Métricas OkHttp3 
        - Métricas kafka 

 

Como desligar a recolha automática de métricas: 
 
- Métricas JVM: 
    - management.metrics.binders.jvm.enabled=false 
- Métricas de logback: 
    - gestão.metrics.binders.logback.enabled=falso
- Métricas uptime: 
    - gestão.metrics.binders.uptime.enabled=falso 
- Métricas do processador:
    -  gestão.metrics.binders.processor.enabled=false 
- FicheiroDescriptorMetrics:
    - gestão.metrics.binders.files.enabled=false 
- Histrix Metrics se biblioteca em classe: 
    - management.metrics.binders.hystrix.enabled=false 
- AspectJ Métricas se biblioteca em classe: 
    - spring.aop.enabled=falso 

> [!NOTE]
> Especifique as propriedades acima na aplicação.properties ou application.yml file of your Spring Boot application

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Utilize o Micrometer com aplicações web não-Spring Boot

Adicione as seguintes dependências ao seu ficheiro pom.xml ou build.gradle:

* Informações de aplicação Web Auto 2.5.0 ou mais tarde
* Registo do Micrómetro Azure 1.1.0 ou superior
* [Recursos insights de aplicação](../../azure-monitor/app/create-new-resource.md )

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

2. Coloque `ApplicationInsights.xml` ficheiro na pasta de recursos:

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

3. Classe Servlet da amostra (emite uma métrica temporizador):

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

4. Classe de configuração da amostra:

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

Para saber mais sobre métricas, consulte a documentação do [Micrometro.](https://micrometer.io/docs/)

Outro código de amostra sobre como criar diferentes tipos de métricas pode ser encontrado[no repo oficial do Micrometer GitHub](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Como ligar a recolha de métricas adicionais

### <a name="springbootspring"></a>Springboot/primavera

Crie um feijão da respetiva categoria métrica. Por exemplo, digamos que precisamos de métricas de cache guava:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Existem várias métricas que não são ativadas por padrão, mas podem ser ligadas na forma acima. Para obter uma lista completa, consulte [o repo oficial do Micrometer GitHub](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Aplicativos não-Primavera
Adicione o seguinte código de ligação ao ficheiro de configuração:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o Micrometro, consulte a documentação oficial do [Micrometro.](https://micrometer.io/docs)
* Para conhecer a primavera de Azure, consulte a primavera oficial sobre a [documentação do Azure.](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable)
