---
title: Explore os registos de rastreio de Java em Insights de Aplicação Azure
description: Pesquisar rastreios de Log4J ou Logback em Insights de Aplicação
ms.topic: conceptual
ms.date: 05/18/2019
ms.openlocfilehash: da1b76d52ab93f4d1be7196d6eb7286579481119
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77657219"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Explore os registos de rastreio de Java em Insights de Aplicação
Se estiver a utilizar o Logback ou o Log4J (v1.2 ou v2.0) para rastreio, pode enviar os seus registos de rastreio automaticamente para Informações de Aplicação, onde pode explorá-los e pesquisar.

> [!TIP]
> Só precisa de definir a chave de instrumentação de insights de aplicação uma vez para a sua aplicação. Se estiver a usar uma estrutura como a Java Spring, pode já ter registado a chave em outros lugares na configuração da sua aplicação.

## <a name="using-the-application-insights-java-agent"></a>Utilização do agente Java Insights de Aplicação

Por predefinição, o agente Java De `WARN` insights de aplicação captura automaticamente o registo realizado ao nível e acima.

Pode alterar o limiar de exploração `AI-Agent.xml` madeireira capturado utilizando o ficheiro:

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

Pode desativar a captura de `AI-Agent.xml` registo do agente Java utilizando o ficheiro:

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

## <a name="alternatively-as-opposed-to-using-the-java-agent-you-can-follow-the-instructions-below"></a>Alternativamente (em oposição à utilização do agente Java), pode seguir as instruções abaixo

### <a name="install-the-java-sdk"></a>Instale o Java SDK

Siga as instruções para instalar o [Application Insights SDK para Java,][java]se ainda não o fez.

### <a name="add-logging-libraries-to-your-project"></a>Adicione bibliotecas de exploração madeireira ao seu projeto
*Escolha a forma adequada para o seu projeto.*

#### <a name="if-youre-using-maven"></a>Se estiver a utilizar Maven...
Se o seu projeto já estiver configurado para utilizar a Maven para construir, misture um dos seguintes fragmentos de código no seu ficheiro pom.xml.

Em seguida, refresque as dependências do projeto, para que os binários sejam descarregados.

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
Se o seu projeto já estiver configurado para utilizar gradle `dependencies` para construir, adicione uma das seguintes linhas ao grupo no seu ficheiro build.gradle:

Em seguida, refresque as dependências do projeto, para que os binários sejam descarregados.

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
Siga as diretrizes para instalar manualmente os Insights da Aplicação Java SDK, descarregue o jarro (Depois de chegar à Página Central de Maven clique no link 'jar' na secção de descarregamento) para o appender apropriado e adicione o frasco de aplicadores descarregado ao projeto.

| Madeireiro | Transferência | Biblioteca |
| --- | --- | --- |
| Logback |[Jar de aparador de logback](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logback |
| Log4J v2.0 |[Jar do anador Log4J v2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4j v1.2 |[Jar do anador Log4J v1.2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


### <a name="add-the-appender-to-your-logging-framework"></a>Adicione o apendicite à sua estrutura de exploração madeireira
Para começar a obter vestígios, misture o fragmento de código relevante para o ficheiro de configuração Log4J ou Logback: 

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

Os apertantes de Insights de Aplicação podem ser referenciados por qualquer madeireiro configurado, e não necessariamente pelo madeireiro raiz (como indicado nas amostras de código acima).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Explore os seus vestígios no portal Application Insights
Agora que configurao o seu projeto para enviar vestígios para Application Insights, pode ver e pesquisar estes vestígios no portal Application Insights, na lâmina [de pesquisa.][diagnostic]

As exceções submetidas através de madeireiros serão apresentadas no portal como Telemetria exceção.

![No portal Application Insights, abra a Pesquisa](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Passos seguintes
[Pesquisa de diagnóstico][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


