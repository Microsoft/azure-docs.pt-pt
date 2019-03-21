---
title: Monitorização de desempenho para aplicações web de Java no Azure Application Insights | Documentos da Microsoft
description: Estendidas de desempenho e monitorização da utilização do seu site de Java com o Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: mbullwin
ms.openlocfilehash: ce5f7ab1e6751a9ce68aa2d9c466a112c9cac182
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58004039"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitorizar dependências, exceções recebidas e tempos de execução do método em aplicações web Java


Se tiver [instrumentado a sua aplicação web de Java com o Application Insights][java], pode utilizar o agente Java para obter informações mais aprofundadas, sem quaisquer alterações de código:

* **Dependências:** Dados sobre chamadas de que a aplicação faz para outros componentes, incluindo:
  * **Chamadas REST** feita por meio do HttpClient, OkHttp e RestTemplate (Spring) são capturadas.
  * **Redis** chamadas efetuadas através do cliente de Jedis são capturadas.
  * **[Chamadas JDBC](https://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**  -comandos do MySQL, SQL Server e Oracle DB são automaticamente capturados. Para o MySQL, se a chamada demora mais tempo do que 10s, o agente reporta o plano de consulta.
* **Exceções recebidas:** Informações sobre as exceções que são processados pelo seu código.
* **Tempo de execução do método:** Informações sobre o tempo que demora a executar métodos específicos.

Para utilizar o agente Java, instalá-lo no seu servidor. As aplicações web tem de ser equipadas com o [SDK de Java do Application Insights][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Instalar o agente do Application Insights para Java
1. No computador a executar o seu servidor de Java [transferir o agente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Certifique-se para baixar a mesma versão do agente Java como pacotes de núcleo e web de Java SDK do Application Insights.
2. Edite o script de inicialização do servidor de aplicação e adicione o JVM seguinte:
   
    `javaagent:`*caminho completo para o ficheiro JAR do agente*
   
    Por exemplo, no Tomcat numa máquina Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Reinicie o servidor de aplicação.

## <a name="configure-the-agent"></a>Configurar o agente
Crie um ficheiro denominado `AI-Agent.xml` e colocá-lo na mesma pasta que o ficheiro JAR do agente.

Defina o conteúdo do arquivo xml. Edite o exemplo seguinte para incluir ou omitir os recursos que pretende.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />
           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

Tem de ativar a exceção de relatórios e o tempo de método para métodos individuais.

Por predefinição, `reportExecutionTime` é verdadeiro e `reportCaughtExceptions` é false.

## <a name="additional-config-spring-boot"></a>Configurações adicionais (Spring Boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Para serviços de aplicações do Azure, efetue o seguinte procedimento:

* Selecione Definições > Definições da Aplicação
* Em Definições da Aplicação, adicione um par de chaves-valores novo:

Chave: `JAVA_OPTS` Valor: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.3.1-SNAPSHOT.jar`

A versão mais recente do agente Java verificar as versões [aqui](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

O agente deve ser empacotado como um recurso no seu projeto, de modo que ele acaba o d: / home/site/wwwroot/diretório. Pode confirmar que o agente está no diretório correto do serviço de aplicações, acedendo a **ferramentas de desenvolvimento** > **ferramentas avançadas** > **depurar consola**e examinando o conteúdo do diretório do site.    

* Guardar as definições e reinicie a aplicação. (Estes passos só se aplicam aos serviços de aplicação em execução no Windows.)

> [!NOTE]
> IA Agent.xml e o ficheiro jar do agente devem estar na mesma pasta. Eles, muitas vezes, são colocados em conjunto no `/resources` pasta do projeto.  

### <a name="spring-rest-template"></a>Modelo de Rest de Spring

Na ordem do Application Insights instrumentar com êxito a chamadas HTTP feitas com o modelo de Rest do Spring, o uso do cliente HTTP Apache é necessário. Por predefinição o modelo de Rest do Spring não está configurado para utilizar o cliente de HTTP do Apache. Ao especificar [HttpComponentsClientHttpRequestfactory](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/client/HttpComponentsClientHttpRequestFactory.html) no construtor de um modelo de Rest de Spring, este irá utilizar o Apache HTTP.

Eis um exemplo de como fazê-lo com o Spring Beans. Este é um exemplo muito simples que utiliza as predefinições da classe de fábrica.

```java
@bean
public ClientHttpRequestFactory httpRequestFactory() {
return new HttpComponentsClientHttpRequestFactory()
}
@Bean(name = 'myRestTemplate')
public RestTemplate dcrAccessRestTemplate() {
    return new RestTemplate(httpRequestFactory())
}
```

#### <a name="enable-w3c-distributed-tracing"></a>Ativar o rastreio distribuído do W3C

Adicione o seguinte ao IA-Agent.xml:

```xml
<Instrumentation>
        <BuiltIn enabled="true">
            <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
        </BuiltIn>
    </Instrumentation>
```

> [!NOTE]
> O modo de compatibilidade com versões anteriores está ativado por predefinição e o parâmetro enableW3CBackCompat é opcional e deve ser usado apenas quando pretender desativá-la. 

Idealmente, isso seria o caso quando todos os seus serviços foram atualizados para a versão mais recente de SDKs que suporta o protocolo de W3C. É altamente recomendado para mover para a versão mais recente dos SDKs com suporte de W3C logo que possível.

Certifique-se de que **ambos [entrada](correlation.md#w3c-distributed-tracing) e de saída configurações (agente)** são exatamente iguais.

## <a name="view-the-data"></a>Ver os dados
O recurso do Application Insights, agregados remotos dependência e o método tempos de execução aparece [sob o mosaico de desempenho][metrics].

Para procurar instâncias individuais de dependência, a exceção e o método de relatórios, abra [pesquisa][diagnostic].

[Diagnóstico de dependência de problemas - Saiba mais](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Tem dúvidas? Problemas?
* Não existem dados? [Exceções de firewall de conjunto](../../azure-monitor/app/ip-addresses.md)
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
