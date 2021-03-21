---
title: Monitorização de desempenho de aplicativos web java - Azure Application Insights
description: Monitorização alargada de desempenho e utilização do seu website Java com Application Insights.
ms.topic: conceptual
ms.date: 01/10/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: c753e4e254890f9198da9bc913b29bdaae335b78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100573835"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitorizar dependências, exceções capturadas e tempos de execução de métodos em aplicações web java

> [!IMPORTANT]
> A abordagem descrita neste documento já não é recomendada.
>
> A abordagem recomendada para monitorizar as aplicações java é utilizar a auto-instrumentação sem alterar o código. Siga as diretrizes para o [agente Application Insights Java 3.0](./java-in-process-agent.md).

Se tiver [instrumentado a sua aplicação web Java com App Insights SDK,][java]pode utilizar o Agente Java para obter informações mais profundas, sem alterações de código:

* **Dependências:** Dados sobre chamadas que a sua aplicação faz a outros componentes, incluindo:
  * **Chamadas HTTP de saída feitas** via Apache HttpClient, OkHttp, e `java.net.HttpURLConnection` são capturadas.
  * **As chamadas vermelhas** feitas através do cliente Jedis são capturadas.
  * **Consultas JDBC** - Para MySQL e PostgreSQL, se a chamada demorar mais de 10 segundos, o agente reporta o plano de consulta.

* **Registo de aplicações:** Capture e correlacione os seus registos de aplicações com pedidos HTTP e outras telemetrias
  * **Log4j 1.2**
  * **Log4j2**
  * **Logback**

* **Melhor operação naming:** (usado para agregação de pedidos no portal)
  * **primavera** - baseada em `@RequestMapping` .
  * **JAX-RS** - baseado em `@Path` . 

Para utilizar o agente Java, instale-o no seu servidor. As suas aplicações web devem ser instrumentadas com a [Aplicação Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Instale o agente Application Insights para Java
1. Na máquina que executa o seu servidor Java, [descarregue o agente 2.x](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/2.6.2). Certifique-se de que a versão do Agente Java 2.x que utiliza corresponde à versão do 2.x Application Insights Java SDK que utiliza.
2. Editar o script de arranque do servidor de aplicações e adicionar o seguinte argumento JVM:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Por exemplo, em Tomcat numa máquina Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Reinicie o servidor de aplicações.

## <a name="configure-the-agent"></a>Configure o agente
Crie um ficheiro com o nome `AI-Agent.xml` e coloque-o na mesma pasta que o ficheiro JAR do agente.

Desaver o conteúdo do ficheiro xml. Edite o exemplo seguinte para incluir ou omitir as funcionalidades que pretende.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>Config adicional (Bota de primavera)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Para os Serviços de Aplicações Azure, faça o seguinte:

* Selecione Definições > Definições da Aplicação
* Em Definições da Aplicação, adicione um par de chaves-valores novo:

Chave: `JAVA_OPTS` Valor: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.6.2.jar`

O agente deve ser embalado como recurso no seu projeto de modo a que acabe no D:/home/site/wwwroot/diretório. Pode confirmar que o seu agente está no diretório correto do Serviço de Aplicações, indo para a **Development Tools**  >  **Advanced Tools**  >  **Debug Console** e examinando o conteúdo do diretório do site.    

* Guarde as definições e reinicie a aplicação. (Estes passos aplicam-se apenas aos Serviços de Aplicações em execução no Windows.)

> [!NOTE]
> AI-Agent.xml e o ficheiro do frasco do agente devem estar na mesma pasta. São muitas vezes colocados juntos na `/resources` pasta do projeto.  

#### <a name="enable-w3c-distributed-tracing"></a>Ativar o rastreio distribuído W3C

Adicione o seguinte a AI-Agent.xml:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> O modo de compatibilidade retrógrada é ativado por predefinição e o parâmetro enableW3CBackCompat é opcional e só deve ser utilizado quando pretender desligá-lo. 

Idealmente, este seria o caso quando todos os seus serviços foram atualizados para a versão mais recente de SDKs suportando o protocolo W3C. É altamente recomendado passar para a versão mais recente de SDKs com suporte W3C o mais rápido possível.

Certifique-se de que **as configurações [de entrada](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) e saída (agente)** são exatamente iguais.

## <a name="view-the-data"></a>Ver os dados
No recurso Application Insights, os tempos de dependência remota agregados e tempos de execução de métodos aparecem [sob o azulejo de Desempenho][metrics].

Para procurar casos individuais de dependência, exceção e relatórios de métodos, abra [a Pesquisa.][diagnostic]

[Diagnosticar questões de dependência - saiba mais.](./asp-net-dependencies.md#diagnosis)

## <a name="questions-problems"></a>Perguntas? Problemas?
* Não existem dados? [Definir exceções de firewall](./ip-addresses.md)
* [Resolução de problemas de Java](java-troubleshoot.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#track-exception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../essentials/metrics-charts.md

