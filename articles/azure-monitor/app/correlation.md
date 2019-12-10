---
title: Correlação de telemetria do Aplicativo Azure insights | Microsoft Docs
description: Correlação de telemetria Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: bc73dfb1c4dc77abe0bd135ecf572fa05ddf6322
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951331"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlação de telemetria no Application Insights

No mundo dos microservices, cada operação lógica requer que o trabalho seja feito em vários componentes do serviço. Você pode monitorar cada um desses componentes separadamente usando [Application insights](../../azure-monitor/app/app-insights-overview.md). O Application Insights dá suporte à correlação de telemetria distribuída, que você usa para detectar qual componente é responsável por falhas ou degradação de desempenho.

Este artigo explica o modelo de dados usado pelo Application Insights para correlacionar a telemetria enviada por vários componentes. Ele aborda técnicas e protocolos de propagação de contexto. Ele também aborda a implementação de táticas de correlação em diferentes linguagens e plataformas.

## <a name="data-model-for-telemetry-correlation"></a>Modelo de dados para correlação de telemetria

Application Insights define um [modelo de dados](../../azure-monitor/app/data-model.md) para correlação de telemetria distribuída. Para associar a telemetria a uma operação lógica, cada item de telemetria tem um campo de contexto chamado `operation_Id`. Esse identificador é compartilhado por cada item de telemetria no rastreamento distribuído. Portanto, mesmo se você perder a telemetria de uma única camada, ainda poderá associar a telemetria relatada por outros componentes.

Uma operação lógica distribuída normalmente consiste em um conjunto de operações menores que são solicitações processadas por um dos componentes. Essas operações são definidas pela [telemetria de solicitação](../../azure-monitor/app/data-model-request-telemetry.md). Cada item de telemetria de solicitação tem seu próprio `id` que o identifica de forma exclusiva e global. E todos os itens de telemetria (como rastreamentos e exceções) associados à solicitação devem definir o `operation_parentId` como o valor da `id`de solicitação.

Cada operação de saída, como uma chamada HTTP para outro componente, é representada pela [telemetria de dependência](../../azure-monitor/app/data-model-dependency-telemetry.md). A telemetria de dependência também define seu próprio `id` que é globalmente exclusivo. A telemetria de solicitação, iniciada por essa chamada de dependência, usa esse `id` como seu `operation_parentId`.

Você pode criar uma exibição da operação lógica distribuída usando `operation_Id`, `operation_parentId`e `request.id` com `dependency.id`. Esses campos também definem a ordem causalidade das chamadas de telemetria.

Em um ambiente de microserviços, os rastreamentos de componentes podem ir para itens de armazenamento diferentes. Cada componente pode ter sua própria chave de instrumentação em Application Insights. Para obter a telemetria para a operação lógica, o Application Insights consulta dados de cada item de armazenamento. Quando o número de itens de armazenamento for grande, você precisará de uma dica sobre onde procurar em seguida. O modelo de dados Application Insights define dois campos para resolver esse problema: `request.source` e `dependency.target`. O primeiro campo identifica o componente que iniciou a solicitação de dependência. O segundo campo identifica qual componente retornou a resposta da chamada de dependência.

## <a name="example"></a>Exemplo

Vamos examinar um exemplo. Um aplicativo chamado preços de ações mostra o preço atual do mercado de um estoque usando uma API externa chamada stock. O aplicativo de preços de ações tem uma página chamada Stock Page que o navegador da Web do cliente abre usando `GET /Home/Stock`. O aplicativo consulta a API de estoque usando a chamada HTTP `GET /api/stock/value`.

Você pode analisar a telemetria resultante executando uma consulta:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Nos resultados, observe que todos os itens de telemetria compartilham o `operation_Id`raiz. Quando uma chamada AJAX é feita da página, uma nova ID exclusiva (`qJSXU`) é atribuída à telemetria de dependência e a ID do pageView é usada como `operation_ParentId`. Em seguida, a solicitação do servidor usa a ID AJAX como `operation_ParentId`.

| itemType   | nome                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Página de ações                |              | STYz               | STYz         |
| Estados | OBTER/Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | OBTER página inicial/estoque            | KqKwlrSt9PA= | qJSXU              | STYz         |
| Estados | OBTER/API/Stock/Value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Quando a chamada `GET /api/stock/value` é feita a um serviço externo, você precisa saber a identidade desse servidor para poder definir o campo `dependency.target` adequadamente. Quando o serviço externo não dá suporte ao monitoramento, `target` é definido como o nome de host do serviço (por exemplo, `stock-prices-api.com`). Mas se o serviço se identificar retornando um cabeçalho HTTP predefinido, `target` contém a identidade do serviço que permite Application Insights criar um rastreamento distribuído consultando a telemetria desse serviço.

## <a name="correlation-headers"></a>Cabeçalhos de correlação

Application Insights está fazendo a transição para o [contexto de rastreamento W3C](https://w3c.github.io/trace-context/), que define:

- `traceparent`: transporta a ID da operação globalmente exclusiva e o identificador exclusivo da chamada.
- `tracestate`: transporta o contexto de rastreamento específico do sistema.

A versão mais recente do SDK do Application Insights dá suporte ao protocolo Trace-Context, mas talvez seja necessário aceitá-lo. (A compatibilidade com versões anteriores com o protocolo de correlação anterior com suporte pelo SDK do Application Insights será mantida.)

O [protocolo http de correlação, também chamado Request-ID](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), está sendo preterido. Esse protocolo define dois cabeçalhos:

- `Request-Id`: transporta a ID globalmente exclusiva da chamada.
- `Correlation-Context`: transporta a coleção de pares nome-valor das propriedades de rastreamento distribuído.

Application Insights também define a [extensão](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) para o protocolo http de correlação. Ele usa `Request-Context` pares nome-valor para propagar a coleção de propriedades usadas pelo chamador ou receptor imediato. O SDK do Application Insights usa esse cabeçalho para definir os campos de `dependency.target` e `request.source`.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Habilitar o suporte de rastreamento distribuído W3C para aplicativos ASP.NET clássicos
 
  > [!NOTE]
  >  Começando com `Microsoft.ApplicationInsights.Web` e `Microsoft.ApplicationInsights.DependencyCollector`, nenhuma configuração é necessária.

O suporte ao contexto de rastreamento W3C é implementado de forma compatível com versões anteriores. Espera-se que a correlação funcione com aplicativos que são instrumentados com versões anteriores do SDK (sem suporte W3C).

Se desejar continuar usando o protocolo `Request-Id` herdado, você poderá desabilitar o rastreamento de contexto usando essa configuração:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Se você executar uma versão mais antiga do SDK, recomendamos atualizá-la ou aplicar a configuração a seguir para habilitar o Trace-Context.
Esse recurso está disponível nos pacotes `Microsoft.ApplicationInsights.Web` e `Microsoft.ApplicationInsights.DependencyCollector`, começando com a versão 2.8.0-beta1.
Ele é desabilitado por padrão. Para habilitá-lo, faça essas alterações `ApplicationInsights.config`:

- Em `RequestTrackingTelemetryModule`, adicione o elemento `EnableW3CHeadersExtraction` e defina seu valor como `true`.
- Em `DependencyTrackingTelemetryModule`, adicione o elemento `EnableW3CHeadersInjection` e defina seu valor como `true`.
- Adicione `W3COperationCorrelationTelemetryInitializer` em `TelemetryInitializers`. Ele será semelhante a este exemplo:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Habilitar o suporte ao rastreamento distribuído W3C para aplicativos ASP.NET Core

 > [!NOTE]
  > A partir do `Microsoft.ApplicationInsights.AspNetCore` versão 2.8.0, nenhuma configuração é necessária.
 
O suporte ao contexto de rastreamento W3C é implementado de forma compatível com versões anteriores. Espera-se que a correlação funcione com aplicativos que são instrumentados com versões anteriores do SDK (sem suporte W3C).

Se desejar continuar usando o protocolo `Request-Id` herdado, você poderá desabilitar o rastreamento de contexto usando essa configuração:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Se você executar uma versão mais antiga do SDK, recomendamos atualizá-la ou aplicar a configuração a seguir para habilitar o Trace-Context.

Esse recurso está em `Microsoft.ApplicationInsights.AspNetCore` versão 2.5.0-beta1 e na versão `Microsoft.ApplicationInsights.DependencyCollector` 2.8.0-beta1.
Ele é desabilitado por padrão. Para habilitá-lo, defina `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` como `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Habilitar o suporte ao rastreamento distribuído W3C para aplicativos Java

- **Configuração de entrada**

  - Para aplicativos Java EE, adicione o seguinte à marca de `<TelemetryModules>` em ApplicationInsights. xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - Para aplicativos Spring boot, adicione estas propriedades:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Configuração de saída**

  Adicione o seguinte ao AI-Agent. xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > O modo de compatibilidade com versões anteriores é habilitado por padrão e o parâmetro `enableW3CBackCompat` é opcional. Use-o somente quando quiser desativar a compatibilidade com versões anteriores.
  >
  > O ideal é que você desative essa opção quando todos os seus serviços tiverem sido atualizados para versões mais recentes de SDKs que dão suporte ao protocolo W3C. É altamente recomendável que você mova para esses SDKs mais recentes assim que possível.

> [!IMPORTANT]
> Verifique se as configurações de entrada e saída são exatamente iguais.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Habilitar o suporte ao rastreamento distribuído W3C para aplicativos Web

Este recurso está em `Microsoft.ApplicationInsights.JavaScript`. Ele é desabilitado por padrão. Para habilitá-lo, use `distributedTracingMode` config. AI_AND_W3C é fornecida para compatibilidade com versões anteriores com qualquer serviço herdado instrumentado por Application Insights.

- **configuração do NPM (ignorar se estiver usando a configuração de trecho de código)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Configuração de trecho de código (ignorar se estiver usando a instalação do NPM)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing e Application Insights

A [especificação do modelo de dados OpenTracing](https://opentracing.io/) e o Application insights modelos de dados são mapeados da seguinte maneira:

| Estatísticas das Aplicações                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` com o `span.kind = server`                  |
| `Dependency`                          | `Span` com o `span.kind = client`                  |
| `Id` de `Request` e `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` do tipo `ChildOf` (a extensão pai)   |

Para obter mais informações, consulte [Application insights modelo de dados de telemetria](../../azure-monitor/app/data-model.md).

Para obter definições de conceitos de OpenTracing, consulte a [especificação](https://github.com/opentracing/specification/blob/master/specification.md) de OpenTracing e as [convenções semânticas](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>Correlação de telemetria no OpenCensus Python

OpenCensus Python segue as especificações de modelo de dados `OpenTracing` descritas anteriormente. Ele também dá suporte ao [contexto de rastreamento W3C](https://w3c.github.io/trace-context/) sem a necessidade de nenhuma configuração.

### <a name="incoming-request-correlation"></a>Correlação de solicitação de entrada

OpenCensus Python correlaciona cabeçalhos de contexto de rastreamento W3C de solicitações de entrada para as extensões que são geradas a partir das solicitações. O OpenCensus fará isso automaticamente com integrações para essas estruturas de aplicativos Web populares: Flask, Django e pirâmide. Você só precisa popular os cabeçalhos de contexto de rastreamento do W3C com o [formato correto](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) e enviá-los com a solicitação. Veja um exemplo de aplicativo Flask que demonstra isso:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Esse código executa um aplicativo Flask de exemplo em seu computador local, ouvindo a porta `8080`. Para correlacionar o contexto de rastreamento, você envia uma solicitação ao ponto de extremidade. Neste exemplo, você pode usar um comando `curl`:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Ao examinar o [formato de cabeçalho do contexto de rastreamento](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), você pode derivar as seguintes informações:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Se você examinar a entrada de solicitação que foi enviada para Azure Monitor, poderá ver os campos preenchidos com as informações do cabeçalho de rastreamento. Você pode encontrar esses dados em logs (análise) no recurso de Application Insights de Azure Monitor.

![Solicitar telemetria em logs (análise)](./media/opencensus-python/0011-correlation.png)

O campo `id` está no formato `<trace-id>.<span-id>`, em que o `trace-id` é obtido do cabeçalho de rastreamento que foi passado na solicitação e o `span-id` é uma matriz de 8 bytes gerada para esse intervalo.

O campo `operation_ParentId` está no formato `<trace-id>.<parent-id>`, onde os `trace-id` e `parent-id` são obtidos do cabeçalho de rastreamento que foi passado na solicitação.

### <a name="log-correlation"></a>Correlação de registos

O OpenCensus Python permite que você correlacione os logs adicionando uma ID de rastreamento, uma ID de extensão e um sinalizador de amostragem aos registros de log. Você adiciona esses atributos instalando a [integração de log](https://pypi.org/project/opencensus-ext-logging/)do OpenCensus. Os seguintes atributos serão adicionados aos objetos de `LogRecord` do Python: `traceId`, `spanId`e `traceSampled`. Observe que isso só entra em vigor para agentes que são criados após a integração.

Aqui está um aplicativo de exemplo que demonstra isso:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Quando esse código é executado, as seguintes impressões são impressas no console do:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Observe que há um `spanId` presente para a mensagem de log que está dentro do intervalo. Esse é o mesmo `spanId` que pertence ao trecho nomeado `hello`.

Você pode exportar os dados de log usando `AzureLogHandler`. Para obter mais informações, consulte [este artigo](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs).

## <a name="telemetry-correlation-in-net"></a>Correlação de telemetria no .NET

Ao longo do tempo, o .NET definiu várias maneiras de correlacionar os logs de telemetria e de diagnóstico:

- `System.Diagnostics.CorrelationManager` permite o acompanhamento de [LogicalOperationStack e ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx).
- o `System.Diagnostics.Tracing.EventSource` e o ETW (rastreamento de eventos para Windows) definem o método [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) .
- `ILogger` usa [escopos de log](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes).
- Windows Communication Foundation (WCF) e HTTP conectam a propagação de contexto "atual".

Mas esses métodos não habilitam o suporte automático a rastreamento distribuído. o `DiagnosticSource` dá suporte à correlação automática entre computadores. As bibliotecas do .NET oferecem suporte a `DiagnosticSource` e permitem a propagação automática entre computadores do contexto de correlação por meio do transporte, como HTTP.

O [Guia do usuário da atividade](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) no `DiagnosticSource` explica os conceitos básicos do acompanhamento de atividades.

ASP.NET Core 2,0 dá suporte à extração de cabeçalhos HTTP e à inicialização de novas atividades.

`System.Net.Http.HttpClient`, começando com a versão 4.1.0, dá suporte à injeção automática de cabeçalhos HTTP de correlação e acompanhamento de chamadas HTTP como atividades.

Há um novo módulo HTTP, [Microsoft. AspNet. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), para ASP.net clássicas. Esse módulo implementa a correlação de telemetria usando `DiagnosticSource`. Ele inicia uma atividade com base nos cabeçalhos de solicitação de entrada. Ele também correlaciona a telemetria de diferentes estágios de processamento de solicitação, mesmo quando cada estágio do processamento de Serviços de Informações da Internet (IIS) é executado em um thread gerenciado diferente.

O SDK do Application Insights, começando com a versão 2.4.0-beta1, usa `DiagnosticSource` e `Activity` para coletar telemetria e associá-la à atividade atual.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Correlação de telemetria no SDK do Java

[Application insights SDK para Java](../../azure-monitor/app/java-get-started.md) versão 2.0.0 ou posterior dá suporte à correlação automática de telemetria. Ele preenche automaticamente `operation_id` para toda a telemetria (como rastreamentos, exceções e eventos personalizados) emitida dentro do escopo de uma solicitação. Ele também propaga os cabeçalhos de correlação (descritos anteriormente) para chamadas de serviço a serviço via HTTP, se o [agente do SDK do Java](../../azure-monitor/app/java-agent.md) estiver configurado.

> [!NOTE]
> Somente as chamadas feitas por meio do Apache HttpClient têm suporte para o recurso de correlação. Tanto o Spring Resttemplate quanto o feign podem ser usados com o Apache HttpClient nos bastidores.

Atualmente, a propagação automática de contexto em tecnologias de mensagens (como Kafka, RabbitMQ e barramento de serviço do Azure) não tem suporte. É possível codificar esses cenários manualmente usando os métodos `trackDependency` e `trackRequest`. Nesses métodos, uma telemetria de dependência representa uma mensagem sendo enfileirada por um produtor. A solicitação representa uma mensagem sendo processada por um consumidor. Nesse caso, tanto `operation_id` quanto `operation_parentId` devem ser propagadas nas propriedades da mensagem.

### <a name="telemetry-correlation-in-asynchronous-java-applications"></a>Correlação de telemetria em aplicativos Java assíncronos

Para saber como correlacionar a telemetria em um aplicativo de Spring boot assíncrono, consulte [rastreamento distribuído em aplicativos Java assíncronos](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications). Este artigo fornece diretrizes para instrumentação de [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) e [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html)da Spring.


<a name="java-role-name"></a>
## <a name="role-name"></a>Nome da função

Talvez você queira personalizar a maneira como os nomes de componentes são exibidos no [mapa do aplicativo](../../azure-monitor/app/app-map.md). Para fazer isso, você pode definir manualmente o `cloud_RoleName` executando uma das seguintes ações:

- Com Application Insights SDK do Java 2.5.0 e posterior, você pode especificar o `cloud_RoleName` adicionando `<RoleName>` ao arquivo ApplicationInsights. xml:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Se você usar o Spring boot com o iniciador do Spring boot Application Insights, precisará apenas definir seu nome personalizado para o aplicativo no arquivo Application. Properties:

  `spring.application.name=<name-of-app>`

  O iniciador do Spring boot atribui automaticamente `cloudRoleName` ao valor inserido para a propriedade `spring.application.name`.

## <a name="next-steps"></a>Passos seguintes

- Gravar [telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md).
- Para cenários de correlação avançada em ASP.NET Core e ASP.NET, consulte [rastrear operações personalizadas](custom-operations-tracking.md).
- Saiba mais sobre como [configurar cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) para outros SDKs.
- Integre todos os componentes do seu microserviço em Application Insights. Confira as [plataformas com suporte](../../azure-monitor/app/platforms.md).
- Consulte o [modelo de dados](../../azure-monitor/app/data-model.md) para tipos de Application insights.
- Saiba como [estender e filtrar a telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- Examine a [referência de configuração de Application insights](configuration-with-applicationinsights-config.md).
