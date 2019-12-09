---
title: Explorar os logs de rastreamento do Java no Aplicativo Azure insights
description: Pesquisar rastreamentos Log4J ou Logback no Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/18/2019
ms.openlocfilehash: f552ccdbc67df93913c698e5d763dbb62b48a4ad
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927232"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Explore os logs de rastreamento do Java no Application Insights
Se você estiver usando Logback ou Log4J (v 1.2 ou v 2.0) para rastreamento, você pode fazer com que seus logs de rastreamento sejam enviados automaticamente para Application Insights onde você pode explorar e Pesquisar neles.

> [!TIP]
> Você só precisa definir sua chave de instrumentação Application Insights uma vez para seu aplicativo. Se você estiver usando uma estrutura como o Java Spring, talvez já tenha registrado a chave em outro lugar na configuração do aplicativo.

## <a name="using-the-application-insights-java-agent"></a>Usando o agente Java Application Insights

Por padrão, o Application Insights agente Java captura automaticamente o log executado no nível `WARN` e acima.

Você pode alterar o limite de log que é capturado usando o arquivo de `AI-Agent.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging threshold="info"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

Você pode desabilitar a captura de log do agente Java usando o arquivo de `AI-Agent.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging enabled="false"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="alternatively-as-opposed-to-using-the-java-agent-you-can-follow-the-instructions-below"></a>Como alternativa (em oposição ao uso do agente Java), você pode seguir as instruções abaixo

### <a name="install-the-java-sdk"></a>Instalar o SDK do Java

Siga as instruções para instalar [Application insights SDK para Java][java], caso ainda não tenha feito isso.

### <a name="add-logging-libraries-to-your-project"></a>Adicionar bibliotecas de log ao seu projeto
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
Se o projeto já estiver configurado para usar o gradle para compilação, adicione uma das linhas a seguir ao grupo de `dependencies` em seu arquivo Build. gradle:

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

| Digita | Transferir | Biblioteca |
| --- | --- | --- |
| Logback |[Jar do appender Logback](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Jar do appender Log4J v2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4j v1.2 |[Jar do appender do Log4J v 1.2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


### <a name="add-the-appender-to-your-logging-framework"></a>Adicionar o acrescentador à sua estrutura de log
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

## <a name="next-steps"></a>Passos seguintes
[Pesquisa de diagnóstico][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


