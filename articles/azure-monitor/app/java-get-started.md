---
title: Análise de aplicação Web em Java com o Application Insights do Azure | Microsoft Docs
description: 'Monitorização do Desempenho de Aplicações para aplicações Web Java com o Application Insights. '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: lagayhar
ms.openlocfilehash: a6e8187a085d637ad3abc650daf15d92b96755a3
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338102"
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Introdução ao Application Insights num projeto Web em Java

O [Application Insights](https://azure.microsoft.com/services/application-insights/) é um serviço de análise extensível para programadores Web que os ajudam a compreender o desempenho e a utilização da aplicação em direto. Use-o para [instrumentar automaticamente a solicitação, controlar as dependências e coletar contadores de desempenho](auto-collect-dependencies.md#java), diagnosticar problemas de desempenho e exceções e [escrever código][api] para controlar o que os usuários fazem com seu aplicativo. 

![Captura de tela de dados de exemplo de visão geral](./media/java-get-started/overview-graphs.png)

O Application Insights suporta aplicações em Java em execução no Linux, Unix ou Windows.

É necessário:

* Java 7 ou posterior
* Uma subscrição do [Microsoft Azure](https://azure.microsoft.com/).

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Obter uma chave de instrumentação do Application Insights
1. Inicie sessão no [Portal do Microsoft Azure](https://portal.azure.com).
2. Crie um recurso do Application Insights. Defina o tipo de aplicação para aplicação Web em Java.

3. Localize a chave de instrumentação do novo recurso. Terá de colar esta chave no seu projeto de código em breve.

    ![Na descrição geral do novo recurso, clique em Propriedades e copie a Chave de Instrumentação](./media/java-get-started/instrumentation-key-001.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Adicionar o Application Insights SDK para Java ao projeto
*Escolha a forma adequada para o seu projeto.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Se estiver a utilizar Maven... <a name="maven-setup" />
Se o seu projeto já estiver configurado para utilizar Maven para a compilação, intercale o código seguinte no ficheiro pom.xml.

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

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Se estiver a utilizar o Gradle... <a name="gradle-setup" />
Se o seu projeto já estiver configurado para utilizar Gradle para a compilação, intercale o código seguinte no ficheiro build.gradle.

Em seguida, atualize as dependências do projeto para obter os binários transferidos.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>Caso contrário, se estiver a gerir as dependências manualmente...
Transfira a [versão mais recente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) e copie os ficheiros necessários para o seu projeto, o que substitui as versões anteriores.

### <a name="questions"></a>Perguntas...
* *Qual é a relação entre os `-web-auto` `-web` componentes e `-core` ?*
  * `applicationinsights-web-auto`fornece métricas que rastreiam contagens de solicitação e tempos de resposta do servlet HTTP, registrando automaticamente o filtro de Application Insights servlet em tempo de execução.
  * `applicationinsights-web`também fornece métricas que controlam as contagens de solicitação e os tempos de resposta do servlet HTTP, mas exigem o registro manual do filtro de Application Insights servlet em seu aplicativo.
  * `applicationinsights-core`oferece apenas a API Bare, por exemplo, se seu aplicativo não for baseado em servlet.
  
* *Como posso atualizar o SDK para a versão mais recente?*
  * Se estiver a utilizar o Gradle ou o Maven...
    * Atualize o arquivo de compilação para especificar a versão mais recente.
  * Se estiver a gerir as dependências manualmente...
    * Transfira a versão mais recente de [Application Insights SDK para Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) e substitua as anteriores. As alterações são descritas nas [notas de versão do SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="3-add-an-applicationinsightsxml-file"></a>3. Adicionar um ficheiro ApplicationInsights.xml
Adicione ApplicationInsights.xml à pasta de recursos do projeto ou certifique-se de que é adicionado ao caminho da classe de implementação do projeto. Copie o seguinte XML para a mesma.

Substitua a chave de instrumentação que recebeu do portal do Azure.

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

Opcionalmente, o ficheiro de configuração pode residir em qualquer localização à qual a sua aplicação possa aceder.  A propriedade do sistema `-Dapplicationinsights.configurationDirectory` especifica o diretório que contém ApplicationInsights.xml. Por exemplo, um ficheiro de configuração localizado em `E:\myconfigs\appinsights\ApplicationInsights.xml` seria configurado com a propriedade `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* A chave de instrumentação é enviada juntamente com todos os itens de telemetria e diz ao Application Insights para apresentá-la no seu recurso.
* O componente de Pedido HTTP é opcional. Envia automaticamente telemetria sobre pedidos e tempos de resposta para o portal.
* A correlação de eventos é uma adição ao componente de pedidos HTTP. Atribui um identificador a cada pedido recebido pelo servidor e adiciona este identificador como uma propriedade a todos os itens de telemetria como a propriedade "Operation.Id". Ele permite que você correlacione a telemetria associada a cada solicitação definindo um filtro na [pesquisa de diagnóstico][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Maneiras alternativas de definir a chave de instrumentação
O SDK do Application Insights procura a chave pela seguinte ordem:

1. Propriedade do sistema:-DAPPINSIGHTS_INSTRUMENTATIONKEY = your_ikey
2. Variável de ambiente: APPINSIGHTS_INSTRUMENTATIONKEY
3. Arquivo de configuração: ApplicationInsights.xml

Também pode [defini-lo no código](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="4-add-agent"></a>4. Adicionar agente

[Instale o agente Java](java-agent.md) para capturar chamadas http de saída, consultas JDBC, log de aplicativo e melhor nomenclatura de operação.

## <a name="5-run-your-application"></a>5. Executar a aplicação
Execute-a no modo de depuração no seu computador de desenvolvimento ou publique-a no seu servidor.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Ver a telemetria no Application Insights
Regresse ao seu recurso do Application Insights no [Portal do Microsoft Azure](https://portal.azure.com).

Os dados de pedidos HTTP aparecem no painel de descrição geral. (Se não aparecerem, aguarde alguns segundos e, em seguida, clique em Atualizar.)

![Captura de tela de dados de exemplo de visão geral](./media/java-get-started/overview-graphs.png)

[Saiba mais sobre métricas.][metrics]

Clique em qualquer gráfico para ver métricas agregadas mais detalhadas.

![Painel de falhas Application Insights com gráficos](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Dados de instâncias
Clique num tipo de pedido específico para ver instâncias individuais.

![Analisar uma exibição de exemplo específica](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analytics Linguagem de consulta avançada
À medida que se acumulam mais dados, pode executar consultas para agregar dados e localizar instâncias individuais.  A [análise](../../azure-monitor/app/analytics.md) é uma ferramenta poderosa para compreender o desempenho e a utilização, e para fins de diagnóstico.

![Exemplo de Análise](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Instalar a aplicação no servidor
Agora publique a aplicação no servidor, permita que as pessoas a utilizem e veja a telemetria a ser mostrada no portal.

* Certifique-se que a firewall permite à aplicação enviar telemetria para estas portas:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Se o tráfego de saída tiver de ser encaminhado através de uma firewall, defina as propriedades do sistema `http.proxyHost` e `http.proxyPort`.

* Nos servidores do Windows, instale:

  * [Microsoft Visual C++ Redistributable](https://www.microsoft.com/download/details.aspx?id=40784)

    (Este componente ativa os contadores de desempenho.)

## <a name="azure-app-service-config-spring-boot"></a>Configuração do serviço de Azure App (Spring boot)

Os aplicativos Spring boot em execução no Windows exigem configuração adicional para serem executados em serviços Azure Apps. Modifique **Web. config** e adicione o seguinte:

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
As exceções não tratadas e as falhas de solicitação são coletadas automaticamente pelo filtro da Web Application Insights.

Para coletar dados sobre outras exceções, você pode [inserir chamadas para trackexception () em seu código][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Monitorizar chamadas de método e dependências externas
[Instale o Agente Java](java-agent.md) para registar métodos internos especificados e as chamadas efetuadas através de JDBC, com dados de temporização.

E para a nomenclatura de operação automática.

## <a name="w3c-distributed-tracing"></a>Rastreamento distribuído W3C

O SDK do Java Application Insights agora dá suporte ao [rastreamento distribuído W3C](https://w3c.github.io/trace-context/).

A configuração do SDK de entrada é explicada com mais detalhes em nosso artigo sobre [correlação](correlation.md#telemetry-correlation-in-the-java-sdk).

A configuração do SDK de saída é definida no arquivo [ai-Agent. xml](java-agent.md) .

## <a name="performance-counters"></a>Contadores de desempenho
Abra **investigar**, **métricas**, para ver um intervalo de contadores de desempenho.

![Captura de tela do painel de métricas com bytes particulares de processo selecionados](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Personalizar a recolha do contador de desempenho
Para desativar a recolha do conjunto padrão de contadores de desempenho, adicione o seguinte código ao nó de raiz do ficheiro ApplicationInsights.xml:

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

* [Adicione telemetria às suas páginas da Web][usage] para monitorar exibições de página e métricas de usuário.
* [Configure testes da Web][availability] para garantir que seu aplicativo permaneça dinâmico e responsivo.

## <a name="send-your-own-telemetry"></a>Enviar a sua própria telemetria
Agora que instalou o SDK, pode utilizar a API para enviar a sua própria telemetria.

* [Acompanhe eventos e métricas personalizados][api] para saber o que os usuários estão fazendo com seu aplicativo.
* [Pesquise eventos e logs][diagnostic] para ajudar a diagnosticar problemas.

## <a name="availability-web-tests"></a>Testes Web de disponibilidade
O Application Insights pode testar o seu site em intervalos regulares para verificar se está a funcionar e a responder bem.

[Saiba mais sobre como configurar testes da Web de disponibilidade.][availability]

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
