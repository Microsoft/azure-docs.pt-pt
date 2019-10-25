---
title: Como usar o micrometer com o SDK do Java do insights Aplicativo Azure | Microsoft Docs
description: 'Um guia passo a passo sobre como usar o micrometer com seus Application Insights aplicativos Spring boot e não Spring boot. '
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 11/01/2018
ms.openlocfilehash: 267665c97f683740c05ae6602a416225c79aa44c
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819303"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Como usar o micrometer com o SDK do Java do insights Aplicativo Azure
O monitoramento de aplicativos micrometer mede as métricas para o código do aplicativo baseado em JVM e permite que você exporte os dados para seus sistemas de monitoramento favoritos. Este artigo ensinará como usar o micrometer com Application Insights para aplicativos Spring boot e não Spring boot.

## <a name="using-spring-boot-15x"></a>Usando o Spring boot 1.5 x
Adicione as seguintes dependências ao arquivo pom. xml ou Build. gradle: 
* [Application insights Spring-boot-Starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter) 2.5.0 ou posterior
* Micrometer Azure registro 1.1.0 ou superior
* [Micrometer Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 ou superior (isso reportará o código de configuração automática no Spring Framework).
* [Recurso ApplicationInsights](../../azure-monitor/app/create-new-resource.md )

Passos

1. Atualize o arquivo pom. XML do seu aplicativo Spring boot e adicione as seguintes dependências a ele:

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
2. Atualize o arquivo Application. Properties ou yml com a chave de instrumentação Application Insights usando a seguinte propriedade:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Compile seu aplicativo e execute
2. O acima deve colocar em funcionamento as métricas previamente agregadas coletadas automaticamente para Azure Monitor. Para obter detalhes sobre como ajustar Application Insights iniciador do Spring boot, consulte o [Leiame no GitHub](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Usando Spring 2. x

Adicione as seguintes dependências ao arquivo pom. xml ou Build. gradle:

* Application Insights Spring-boot-Starter 2.1.2 ou superior
* Azure-Spring-boot-métricas-inicializadores 2.0.7 ou posterior
* [Application Insights recurso](../../azure-monitor/app/create-new-resource.md )

Passos:

1. Atualize o arquivo pom. XML do seu aplicativo Spring boot e adicione a seguinte dependência:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Atualize o arquivo Application. Properties ou yml com a chave de instrumentação Application Insights usando a seguinte propriedade:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Compile seu aplicativo e execute
4. A seguir, você deve fazer com que você execute com as métricas previamente agregadas coletadas automaticamente para Azure Monitor. Para obter detalhes sobre como ajustar Application Insights iniciador do Spring boot, consulte o [Leiame no GitHub](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Métricas padrão:

*    As métricas configuradas automaticamente para Tomcat, JVM, métricas de Logback, métricas de Log4J, métricas de tempo de atividade, métricas de processador, FileDescriptorMetrics.
*    Por exemplo, se Netflix Hystrix estiver presente no caminho de classe, obteremos essas métricas também. 
*    As métricas a seguir podem estar disponíveis adicionando os respectivos Beans. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcastCache, JCache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - Métricas de OkHttp3 
        - Métricas de Kafka 

 

Como desativar a coleção de métricas automáticas: 
 
- Métricas de JVM: 
    - Management. Metrics. associars. JVM. Enabled = False 
- Métricas de Logback: 
    - Management. Metrics. associars. logback. Enabled = False
- Métricas de tempo de atividade: 
    - Management. Metrics. binderers. tempo de atividade. Enabled = False 
- Métricas do processador:
    -  Management. Metrics. associars. Processor. Enabled = False 
- FileDescriptorMetrics:
    - Management. Metrics. binderers. files. Enabled = False 
- Métricas de Hystrix se biblioteca no classpath: 
    - Management. Metrics. associars. Hystrix. Enabled = False 
- Métricas de AspectJ se biblioteca no classpath: 
    - Spring. AOP. Enabled = False 

> [!NOTE]
> Especifique as propriedades acima no arquivo Application. Properties ou Application. yml do seu aplicativo Spring boot

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Usar o micrometer com aplicativos Web que não são Spring boot

Adicione as seguintes dependências ao arquivo pom. xml ou Build. gradle:

* Application Insights Web auto 2.5.0 ou posterior
* Micrometer Azure registro 1.1.0 ou superior
* [Application Insights recurso](../../azure-monitor/app/create-new-resource.md )

Passos:

1. Adicione as seguintes dependências em seu arquivo pom. xml ou Build. gradle:

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

2. Coloque `ApplicationInsights.xml` arquivo na pasta de recursos:

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

3. Classe de servlet de exemplo (emite uma métrica de temporizador):

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

4. Classe de configuração de exemplo:

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

Para saber mais sobre métricas, consulte a [documentação do micrometer](https://micrometer.io/docs/).

Outros códigos de exemplo sobre como criar diferentes tipos de métricas podem ser encontrados no[repositório GitHub oficial do micrometer](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Como associar uma coleção de métricas adicionais

### <a name="springbootspring"></a>SpringBoot/Spring

Crie um Bean da respectiva categoria de métrica. Por exemplo, digamos que precisamos de métricas de cache guava:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Há várias métricas que não estão habilitadas por padrão, mas que podem ser associadas na maneira acima. Para obter uma lista completa, consulte [o repositório GitHub oficial do micrometer](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Aplicativos que não são Spring
Adicione o seguinte código de associação ao arquivo de configuração:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o micrometer, consulte a [documentação](https://micrometer.io/docs)oficial do micrometer.
* Para saber mais sobre o Spring no Azure, consulte a documentação oficial do [Spring on Azure](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable).
