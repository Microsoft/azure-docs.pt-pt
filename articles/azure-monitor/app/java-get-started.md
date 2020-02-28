---
title: 'Quickstart: Análise de aplicações web java com Insights de Aplicação Azure'
description: 'Monitorização do Desempenho de Aplicações para aplicações Web Java com o Application Insights. '
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 05/24/2019
ms.openlocfilehash: 484d4e8df8a8fdceed62a65858126a16d028121e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670088"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>Quickstart: Comece com Application Insights num projeto web da Java

Neste arranque rápido, utiliza os Application Insights para solicitar automaticamente instrumentos, rastrear dependências e recolher contadores de desempenho, diagnosticar problemas de desempenho e exceções e escrever código para acompanhar o que os utilizadores fazem com a sua app.

Application Insights é um serviço de análise extensível para desenvolvedores web que o ajuda a entender o desempenho e o uso da sua aplicação ao vivo. O Application Insights suporta aplicações em Java em execução no Linux, Unix ou Windows.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Uma aplicação java funcional.

## <a name="get-an-application-insights-instrumentation-key"></a>Obter uma chave de instrumentação do Application Insights

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No portal Azure, crie um recurso Application Insights. Defina o tipo de aplicação para aplicação Web em Java.

3. Localize a chave de instrumentação do novo recurso. Terá de colar esta chave no seu projeto de código em breve.

    ![Na descrição geral do novo recurso, clique em Propriedades e copie a Chave de Instrumentação](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>Adicionar o Application Insights SDK para Java ao projeto

*Escolha o seu tipo de projeto.*

# <a name="maven"></a>[Maven](#tab/maven)

Se o seu projeto já estiver configurado para utilizar a Maven para construir, misture o seguinte código com o ficheiro *pom.xml.*

Em seguida, atualize as dependências do projeto para obter os binários transferidos.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

Se o seu projeto já estiver configurado para utilizar o Gradle para construir, misture o seguinte código para o seu ficheiro *build.gradle.*

Em seguida, atualize as dependências do projeto para obter os binários transferidos.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

# <a name="other-types"></a>[Outros tipos](#tab/other)

Transfira a [versão mais recente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) e copie os ficheiros necessários para o seu projeto, o que substitui as versões anteriores.

---

### <a name="questions"></a>Perguntas
* *Qual é a relação entre os componentes `-web-auto`, `-web` e `-core`?*
  * `applicationinsights-web-auto` dá-lhe métricas que rastreiam as contagens de pedido de servlet HTTP e os tempos de resposta, registando automaticamente o filtro de servlet Application Insights no tempo de funcionamento.
  * `applicationinsights-web` também lhe dá métricas que rastreiam as contagens de pedido de servlet HTTP e os tempos de resposta, mas requer o registo manual do filtro de servlet Application Insights na sua aplicação.
  * `applicationinsights-core` lhe dá apenas a API nua, por exemplo, se a sua aplicação não for baseada em servlet.
  
* *Como posso atualizar o SDK para a versão mais recente?*
  * Se estás a usar o Gradle ou o Maven...
    * Atualize o seu ficheiro de construção para especificar a versão mais recente.
  * Se está sondo manualmente com dependências...
    * Transfira a versão mais recente de [Application Insights SDK para Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) e substitua as anteriores. As alterações são descritas nas [notas de versão do SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="add-an-applicationinsightsxml-file"></a>Adicione um ficheiro *ApplicationInsights.xml*
Adicione *ApplicationInsights.xml* à pasta de recursos do seu projeto, ou certifique-se de que é adicionado ao caminho da classe de implementação do seu projeto. Copie o seguinte XML para a mesma.

Substitua a chave de instrumentação pela que obteve do portal Azure.

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

Opcionalmente, o ficheiro de configuração pode estar em qualquer local acessível à sua aplicação.  A propriedade do sistema `-Dapplicationinsights.configurationDirectory` especifica o diretório que contém *ApplicationInsights.xml*. Por exemplo, um ficheiro de configuração localizado em `E:\myconfigs\appinsights\ApplicationInsights.xml` seria configurado com a propriedade `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* A chave de instrumentação é enviada juntamente com todos os itens de telemetria e diz ao Application Insights para apresentá-la no seu recurso.
* O componente de Pedido HTTP é opcional. Envia automaticamente telemetria sobre pedidos e tempos de resposta para o portal.
* A correlação de eventos é uma adição ao componente de pedidos HTTP. Atribui um identificador a cada pedido recebido pelo servidor. Em seguida, adiciona este identificador como uma propriedade a cada item de telemetria como a propriedade 'Operation.Id'. Permite-lhe correlacionar a telemetria associada a cada pedido, definindo um filtro em [pesquisa de diagnóstico][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Maneiras alternativas de definir a chave de instrumentação
O SDK do Application Insights procura a chave pela seguinte ordem:

1. Propriedade do sistema: -DAPPINSIGHTS_INSTRUMENTATIONKEY=your_ikey
2. Variável ambiental: APPINSIGHTS_INSTRUMENTATIONKEY
3. Ficheiro de configuração: *ApplicationInsights.xml*

Também pode [defini-lo no código](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>Adicionar agente

[Instale o Agente Java](java-agent.md) para capturar chamadas http cessantes, consultas JDBC, registo de aplicações e melhor nomeação de operações.

## <a name="run-your-application"></a>Executar a aplicação
Execute-a no modo de depuração no seu computador de desenvolvimento ou publique-a no seu servidor.

## <a name="view-your-telemetry-in-application-insights"></a>Ver a telemetria no Application Insights
Regresse ao seu recurso do Application Insights no [Portal do Microsoft Azure](https://portal.azure.com).

Os dados de pedidos HTTP aparecem no painel de descrição geral. (Se não aparecerem, aguarde alguns segundos e, em seguida, clique em Atualizar.)

![Screenshot dos dados da amostra de visão geral](./media/java-get-started/overview-graphs.png)

[Saiba mais sobre métricas.][metrics]

Clique em qualquer gráfico para ver métricas agregadas mais detalhadas.

![Falhas de insights de aplicação painel com gráficos](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Dados de instâncias
Clique num tipo de pedido específico para ver instâncias individuais.

![Perfurar numa visão específica da amostra](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Análise: linguagem de consulta poderosa
À medida que se acumulam mais dados, pode executar consultas para agregar dados e localizar instâncias individuais.  A [análise](../../azure-monitor/app/analytics.md) é uma ferramenta poderosa para compreender o desempenho e a utilização, e para fins de diagnóstico.

![Exemplo de Análise](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Instale a sua aplicação no servidor
Agora publique a aplicação no servidor, permita que as pessoas a utilizem e veja a telemetria a ser mostrada no portal.

* Certifique-se que a firewall permite à aplicação enviar telemetria para estas portas:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Se o tráfego de saída tiver de ser encaminhado através de uma firewall, defina as propriedades do sistema `http.proxyHost` e `http.proxyPort`.

* Nos servidores do Windows, instale:

  * [Microsoft Visual C++ Redistributable](https://www.microsoft.com/download/details.aspx?id=40784)

    (Este componente ativa os contadores de desempenho.)

## <a name="azure-app-service-config-spring-boot"></a>Azure App Service config (Bota de primavera)

As aplicações spring boot que funcionam no Windows requerem uma configuração adicional para funcionar em Serviços de Aplicações Azure. Modificar **web.config** e adicionar a seguinte configuração:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>Exceções e falhas de pedido
As exceções não tratadas e as falhas de pedido são automaticamente recolhidas pelo filtro web Application Insights.

Para recolher dados sobre outras exceções, pode [inserir chamadas para rastrear A exceção no seu código][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Monitorizar chamadas de método e dependências externas
[Instale o Agente Java](java-agent.md) para registar métodos internos especificados e as chamadas efetuadas através de JDBC, com dados de temporização.

E para o nome amento da operação automática.

## <a name="w3c-distributed-tracing"></a>Rastreio distribuído w3C

A Aplicação Insights Java SDK suporta agora o rastreio distribuído pela [W3C](https://w3c.github.io/trace-context/).

A configuração de SDK que está a chegar é explicada ainda mais no nosso artigo sobre [correlação](correlation.md#telemetry-correlation-in-the-java-sdk).

A configuração SDK de saída é definida no ficheiro [AI-Agent.xml.](java-agent.md)

## <a name="performance-counters"></a>Contadores de desempenho
Open **Investigate**, **Metrics,** para ver uma gama de contadores de desempenho.

![Screenshot de painel de métricas com bytes de processo privados selecionados](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Personalizar a recolha do contador de desempenho
Para desativar a recolha do conjunto padrão de contadores de desempenho, adicione o seguinte código sob o nó raiz do ficheiro *ApplicationInsights.xml:*

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Recolher contadores de desempenho adicionais
Pode especificar contadores de desempenho adicionais para recolha.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>Contadores JMX (expostos pela Máquina Virtual Java)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` – O nome apresentado no portal do Application Insights.
* `objectName` – O nome do objeto JMX.
* `attribute` – O atributo do nome do objeto JMX a obter
* `type` (opcional) - O tipo de atributo do objeto JMX:
  * Predefinição: um tipo simples, como int ou long.
  * `composite`: os dados de contador de desempenho estão no formato “Attribute.Data”
  * `tabular`: os dados de contador de desempenho estão no formato de uma linha de tabela

#### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows
Cada [contador de desempenho do Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) é membro de uma categoria (da mesma forma que um campo é membro de uma classe). As categorias podem ser globais ou podem ter instâncias numeradas ou nomeadas.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName – o nome apresentado no portal do Application Insights.
* categoryName – a categoria do contador de desempenho (objeto de desempenho) à qual este contador de desempenho está associado.
* counterName – o nome do contador de desempenho.
* instanceName – o nome da instância da categoria do contador de desempenho ou uma cadeia vazia (""), se a categoria contiver uma única instância. Se o categoryName for Processo e o contador de desempenho que pretende recolher é do processo JVM atual no qual a aplicação está em execução, especifique `"__SELF__"`.

### <a name="unix-performance-counters"></a>Contadores de desempenho Unix
* [Instale collectd com o plug-in do Application Insights](java-collectd.md) para obter uma ampla variedade de dados de sistema e de rede.

## <a name="get-user-and-session-data"></a>Obter dados de utilizador e de sessão
OK, está a enviar telemetria a partir do seu servidor Web. Para obter a vista completa em 360 graus da sua aplicação, agora pode adicionar mais monitorização:

* [Adicione telemetria às suas páginas web][usage] para monitorizar as visualizações das páginas e métricas do utilizador.
* [Instale testes web][availability] para garantir que a sua aplicação permaneça ao vivo e responsiva.

## <a name="send-your-own-telemetry"></a>Enviar a sua própria telemetria
Agora que instalou o SDK, pode utilizar a API para enviar a sua própria telemetria.

* [Acompanhe eventos e métricas personalizados][api] para saber o que os utilizadores estão a fazer com a sua aplicação.
* [Pesquisar eventos e registos][diagnostic] para ajudar a diagnosticar problemas.

## <a name="availability-web-tests"></a>Testes Web de disponibilidade
O Application Insights pode testar o seu site em intervalos regulares para verificar se está a funcionar e a responder bem.

[Saiba mais sobre como configurar os testes web de disponibilidade.][availability]

## <a name="questions-problems"></a>Tem dúvidas? Problemas?
[Resolução de problemas de Java](java-troubleshoot.md)

## <a name="next-steps"></a>Passos seguintes
* [Chamadas de dependência do monitor](java-agent.md)
* [Contadores de desempenho Unix do monitor](java-collectd.md)
* Adicione [monitorização das suas páginas Web](javascript.md), para monitorizar os tempos de carregamento da página, as chamadas AJAX e as exceções de browser.
* Escreva [telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md) para controlar a utilização no browser ou no servidor.
* Utilize o [Analytics](../../azure-monitor/app/analytics.md) para consultas eficientes através de telemetria a partir da aplicação
* Para obter mais informações, visite [Azure para programadores Java](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
