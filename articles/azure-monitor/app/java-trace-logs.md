---
title: Explore registos de vestígios de Java em Azure Application Insights
description: Pesquisa de registo4J ou rastreios de logback em Insights de Aplicações
ms.topic: conceptual
ms.date: 05/18/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 4013b60a5718de6c196e806f894503f63ec9dafd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593679"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Explore registos de vestígios de Java em Insights de Aplicação

> [!IMPORTANT]
> A abordagem descrita neste documento já não é recomendada.
>
> A abordagem recomendada para monitorizar as aplicações java é utilizar a auto-instrumentação sem alterar o código. Siga as diretrizes para o [agente Application Insights Java 3.0](./java-in-process-agent.md).

Se estiver a utilizar o Logback ou o Log4J (v1.2 ou v2.0) para rastreio, pode enviar automaticamente os seus registos de rastreios para o Application Insights, onde poderá explorar e pesquisar neles.

> [!TIP]
> Só precisa de definir a sua Chave de Instrumentação de Insights de Aplicação uma vez para a sua aplicação. Se estiver a utilizar uma estrutura como a Java Spring, pode já ter registado a chave em outro lugar na configuração da sua aplicação.

## <a name="using-the-application-insights-java-agent"></a>Usando o agente Java Do Application Insights

Por predefinição, o agente Java Application Insights captura automaticamente o registo realizado ao `WARN` nível e acima.

Pode alterar o limiar de registo registado que é capturado utilizando o `AI-Agent.xml` ficheiro:

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

Pode desativar a captura de registo do agente Java utilizando o `AI-Agent.xml` ficheiro:

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

## <a name="alternatively-as-opposed-to-using-the-java-agent-you-can-follow-the-instructions-below"></a>Alternativamente (em vez de usar o agente Java), pode seguir as instruções abaixo

### <a name="install-the-java-sdk"></a>Instale o Java SDK

Siga as instruções para instalar o [Application Insights SDK para Java,][java]se ainda não o fez.

### <a name="add-logging-libraries-to-your-project"></a>Adicione bibliotecas madeireiras ao seu projeto
*Escolha a forma adequada para o seu projeto.*

#### <a name="if-youre-using-maven"></a>Se estiver a utilizar Maven...
Se o seu projeto já estiver configurado para usar o Maven para construir, misture um dos seguintes excertos de código no seu ficheiro pom.xml.

Em seguida, refresque as dependências do projeto, para fazer com que os binários sejam descarregados.

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
Se o seu projeto já estiver configurado para utilizar Gradle para construção, adicione uma das seguintes linhas ao `dependencies` grupo no seu ficheiro build.gradle:

Em seguida, refresque as dependências do projeto, para fazer com que os binários sejam descarregados.

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
Siga as diretrizes para instalar manualmente o Application Insights Java SDK, descarregue o jarro (Depois de chegar à Página Central de Maven clique no link 'jar' na secção de descarregamento) para obter o appender apropriado e adicione o frasco de apetender descarregado ao projeto.

| Madeir | Download | Biblioteca |
| --- | --- | --- |
| Logback |[Logback appender Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Frasco de appender Log4J v2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |aplicaçõesinsights-logging-log4j2 |
| Log4j v1.2 |[Frasco de appender Log4J v1.2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |aplicações-ineserções-log4j1_2 |


### <a name="add-the-appender-to-your-logging-framework"></a>Adicione o appender à sua estrutura de registo
Para começar a obter vestígios, misture o corte de código relevante para o ficheiro de configuração Log4J ou Logback: 

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

Os appenders Application Insights podem ser referenciados por qualquer madeireira configurado, e não necessariamente pelo madeir de raiz (como mostrado nas amostras de código acima).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Explore os seus vestígios no portal Application Insights
Agora que configura o seu projeto para enviar vestígios para o Application Insights, pode visualizar e pesquisar estes vestígios no portal Deteções de Aplicações, na lâmina [de pesquisa.][diagnostic]

As exceções submetidas através de madeireiros serão apresentadas no portal como Telemetria de Exceção.

![No portal Application Insights, abrir procura](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Passos seguintes
[Pesquisa de diagnóstico][diagnostic]

<!--Link references-->

[diagnostic]: ./diagnostic-search.md
[java]: java-get-started.md

