---
title: Explorar os logs de rastreamento do Java no Aplicativo Azure insights (2.5.0-BETA) | Microsoft Docs
description: Pesquisar rastreamentos Log4J ou Logback no Application Insights (2.5.0-BETA)
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/18/2019
ms.author: mbullwin
ms.openlocfilehash: 028563658256f7bd8e016b5c7bbf703a5030a3de
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298363"
---
# <a name="explore-java-trace-logs-in-application-insights-250-beta"></a>Explorar os logs de rastreamento do Java no Application Insights (2.5.0-BETA)
Se você estiver usando Logback ou Log4J (v 1.2 ou v 2.0) para rastreamento, você pode fazer com que seus logs de rastreamento sejam enviados automaticamente para Application Insights onde você pode explorar e Pesquisar neles.

## <a name="note-if-you-are-using-the-application-insights-java-agent"></a>Observação: se você estiver usando o agente Java Application Insights

Você pode configurar o Application insights agente Java para capturar seus logs automaticamente, habilitando o recurso no `AI-Agent.xml` arquivo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">
         <Logging enabled="true" />
      </BuiltIn>
   </Instrumentation>
   <AgentLogger />
</ApplicationInsightsAgent>
```

Caso contrário...

## <a name="install-the-java-sdk"></a>Instalar o SDK do Java

Siga as instruções para instalar [Application insights SDK para Java][java], caso ainda não tenha feito isso.

## <a name="add-logging-libraries-to-your-project"></a>Adicionar bibliotecas de log ao seu projeto
*Escolha a forma adequada para o seu projeto.*

#### <a name="if-youre-using-maven"></a>Se estiver a utilizar Maven...
Se seu projeto já estiver configurado para usar o Maven para compilação, mescle um dos trechos de código a seguir em seu arquivo pom. xml.

Em seguida, atualize as dependências do projeto para que os binários sejam baixados.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Se estiver a utilizar Gradle...
Se o projeto já estiver configurado para usar o `dependencies` gradle para compilação, adicione uma das linhas a seguir ao grupo no arquivo Build. gradle:

Em seguida, atualize as dependências do projeto para que os binários sejam baixados.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>Caso contrário...
Siga as diretrizes para instalar manualmente Application Insights SDK do Java, baixe o jar (depois de chegar à página central do Maven, clique no link ' jar ' na seção de download) para o anexador apropriado e adicione o JAR do appender baixado ao projeto.

| Digita | Transferência | Biblioteca |
| --- | --- | --- |
| Logback |[Jar do appender Logback](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Jar do appender Log4J v2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4j v1.2 |[Jar do appender do Log4J v 1.2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>Adicionar o acrescentador à sua estrutura de log
Para começar a obter rastreamentos, mescle o trecho de código relevante para o arquivo de configuração Log4J ou Logback: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Os acrescentadores de Application Insights podem ser referenciados por qualquer agente configurado e não necessariamente pelo agente raiz (conforme mostrado nos exemplos de código acima).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Explore seus rastreamentos no portal de Application Insights
Agora que você configurou seu projeto para enviar rastreamentos para Application Insights, você pode exibir e Pesquisar esses rastreamentos no portal de Application Insights, na folha [Pesquisar][diagnostic] .

As exceções enviadas por meio de agentes serão exibidas no portal como telemetria de exceção.

![No portal de Application Insights, abra Pesquisar](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Passos Seguintes
[Pesquisa de diagnóstico][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


