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
ms.openlocfilehash: df93405940c02affa224fba2d2e6f07ce5278b15
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755347"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlação de telemetria no Application Insights

No mundo dos microservices, cada operação lógica requer que o trabalho seja feito em vários componentes do serviço. Cada um desses componentes pode ser monitorado separadamente pelo [aplicativo Azure insights](../../azure-monitor/app/app-insights-overview.md). O componente de aplicativo Web se comunica com o componente do provedor de autenticação para validar as credenciais do usuário e com o componente de API para obter dados para visualização. O componente de API pode consultar dados de outros serviços e usar componentes do provedor de cache para notificar o componente de cobrança sobre essa chamada. O Application Insights dá suporte à correlação de telemetria distribuída, que você usa para detectar qual componente é responsável por falhas ou degradação de desempenho.

Este artigo explica o modelo de dados usado pelo Application Insights para correlacionar a telemetria enviada por vários componentes. Ele aborda técnicas e protocolos de propagação de contexto. Ele também aborda a implementação de conceitos de correlação em diferentes linguagens e plataformas.

## <a name="data-model-for-telemetry-correlation"></a>Modelo de dados para correlação de telemetria

Application Insights define um [modelo de dados](../../azure-monitor/app/data-model.md) para correlação de telemetria distribuída. Para associar a telemetria à operação lógica, cada item de telemetria tem um campo de contexto chamado `operation_Id`. Esse identificador é compartilhado por cada item de telemetria no rastreamento distribuído. Portanto, mesmo com a perda de telemetria de uma única camada, você ainda pode associar a telemetria relatada por outros componentes.

Uma operação lógica distribuída normalmente consiste em um conjunto de operações menores, que são solicitações processadas por um dos componentes. Essas operações são definidas pela [telemetria de solicitação](../../azure-monitor/app/data-model-request-telemetry.md). Cada telemetria de solicitação tem seu próprio `id` que a identifica de forma exclusiva e global. E todos os itens de telemetria (como rastreamentos e exceções) associados a essa solicitação devem definir o `operation_parentId` como o valor da `id` de solicitação.

Cada operação de saída, como uma chamada HTTP para outro componente, é representada pela [telemetria de dependência](../../azure-monitor/app/data-model-dependency-telemetry.md). A telemetria de dependência também define seu próprio `id` que é globalmente exclusivo. A telemetria de solicitação, iniciada por essa chamada de dependência, usa esse `id` como seu `operation_parentId`.

Você pode criar uma exibição da operação lógica distribuída usando `operation_Id`, `operation_parentId` e `request.id` com `dependency.id`. Esses campos também definem a ordem causalidade das chamadas de telemetria.

Em um ambiente de microserviços, os rastreamentos de componentes podem ir para itens de armazenamento diferentes. Cada componente pode ter sua própria chave de instrumentação em Application Insights. Para obter a telemetria para a operação lógica, o Application Insights UX consulta dados de cada item de armazenamento. Quando o número de itens de armazenamento for enorme, você precisará de uma dica sobre onde procurar em seguida. O modelo de dados Application Insights define dois campos para resolver esse problema: `request.source` e `dependency.target`. O primeiro campo identifica o componente que iniciou a solicitação de dependência e o segundo identifica qual componente retornou a resposta da chamada de dependência.

## <a name="example"></a>Exemplo

Vamos pegar um exemplo de um aplicativo chamado preços de ações, que mostra o preço de mercado atual de um estoque usando uma API externa chamada `Stock`. O aplicativo de preços de ações tem uma página chamada `Stock page` que o navegador da Web do cliente abre usando `GET /Home/Stock`. O aplicativo consulta a API de `Stock` usando uma chamada HTTP `GET /api/stock/value`.

Você pode analisar a telemetria resultante executando uma consulta:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Nos resultados, observe que todos os itens de telemetria compartilham o `operation_Id` raiz. Quando uma chamada AJAX é feita da página, uma nova ID exclusiva (`qJSXU`) é atribuída à telemetria de dependência e a ID de pageView é usada como `operation_ParentId`. Em seguida, a solicitação do servidor usa a ID AJAX como `operation_ParentId`.

| ItemType   | nome                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Página de ações                |              | STYz               | STYz         |
| Estados | OBTER/Home/Stock           | qJSXU        | STYz               | STYz         |
| Quest    | OBTER página inicial/estoque            | KqKwlrSt9PA = | qJSXU              | STYz         |
| Estados | OBTER/API/Stock/Value      | bBrf2L7mm2g = | KqKwlrSt9PA =       | STYz         |

Quando a chamada `GET /api/stock/value` é feita a um serviço externo, você deseja saber a identidade desse servidor para poder definir o campo `dependency.target` adequadamente. Quando o serviço externo não dá suporte ao monitoramento, `target` é definido como o nome de host do serviço (por exemplo, `stock-prices-api.com`). No entanto, se o serviço se identificar retornando um cabeçalho HTTP predefinido, `target` contém a identidade do serviço que permite Application Insights criar um rastreamento distribuído consultando a telemetria desse serviço.

## <a name="correlation-headers"></a>Cabeçalhos de correlação

Estamos fazendo a transição para o [contexto de rastreamento W3C](https://w3c.github.io/trace-context/) que define:

- `traceparent`: transporta a ID da operação globalmente exclusiva e o identificador exclusivo da chamada.
- `tracestate`: transporta o contexto específico do sistema.

As versões mais recentes do Application Insights SDKs dão suporte ao protocolo Trace-Context, mas talvez seja necessário aceitar isso (ela manterá a compatibilidade com versões anteriores com o protocolo de correlação antigo com suporte dos SDKs do ApplicationInsights).

O [protocolo http de correlação aka Request-ID](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) está no caminho de substituição. Esse protocolo define dois cabeçalhos:

- `Request-Id`: transporta a ID globalmente exclusiva da chamada.
- `Correlation-Context`: transporta a coleção de pares nome-valor das propriedades de rastreamento distribuído.

Application Insights também define a [extensão](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) para o protocolo http de correlação. Ele usa `Request-Context` pares nome-valor para propagar a coleção de propriedades usadas pelo chamador ou receptor imediato. O SDK do Application Insights usa esse cabeçalho para definir `dependency.target` e `request.source` campos.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Habilitar o suporte de rastreamento distribuído W3C para aplicativos ASP.NET clássicos
 
  > [!NOTE]
  > Nenhuma configuração necessária a partir de `Microsoft.ApplicationInsights.Web` e `Microsoft.ApplicationInsights.DependencyCollector` 

O suporte ao contexto de rastreamento W3C é feito na maneira compatível com versões anteriores, e a correlação deve funcionar com aplicativos que são instrumentados com versões anteriores do SDK (sem suporte do W3C). 

Se, por qualquer motivo, você quiser continuar usando o protocolo `Request-Id` herdado, você poderá *desabilitar* o contexto de rastreamento com a seguinte configuração

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Se você executar uma versão mais antiga do SDK, é recomendável atualizá-la ou aplicar a configuração a seguir para habilitar o Trace-Context.
Esse recurso está disponível em pacotes `Microsoft.ApplicationInsights.Web` e `Microsoft.ApplicationInsights.DependencyCollector` começando com a versão 2.8.0-beta1.
Ele é desabilitado por padrão. Para habilitá-lo, altere `ApplicationInsights.config`:

- Em `RequestTrackingTelemetryModule`, adicione o elemento `EnableW3CHeadersExtraction` com o valor definido como `true`.
- Em `DependencyTrackingTelemetryModule`, adicione o elemento `EnableW3CHeadersInjection` com o valor definido como `true`.
- Adicione `W3COperationCorrelationTelemetryInitializer` sob o `TelemetryInitializers` semelhante a 

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers> 
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Habilitar o suporte ao rastreamento distribuído W3C para aplicativos ASP.NET Core

 > [!NOTE]
  > Nenhuma configuração necessária a partir do `Microsoft.ApplicationInsights.AspNetCore` versão 2.8.0.
 
O suporte ao contexto de rastreamento W3C é feito na maneira compatível com versões anteriores, e a correlação deve funcionar com aplicativos que são instrumentados com versões anteriores do SDK (sem suporte do W3C). 

Se, por qualquer motivo, você quiser continuar usando o protocolo `Request-Id` herdado, você poderá *desabilitar* o contexto de rastreamento com a seguinte configuração

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Se você executar uma versão mais antiga do SDK, é recomendável atualizá-la ou aplicar a configuração a seguir para habilitar o Trace-Context.

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

  - Para aplicativos Java EE, adicione o seguinte à marcação de `<TelemetryModules>` dentro de ApplicationInsights. xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - Para aplicativos Spring boot, adicione as seguintes propriedades:

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
> Certifique-se de que as configurações de entrada e saída sejam exatamente as mesmas.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Habilitar o suporte ao rastreamento distribuído W3C para aplicativos Web

Este recurso está em `Microsoft.ApplicationInsights.JavaScript`. Ele é desabilitado por padrão. Para habilitá-lo, use `distributedTracingMode` config. O AI_AND_W3C é fornecido para compatibilidade com os serviços instrumentados Application Insightss herdados:

- **Configuração do NPM (ignorar se estiver usando a configuração de trecho de código)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...Other Configuration Options... */
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
      /* ...Other Configuration Options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing e Application Insights

A [especificação do modelo de dados OpenTracing](https://opentracing.io/) e o Application insights modelos de dados são mapeados da seguinte maneira:

| Estatísticas das Aplicações                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` com `span.kind = server`                  |
| `Dependency`                          | `Span` com `span.kind = client`                  |
| `Id` de `Request` e `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` do tipo `ChildOf` (a extensão pai)   |

Para obter mais informações, consulte o [modelo de dados de telemetria Application insights](../../azure-monitor/app/data-model.md). 

Para obter definições de conceitos de OpenTracing, consulte a [especificação](https://github.com/opentracing/specification/blob/master/specification.md) OpenTracing e [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>Correlação de telemetria no OpenCensus Python

OpenCensus Python segue as especificações de modelo de dados `OpenTracing` descritas acima. Ele também dá suporte ao [contexto de rastreamento W3C](https://w3c.github.io/trace-context/) sem a necessidade de qualquer configuração.

### <a name="incoming-request-correlation"></a>Correlação de solicitação de entrada

OpenCensus Python correlaciona cabeçalhos de contexto de rastreamento W3C de solicitações de entrada para as extensões que são geradas a partir das próprias solicitações. O OpenCensus fará isso automaticamente com integrações para estruturas de aplicativos Web populares, como `flask`, `django` e `pyramid`. Os cabeçalhos de contexto de rastreamento do W3C simplesmente precisam ser preenchidos com o [formato correto](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)e enviados com a solicitação. Veja abaixo um exemplo `flask` aplicativo que demonstra isso.

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

Isso executa um aplicativo de `flask` de exemplo em seu computador local, ouvindo a porta `8080`. Para correlacionar o contexto de rastreamento, enviamos uma solicitação para o ponto de extremidade. Neste exemplo, podemos usar um comando `curl`.
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Observando o [formato de cabeçalho de contexto de rastreamento](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), podemos derivar as seguintes informações: `version`: `00` 
 `trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736` 
 `parent-id/span-id`: `00f067aa0ba902b7` 
 0: 1

Se olharmos a entrada de solicitação que foi enviada para Azure Monitor, podemos ver os campos populados com as informações de cabeçalho de rastreamento.

![Captura de tela da telemetria de solicitação em logs (análise) com campos de cabeçalho de rastreamento realçados em caixa vermelha](./media/opencensus-python/0011-correlation.png)

O campo `id` está no formato `<trace-id>.<span-id>`, em que o `trace-id` é obtido do cabeçalho de rastreamento que foi passado na solicitação e o `span-id` é uma matriz de 8 bytes gerada para esse intervalo. 

O campo `operation_ParentId` está no formato `<trace-id>.<parent-id>`, onde os `trace-id` e `parent-id` são extraídos do cabeçalho de rastreamento que foi passado na solicitação.

### <a name="logs-correlation"></a>Correlação de logs

O OpenCensus Python permite a correlação de logs ao enriquecer registros de log com ID de rastreamento, ID de extensão e sinalizador de amostragem. Isso é feito com a instalação da [integração de log](https://pypi.org/project/opencensus-ext-logging/)do OpenCensus. Os seguintes atributos serão adicionados ao `LogRecord`s do Python: `traceId`, `spanId` e `traceSampled`. Observe que isso só entra em vigor para os agentes criados após a integração.
Veja abaixo um exemplo de aplicativo que demonstra isso.

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
Quando esse código é executado, obtemos o seguinte no console:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Observe como há um spanid presente para a mensagem de log que está dentro do span, que é o mesmo spanid que pertence ao trecho nomeado `hello`.

## <a name="telemetry-correlation-in-net"></a>Correlação de telemetria no .NET

Ao longo do tempo, o .NET definiu várias maneiras de correlacionar os logs de telemetria e diagnóstico:

- `System.Diagnostics.CorrelationManager` permite o acompanhamento de [LogicalOperationStack e ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- o `System.Diagnostics.Tracing.EventSource` e o ETW (rastreamento de eventos para Windows) definem o método [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) .
- `ILogger` usa [escopos de log](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) e HTTP conectam a propagação de contexto "atual".

No entanto, esses métodos não habilitam o suporte automático a rastreamento distribuído. `DiagnosticSource` é uma maneira de dar suporte à correlação automática entre computadores. As bibliotecas .NET dão suporte a ' Diagnosticname ' e permitem a propagação automática entre computadores do contexto de correlação por meio do transporte, como HTTP.

O [guia para atividades](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) no `DiagnosticSource` explica as noções básicas de acompanhamento de atividades.

ASP.NET Core 2,0 dá suporte à extração de cabeçalhos HTTP e à inicialização de uma nova atividade.

`System.Net.Http.HttpClient`, começando com a versão 4.1.0, dá suporte à injeção automática dos cabeçalhos HTTP de correlação e ao acompanhamento da chamada HTTP como uma atividade.

Há um novo módulo HTTP, [Microsoft. AspNet. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), para ASP.net clássicas. Esse módulo implementa a correlação de telemetria usando `DiagnosticSource`. Ele inicia uma atividade com base nos cabeçalhos de solicitação de entrada. Ele também correlaciona a telemetria de diferentes estágios de processamento de solicitação, mesmo para casos em que cada estágio do processamento de Serviços de Informações da Internet (IIS) é executado em um thread gerenciado diferente.

O SDK do Application Insights, começando com a versão 2.4.0-beta1, usa `DiagnosticSource` e `Activity` para coletar telemetria e associá-la à atividade atual.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Correlação de telemetria no SDK do Java

O [SDK do Application insights para Java](../../azure-monitor/app/java-get-started.md) dá suporte à correlação automática de telemetria, começando com a versão 2.0.0. Ele preenche automaticamente `operation_id` para toda a telemetria (como rastreamentos, exceções e eventos personalizados) emitida dentro do escopo de uma solicitação. Ele também cuida da propagação dos cabeçalhos de correlação (descritos anteriormente) para chamadas de serviço a serviço via HTTP, se o agente do [SDK do Java](../../azure-monitor/app/java-agent.md) estiver configurado.

> [!NOTE]
> Somente as chamadas feitas por meio do Apache HTTPClient têm suporte para o recurso de correlação. Se você estiver usando o Spring Resttemplate ou feign, ambos poderão ser usados com o Apache HTTPClient nos bastidores.

Atualmente, a propagação automática de contexto em tecnologias de mensagens (como Kafka, RabbitMQ ou barramento de serviço do Azure) não tem suporte. No entanto, é possível codificar esses cenários manualmente usando as APIs `trackDependency` e `trackRequest`. Nessas APIs, uma telemetria de dependência representa uma mensagem sendo enfileirada por um produtor e a solicitação representa uma mensagem sendo processada por um consumidor. Nesse caso, tanto `operation_id` quanto `operation_parentId` devem ser propagadas nas propriedades da mensagem.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Correlação de telemetria no aplicativo Java assíncrono

Para correlacionar a telemetria no aplicativo de Spring boot assíncrono, siga [este](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) artigo detalhado. Ele fornece diretrizes para instrumentação de [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) da mola, bem como [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html). 


<a name="java-role-name"></a>
## <a name="role-name"></a>Nome da função

Às vezes, talvez você queira personalizar a maneira como os nomes de componentes são exibidos no [mapa do aplicativo](../../azure-monitor/app/app-map.md). Para fazer isso, você pode definir manualmente o `cloud_RoleName` seguindo um destes procedimentos:

- Se você usar o Spring boot com o iniciador do Spring boot Application Insights, a única alteração necessária será definir seu nome personalizado para o aplicativo no arquivo Application. Properties.

  `spring.application.name=<name-of-app>`

  O iniciador do Spring boot atribui automaticamente `cloudRoleName` ao valor inserido para a propriedade `spring.application.name`.

- Se você estiver usando o `WebRequestTrackingFilter`, o `WebAppNameContextInitializer` definirá o nome do aplicativo automaticamente. Adicione o seguinte ao seu arquivo de configuração (ApplicationInsights. xml):

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- Se você usar a classe de contexto de nuvem:

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>Passos seguintes

- Gravar [telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md).
- Para cenários de correlação avançada em ASP.NET Core e ASP.NET, consulte o artigo [rastrear operações personalizadas](custom-operations-tracking.md) .
- Saiba mais sobre como [definir cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) para outros SDKs.
- Integre todos os componentes do seu microserviço em Application Insights. Confira as [plataformas com suporte](../../azure-monitor/app/platforms.md).
- Consulte o [modelo de dados](../../azure-monitor/app/data-model.md) para tipos de Application insights.
- Saiba como [estender e filtrar a telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- Examine a [referência de configuração de Application insights](configuration-with-applicationinsights-config.md).
