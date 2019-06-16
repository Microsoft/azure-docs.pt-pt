---
title: Como utilizar Micrometer com o SDK de Java do Azure Application Insights | Documentos da Microsoft
description: 'Um guia passo a passo sobre como utilizar Micrometer com as suas aplicações Spring Boot do Application Insights e não do Spring Boot. '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: lagayhar
ms.openlocfilehash: 778690fb2796cea3154b3acbb662341fdaea87da
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60699143"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Como utilizar Micrometer com o SDK de Java do Azure Application Insights
Métricas de medidas de monitorização de aplicações do micrometer para aplicações baseadas em JVM de código e permite-lhe exportar os dados para os seus favoritos sistemas de monitorização. Este artigo irá ensiná-lo a utilizar Micrometer com o Application Insights para o Spring Boot e as aplicações não Spring Boot.

## <a name="using-spring-boot-15x"></a>Usando a Primavera de arranque x 1,5
Adicione as seguintes dependências ao seu ficheiro pom. XML ou gradle: 
* [Application Insights spring-boot-starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter)1.1.0-BETA ou superior
* Micrometer 1.1.0 o registo do Azure ou superior
* [Herdado de micrometer Spring](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 ou superior (este backports o código de configuração automática do Framework Spring).
* [Recurso do Application Insights](../../azure-monitor/app/create-new-resource.md )

Passos

1. Atualize o ficheiro pom. XML da sua aplicação de Spring Boot e adicione as seguintes dependências no mesmo:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>1.1.0-BETA</version>
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
2. Atualize o ficheiro. yml ou Application com a chave de instrumentação do Application Insights com a seguinte propriedade:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Crie seu aplicativo e executar
2. Acima deve ajudá-lo a cópia de segurança e em execução com automática de métricas agregadas previamente recolhidos para o Azure Monitor. Para obter detalhes sobre como ajustar o Application Insights Spring Boot starter, consulte a [Leiame no GitHub](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Através da Primavera de 2.x

Adicione as seguintes dependências ao seu ficheiro pom. XML ou gradle:

* Application Insights Spring-boot-starter 2.1.2 ou superior
* Azure-spring-boot-métricas-iniciantes 2.0.7 ou superior  
* [Recurso do Application Insights](../../azure-monitor/app/create-new-resource.md )

Passos:

1. Atualize o ficheiro pom. XML da sua aplicação de Spring Boot e adicione a seguinte dependência no mesmo:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Atualize o ficheiro. yml ou Application com a chave de instrumentação do Application Insights com a seguinte propriedade:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Crie seu aplicativo e executar
4. O procedimento acima deve ajudá-lo em execução com automática de métricas agregadas previamente recolhida para o Azure Monitor. Para obter detalhes sobre como ajustar o Application Insights Spring Boot starter, consulte a [Leiame no GitHub](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Métricas predefinidas:

*    Configurada automaticamente métricas para Tomcat, JVM, Logback métricas, métricas de Log4J, métricas de tempo de atividade, métricas de processador, FileDescriptorMetrics.
*    Por exemplo, se o netflix hystrix está presente no caminho da classe recebemos essas métricas também. 
*    As métricas seguintes podem estar disponíveis, adicionando beans respectivos. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcaseCache, Jcache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - Métricas de OkHttp3 
        - Métricas de Kafka 

 

Como desativar a recolha de métricas automática: 
 
- Métricas JVM: 
    - management.metrics.binders.jvm.enabled=false 
- Métricas de Logback: 
    - management.metrics.binders.logback.enabled=false
- Métricas de tempo de atividade: 
    - Management.Metrics.binders.uptime.Enabled=FALSE 
- Métricas de processador:
    -  management.metrics.binders.processor.enabled=false 
- FileDescriptorMetrics:
    - management.metrics.binders.files.enabled=false 
- Métricas de hystrix se biblioteca no caminho da classe: 
    - management.metrics.binders.hystrix.enabled=false 
- Métricas de AspectJ se biblioteca no caminho da classe: 
    - spring.aop.enabled=false 

> [!NOTE]
> Especificar as propriedades acima no ficheiro Application ou application.yml da sua aplicação de Spring Boot

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Utilizar Micrometer com aplicativos da web não Spring Boot

Adicione as seguintes dependências ao seu ficheiro pom. XML ou gradle:
 
* [Núcleo de informações da aplicação 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.2.0) ou superior
* [Application Insights Web 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/2.2.0) ou superior
* [Registar o filtro da Web](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* Micrometer 1.1.0 o registo do Azure ou superior
* [Recurso do Application Insights](../../azure-monitor/app/create-new-resource.md )

Passos:

1. Adicione as seguintes dependências no ficheiro pom. XML ou gradle:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web</artifactId>
            <version>2.2.0</version>
        </dependency
     ```

2. Colocar o aplicativo Insights.xml na pasta de recursos

    Classe de Servlet de exemplo (emite uma métrica de temporizador):

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

      Classe de configuração de exemplo:

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

Para saber mais sobre métricas, consulte a [documentação Micrometer](https://micrometer.io/docs/).

Outro código de exemplo sobre como criar diferentes tipos de métricas pode ser encontrado no[o repositório do GitHub de Micrometer oficial](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Como ligar a coleção de métricas adicionais

### <a name="springbootspring"></a>SpringBoot/Spring

Crie um bean da categoria de métrica respectiva. Por exemplo, digamos que precisamos Guava as métricas da cache:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Existem várias métricas que não estão ativadas por predefinição, mas podem ser vinculadas a maneira acima. Para obter uma lista completa, consulte [o repositório do GitHub de Micrometer oficial](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Aplicações não Spring
Adicione o seguinte código de enlace para o ficheiro de configuração:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre Micrometer Consulte oficial [documentação Micrometer](https://micrometer.io/docs).
* Para saber mais sobre o Spring no Azure, consulte a oficial [Spring na documentação do Azure](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable).
