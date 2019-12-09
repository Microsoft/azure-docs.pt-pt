---
title: Monitoramento de desempenho de aplicativos Web Java-insights de Aplicativo Azure
description: Monitoramento de uso e desempenho estendido do seu site Java com Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/10/2019
ms.openlocfilehash: 8194c4b16d114be9b2b95ff56dea59d98cfdae10
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931133"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitorar dependências, exceções capturadas e tempos de execução de método em aplicativos Web Java


Se você tiver [instrumentado seu aplicativo Web Java com Application insights][java], poderá usar o agente Java para obter informações mais aprofundadas, sem nenhuma alteração de código:

* **Dependências:** Dados sobre chamadas que seu aplicativo faz para outros componentes, incluindo:
  * As **chamadas http de saída** feitas via Apache HttpClient, OkHttp e `java.net.HttpURLConnection` são capturadas.
  * **Chamadas Redis** feitas por meio do cliente Jedis são capturadas.
  * **Consultas JDBC** – para MySQL e PostgreSQL, se a chamada demorar mais de 10 segundos, o agente relatará o plano de consulta.

* **Log de aplicativo:** Capturar e correlacionar os logs de aplicativo com solicitações HTTP e outras telemetrias
  * **Log4J 1,2**
  * **Log4j2**
  * **Logback**

* **Melhor nomenclatura de operação:** (usada para agregação de solicitações no Portal)
  * Baseado na **mola** `@RequestMapping`.
  * **JAX-RS** -baseado em `@Path`. 

Para usar o agente Java, instale-o em seu servidor. Seus aplicativos Web devem ser instrumentados com o [SDK do Java Application insights][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Instalar o agente de Application Insights para Java
1. No computador com o servidor Java em execução, [transfira o agente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Garanta que transfere a mesma versão do Agente Java que a do núcleo do SDK Java do Application Insights e dos pacotes Web.
2. Edite o script de inicialização do servidor de aplicativos e adicione o seguinte argumento JVM:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Por exemplo, no Tomcat em um computador Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Reinicie o servidor de aplicativos.

## <a name="configure-the-agent"></a>Configurar o agente
Crie um arquivo chamado `AI-Agent.xml` e coloque-o na mesma pasta que o arquivo JAR do agente.

Defina o conteúdo do arquivo XML. Edite o exemplo a seguir para incluir ou omitir os recursos desejados.

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

## <a name="additional-config-spring-boot"></a>Configuração adicional (Spring boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Para Azure App serviços, faça o seguinte:

* Selecione Definições > Definições da Aplicação
* Em Definições da Aplicação, adicione um par de chaves-valores novo:

Chave: `JAVA_OPTS` valor: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

Para obter a versão mais recente do agente Java, verifique as versões [aqui](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

O agente deve ser empacotado como um recurso em seu projeto, de modo que ele termine no diretório D:/Home/site/wwwroot/. Você pode confirmar que o agente está no diretório do serviço de aplicativo correto acessando **ferramentas de desenvolvimento** > **ferramentas avançadas** > **console de depuração** e examinando o conteúdo do diretório do site.    

* Salve as configurações e reinicie o aplicativo. (Essas etapas se aplicam somente aos serviços de aplicativo em execução no Windows.)

> [!NOTE]
> AI-Agent. xml e o arquivo JAR do agente devem estar na mesma pasta. Geralmente, eles são colocados juntos na pasta `/resources` do projeto.  

#### <a name="enable-w3c-distributed-tracing"></a>Habilitar o rastreamento distribuído do W3C

Adicione o seguinte ao AI-Agent. xml:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> O modo de compatibilidade com versões anteriores é habilitado por padrão e o parâmetro enableW3CBackCompat é opcional e deve ser usado somente quando você deseja desativá-lo. 

Idealmente, esse seria o caso quando todos os seus serviços foram atualizados para uma versão mais recente de SDKs com suporte para o protocolo W3C. É altamente recomendável migrar para a versão mais recente de SDKs com suporte W3C assim que possível.

Verifique se **as configurações de [entrada](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) e de saída (agente)** são exatamente iguais.

## <a name="view-the-data"></a>Ver os dados
No recurso Application Insights, a dependência remota agregada e os tempos de execução [do método aparecem no bloco desempenho][metrics].

Para pesquisar instâncias individuais de dependência, exceção e relatórios de método, abra a [pesquisa][diagnostic].

[Diagnosticando problemas de dependência-saiba mais](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Dúvidas? Problemas?
* Não tem dados? [Definir exceções de firewall](../../azure-monitor/app/ip-addresses.md)
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
