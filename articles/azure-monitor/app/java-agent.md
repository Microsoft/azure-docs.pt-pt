---
title: Monitorização do desempenho das aplicações web java - Azure Application Insights
description: Desempenho alargado e monitorização de utilização do seu website java com Insights de Aplicação.
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: b29618179d22eac97a07bf41906465aba1fd7929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657032"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitorize dependências, exceções apanhadas e tempos de execução de métodos em aplicações web java


Se [tiver instrumentado a sua aplicação web Java com Application Insights,][java]pode utilizar o Agente Java para obter informações mais profundas, sem alterações de código:

* **Dependências:** Dados sobre chamadas que a sua aplicação faz a outros componentes, incluindo:
  * **Chamadas http-000 de saída** feitas via Apache HttpClient, OkHttp, e `java.net.HttpURLConnection` capturadas.
  * As **chamadas** vermelhas feitas através do cliente Jedis são capturadas.
  * **Consultas JDBC** - Para MySQL e PostgreSQL, se a chamada demorar mais de 10 segundos, o agente reporta o plano de consulta.

* **Registo de aplicação:** Capture e correlacionar os seus registos de candidatura com pedidos HTTP e outras telemetrias
  * **Log4j 1.2**
  * **Log4j2**
  * **Logback**

* **Melhor operação nomeação:** (utilizado para agregação de pedidos no portal)
  * **primavera** - `@RequestMapping`com base em .
  * **JAX-RS** - `@Path`baseado em . 

Para utilizar o agente Java, instale-o no seu servidor. As suas aplicações web devem ser instrumentadas com os Insights de [Aplicação Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Instale o agente de insights de aplicação para Java
1. No computador com o servidor Java em execução, [transfira o agente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Garanta que transfere a mesma versão do Agente Java que a do núcleo do SDK Java do Application Insights e dos pacotes Web.
2. Editar o script de arranque do servidor de aplicações e adicionar o seguinte argumento JVM:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Por exemplo, em Tomcat numa máquina Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Reinicie o seu servidor de aplicações.

## <a name="configure-the-agent"></a>Configure o agente
Crie um `AI-Agent.xml` ficheiro nomeado e coloque-o na mesma pasta que o ficheiro JAR do agente.

Detete o conteúdo do ficheiro xml. Editar o seguinte exemplo para incluir ou omitir as funcionalidades que deseja.

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

Para serviços de aplicações Azure, faça o seguinte:

* Selecione Definições > Definições da Aplicação
* Em Definições da Aplicação, adicione um par de chaves-valores novo:

Chave: `JAVA_OPTS` Valor:`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

Para obter a versão mais recente do agente Java, consulte os lançamentos [aqui](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

O agente deve ser embalado como recurso no seu projeto de modo a que ele acabe no D:/home/site/wwwroot/ diretório. Pode confirmar que o seu agente está no diretório correto do App Service, indo para **ferramentas** > **Advanced Tools** > avançadas de desenvolvimento**Consola Debug** e examinando os conteúdos do diretório do site.    

* Guarde as definições e reinicie a aplicação. (Estes passos aplicam-se apenas aos Serviços de Aplicações em execução no Windows.)

> [!NOTE]
> AI-Agent.xml e o ficheiro do frasco do agente devem estar na mesma pasta. São muitas vezes `/resources` colocados juntos na pasta do projeto.  

#### <a name="enable-w3c-distributed-tracing"></a>Ativar rastreio distribuído W3C

Adicione o seguinte a AI-Agent.xml:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> O modo de compatibilidade para trás é ativado por predefinição e o parâmetro activaw3CBackCompat é opcional e só deve ser utilizado quando quiser desligá-lo. 

Idealmente, este seria o caso quando todos os seus serviços foram atualizados para a versão mais recente dos SDKs que suportam o protocolo W3C. É altamente recomendado passar para a versão mais recente de SDKs com suporte W3C o mais rápido possível.

Certifique-se de que as configurações de ** [entrada](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) e de saída (agente)** são exatamente iguais.

## <a name="view-the-data"></a>Ver os dados
No recurso Application Insights, os tempos agregados de dependência remota e execução de métodos aparecem [sob o azulejo performance][metrics].

Para procurar casos individuais de dependência, exceção e relatórios de métodos, abra [a Pesquisa.][diagnostic]

[Diagnosticar problemas](../../azure-monitor/app/asp-net-dependencies.md#diagnosis)de dependência - saiba mais .

## <a name="questions-problems"></a>Tem dúvidas? Problemas?
* Não existem dados? [Definir exceções à firewall](../../azure-monitor/app/ip-addresses.md)
* [Resolução de problemas de Java](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
