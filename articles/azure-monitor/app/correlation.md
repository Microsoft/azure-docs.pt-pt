---
title: Correlação de telemetria Azure Application Insights Microsoft Docs
description: Visão de telemetria correlação
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 06897fffda490cdfcbb2a9cf6f55c7945e8afda0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367663"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlação de telemetria em Insights de Aplicação

No mundo dos microserviços, todas as operações lógicas exigem que o trabalho seja feito em vários componentes do serviço. Pode monitorizar cada um destes componentes separadamente utilizando insights de [aplicação](../../azure-monitor/app/app-insights-overview.md). Application Insights suporta correlação de telemetria distribuída, que utiliza para detetar qual o componente responsável por falhas ou degradação do desempenho.

Este artigo explica o modelo de dados utilizado pela Application Insights para correlacionar a telemetria enviada por vários componentes. Abrange técnicas e protocolos de propagação de contextos. Abrange também a implementação de táticas de correlação em diferentes línguas e plataformas.

## <a name="data-model-for-telemetry-correlation"></a>Modelo de dados para correlação de telemetria

Os Insights de Aplicação definem um modelo de [dados](../../azure-monitor/app/data-model.md) para a correlação de telemetria distribuída. Para associar a telemetria a uma operação lógica, cada item de telemetria tem um campo de contexto chamado `operation_Id`. Este identificador é partilhado por todos os artigos de telemetria no vestígio distribuído. Assim, mesmo que perca a telemetria de uma única camada, ainda pode associar a telemetria reportada por outros componentes.

Uma operação lógica distribuída consiste tipicamente num conjunto de operações menores que são pedidos processados por um dos componentes. Estas operações são definidas por [telemetria de pedido.](../../azure-monitor/app/data-model-request-telemetry.md) Cada artigo de telemetria de pedido tem o seu próprio `id` que o identifica de forma única e global. E todos os artigos de telemetria (tais como vestígios e exceções) associados ao pedido devem definir o `operation_parentId` ao valor do pedido `id`.

Todas as operações de saída, como uma chamada http para outro componente, são representadas pela [telemetria da dependência.](../../azure-monitor/app/data-model-dependency-telemetry.md) A telemetria da dependência também define o seu próprio `id` que é globalmente único. A telemetria de pedidos, iniciada por esta chamada de dependência, utiliza este `id` como `operation_parentId`.

Pode construir uma visão da operação lógica distribuída utilizando `operation_Id`, `operation_parentId`e `request.id` com `dependency.id`. Estes campos também definem a ordem de causalidade das chamadas de telemetria.

Num ambiente de microserviços, os vestígios de componentes podem ir para diferentes itens de armazenamento. Cada componente pode ter a sua própria chave de instrumentação em Insights de Aplicação. Para obter telemetria para a operação lógica, application Insights consulta dados de todos os itens de armazenamento. Quando o número de artigos de armazenamento for grande, você precisará de uma pista sobre onde procurar a seguir. O modelo de dados Application Insights define dois campos para resolver este problema: `request.source` e `dependency.target`. O primeiro campo identifica o componente que iniciou o pedido de dependência. O segundo campo identifica qual o componente que devolveu a resposta da chamada de dependência.

## <a name="example"></a>Exemplo

Vejamos um exemplo. Uma aplicação chamada Stock Prices mostra o preço de mercado atual de uma ação utilizando uma API externa chamada Stock. A aplicação Stock Prices tem uma página chamada Página de Stock que o navegador web do cliente abre usando `GET /Home/Stock`. A aplicação consulta a Stock API utilizando a chamada HTTP `GET /api/stock/value`.

Pode analisar a telemetria resultante executando uma consulta:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Nos resultados, note que todos os artigos de telemetria partilham a raiz `operation_Id`. Quando uma chamada do Ajax é feita a partir da página, um novo ID único (`qJSXU`) é atribuído à telemetria de dependência, e o ID da páginaView é usado como `operation_ParentId`. O pedido do servidor utiliza então o ID do Ajax como `operation_ParentId`.

| ItemType   | nome                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| páginaVer   | Página de stock                |              | Rio STYz               | Rio STYz         |
| dependência | GET /Casa/Stock           | qJSXU        | Rio STYz               | Rio STYz         |
| request    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | Rio STYz         |
| dependência | GET /api/stock/valor      | bBrf2L7mm2g= | KqKwlrSt9PA=       | Rio STYz         |

Quando a chamada `GET /api/stock/value` é feita para um serviço externo, você precisa saber a identidade desse servidor para que possa definir o campo `dependency.target` adequadamente. Quando o serviço externo não suporta a monitorização, `target` é definido para o nome de anfitrião do serviço (por exemplo, `stock-prices-api.com`). Mas se o serviço se identificar devolvendo um cabeçalho HTTP predefinido, `target` contém a identidade de serviço que permite que a Application Insights construa um traço distribuído consultando a telemetria desse serviço.

## <a name="correlation-headers"></a>Cabeçalhos de correlação

Os Insights de Aplicação estão em transição para [o Rastreio-Contexto W3C,](https://w3c.github.io/trace-context/)que define:

- `traceparent`: Transporta o ID de operação globalmente único e identificador único da chamada.
- `tracestate`: Transporta um contexto de rastreio específico do sistema.

A versão mais recente do SDK de Insights de Aplicação suporta o protocolo Trace-Context, mas poderá ter de optar por ele. (Retrocompatibilidade com o protocolo de correlação anterior suportado pelo SDK de Insights de Aplicação será mantido.)

O [protocolo HTTP de correlação, também chamado Request-Id,](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)está a ser depreciado. Este protocolo define dois cabeçalhos:

- `Request-Id`: Transporta a identificação globalmente única da chamada.
- `Correlation-Context`: Transporta a coleção de pares de nomes das propriedades de vestígios distribuídos.

Os Insights de Aplicação também definem a [extensão](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) para o protocolo HTTP de correlação. Utiliza `Request-Context` pares de valor de nome para propagação da coleção de propriedades utilizadas pelo chamador ou callee imediato. O Application Insights SDK usa este cabeçalho para definir os campos `dependency.target` e `request.source`.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Enable W3C suporte de rastreio distribuído para aplicações clássicas de ASP.NET
 
  > [!NOTE]
  >  Começando com `Microsoft.ApplicationInsights.Web` e `Microsoft.ApplicationInsights.DependencyCollector`, não é necessária qualquer configuração.

O suporte w3C Trace-Context é implementado de forma retrocompatível. Espera-se que a correlação funcione com aplicações que são instrumentadas com versões anteriores do SDK (sem suporte W3C).

Se pretender continuar a utilizar o protocolo `Request-Id` legado, pode desativar o Trace-Context utilizando esta configuração:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Se executar uma versão mais antiga do SDK, recomendamos que o atualize ou aplique a seguinte configuração para ativar o Trace-Context.
Esta funcionalidade está disponível nos pacotes `Microsoft.ApplicationInsights.Web` e `Microsoft.ApplicationInsights.DependencyCollector`, a começar pela versão 2.8.0-beta1.
É desativado por defeito. Para o permitir, faça estas alterações para `ApplicationInsights.config`:

- Em `RequestTrackingTelemetryModule`, adicione o elemento `EnableW3CHeadersExtraction` e desemque o seu valor para `true`.
- Em `DependencyTrackingTelemetryModule`, adicione o elemento `EnableW3CHeadersInjection` e desemque o seu valor para `true`.
- Adicione `W3COperationCorrelationTelemetryInitializer` sob `TelemetryInitializers`. Será semelhante a este exemplo:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Ativar o suporte de rastreio distribuído w3C para ASP.NET aplicações Core

 > [!NOTE]
  > A partir `Microsoft.ApplicationInsights.AspNetCore` versão 2.8.0, não é necessária qualquer configuração.
 
O suporte w3C Trace-Context é implementado de forma retrocompatível. Espera-se que a correlação funcione com aplicações que são instrumentadas com versões anteriores do SDK (sem suporte W3C).

Se pretender continuar a utilizar o protocolo `Request-Id` legado, pode desativar o Trace-Context utilizando esta configuração:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Se executar uma versão mais antiga do SDK, recomendamos que o atualize ou aplique a seguinte configuração para ativar o Trace-Context.

Esta funcionalidade encontra-se na versão 2.5.0-beta1 `Microsoft.ApplicationInsights.AspNetCore` e na versão 2.8.0-beta1 `Microsoft.ApplicationInsights.DependencyCollector`.
É desativado por defeito. Para o ativar, `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Ativar o suporte de rastreio distribuído w3C para aplicações Java

- **Configuração de entrada**

  - Para aplicações Java EE, adicione o seguinte à etiqueta `<TelemetryModules>` em ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - Para aplicações Spring Boot, adicione estas propriedades:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Configuração de saída**

  Adicione o seguinte a AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > O modo de compatibilidade para trás é ativado por padrão e o parâmetro `enableW3CBackCompat` é opcional. Use-o apenas quando quiser desligar a compatibilidade.
  >
  > Idealmente, você iria desativar isto quando todos os seus serviços foram atualizados para versões mais recentes de SDKs que suportam o protocolo W3C. Recomendamos vivamente que se mude para estes SDKs mais recentes o mais rápido possível.

> [!IMPORTANT]
> Certifique-se de que as configurações de entrada e saída são exatamente as mesmas.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Ativar o suporte de rastreio distribuído w3C para aplicações Web

Esta funcionalidade encontra-se em `Microsoft.ApplicationInsights.JavaScript`. É desativado por defeito. Para o ativar, utilize `distributedTracingMode` config. AI_AND_W3C está prevista para a retrocompatibilidade com quaisquer serviços legados instrumentados pela Application Insights.

- **configuração npm (ignore se utilizar a configuração snippet)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Configuração de snippet (ignore se utilizar a configuração do npm)**

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

## <a name="opentracing-and-application-insights"></a>Insights de Abertura e Aplicação

O mapa de dados do modelo de [dados OpenTracing](https://opentracing.io/) e application Insights mapeia da seguinte forma:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` com `span.kind = server`                  |
| `Dependency`                          | `Span` com `span.kind = client`                  |
| `Id` de `Request` e `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` de `ChildOf` tipo (o tempo dos pais)   |

Para mais informações, consulte o modelo de dados de [telemetria Application Insights](../../azure-monitor/app/data-model.md).

Para definições de conceitos OpenTracing, consulte as [especificações](https://github.com/opentracing/specification/blob/master/specification.md) OpenTracing e [as convenções semânticas.](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)

## <a name="telemetry-correlation-in-opencensus-python"></a>Correlação de telemetria no OpenCensus Python

O OpenCensus Python segue as especificações do modelo de dados `OpenTracing` delineadas anteriormente. Também suporta [o W3C Trace-Context](https://w3c.github.io/trace-context/) sem necessitar de qualquer configuração.

### <a name="incoming-request-correlation"></a>Correlação de pedido de entrada

OpenCensus Python correlaciona os cabeçalhos w3C Trace-Context dos pedidos de entrada para os vãos que são gerados a partir dos próprios pedidos. O OpenCensus fá-lo-á automaticamente com integrações para estes populares quadros de aplicação web: Flask, Django e Pyramid. Basta preencher os cabeçalhos w3C Trace-Context com o [formato correto](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) e enviá-los com o pedido. Aqui está uma amostra de aplicação do Flask que demonstra isto:

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

Este código executa uma aplicação de balão de amostra na sua máquina local, ouvindo `8080`de porta . Para correlacionar o contexto de rastreio, envia um pedido para o ponto final. Neste exemplo, pode utilizar um comando `curl`:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Ao olhar para o [formato cabeçalho Trace-Context,](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)pode obter as seguintes informações:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Se olhar para a entrada de pedido que foi enviada para o Monitor Azure, pode ver campos povoados com a informação do cabeçalho de traços. Pode encontrar estes dados em Registos (Analytics) no recurso Insights de Aplicação Do Monitor Do Monitor do Azure.

![Solicitar telemetria em Registos (Analytics)](./media/opencensus-python/0011-correlation.png)

O campo `id` está no formato `<trace-id>.<span-id>`, onde o `trace-id` é retirado do cabeçalho de traço que foi passado no pedido e o `span-id` é um conjunto de 8 bytes gerado para este período.

O campo `operation_ParentId` está em formato `<trace-id>.<parent-id>`, onde tanto o `trace-id` como o `parent-id` são retirados do cabeçalho de traço que foi passado no pedido.

### <a name="log-correlation"></a>Correlação de registo

O OpenCensus Python permite-lhe correlacionar os registos adicionando um iD de traço, um ID de extensão e uma bandeira de amostragem para registar registos. Adicione estes atributos instalando a integração de [registos](https://pypi.org/project/opencensus-ext-logging/)OpenCensus. Os seguintes atributos serão adicionados aos objetos `LogRecord` Python: `traceId`, `spanId`e `traceSampled`. Note que isto só entra em vigor para os madeireiros que são criados após a integração.

Aqui está uma aplicação de amostra que demonstra isto:

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
Quando este código funciona, as seguintes impressões na consola:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Note que há um `spanId` presente para a mensagem de registo que está dentro do espaço. Este é o mesmo `spanId` que pertence ao espaço chamado `hello`.

Pode exportar os dados de registo utilizando `AzureLogHandler`. Para obter mais informações, consulte [este artigo](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs).

## <a name="telemetry-correlation-in-net"></a>Correlação de telemetria em .NET

Ao longo do tempo, o .NET definiu várias formas de correlacionar os registos de telemetria e diagnóstico:

- `System.Diagnostics.CorrelationManager` permite o rastreio de [LogicalOperationStack e ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx).
- `System.Diagnostics.Tracing.EventSource` e Rastreio de Eventos para Windows (ETW) definem o método [SetCurrentThreadActivityId.](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx)
- `ILogger` utiliza [os scopes de registo](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes).
- A Windows Communication Foundation (WCF) e a HTTP liguem a propagação do contexto "atual".

Mas esses métodos não permitiram o suporte automático de rastreio distribuído. `DiagnosticSource` suporta a correlação automática entre máquinas cruzadas. .NET bibliotecas suportam `DiagnosticSource` e permitem a propagação automática de máquinas cruzadas do contexto de correlação através do transporte, como http.

O Guia de Utilizadores de [Atividades](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) em `DiagnosticSource` explica o básico das atividades de rastreio.

ASP.NET Core 2.0 suporta a extração de cabeçalhos HTTP e inicia novas atividades.

`System.Net.Http.HttpClient`, a começar pela versão 4.1.0, suporta a injeção automática de cabeçalhos HTTP correlação e rastreio as chamadas HTTP como atividades.

Há um novo módulo HTTP, [Microsoft.AspNet.TelemettryCorrelation,](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/)para ASP.NET clássicos. Este módulo implementa a correlação de telemetria utilizando `DiagnosticSource`. Inicia uma atividade baseada em cabeçalhos de pedido de entrada. Também correlaciona a telemetria das diferentes fases do processamento de pedidos, mesmo quando todas as fases do processamento dos Serviços de Informação da Internet (IIS) são de uma linha gerida diferente.

O Application Insights SDK, a começar pela versão 2.4.0-beta1, utiliza `DiagnosticSource` e `Activity` para recolher telemetria e associá-lo à atividade atual.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Correlação de telemetria no Java SDK

[Application Insights SDK para java](../../azure-monitor/app/java-get-started.md) versão 2.0.0 ou posteriorsuporta correlação automática de telemetria. Preenche automaticamente `operation_id` para todas as telemetrias (como vestígios, exceções e eventos personalizados) emitidas no âmbito de um pedido. Também propaga os cabeçalhos de correlação (descritos anteriormente) para chamadas de serviço-a-serviço via HTTP, se o [agente Java SDK](../../azure-monitor/app/java-agent.md) estiver configurado.

> [!NOTE]
> Apenas as chamadas efetuadas via Apache HttpClient são suportadas para a funcionalidade de correlação. Tanto o Spring RestTemplate como o Feign podem ser usados com o Apache HttpClient sob o capot.

Atualmente, a propagação automática do contexto através de tecnologias de mensagens (como Kafka, RabbitMQ e Azure Service Bus) não é suportada. É possível codificar estes cenários manualmente utilizando os métodos `trackDependency` e `trackRequest`. Nestes métodos, uma telemetria de dependência representa uma mensagem que está a ser enquecida por um produtor. O pedido representa uma mensagem que está a ser processada por um consumidor. Neste caso, tanto `operation_id` como `operation_parentId` devem ser propagados nas propriedades da mensagem.

### <a name="telemetry-correlation-in-asynchronous-java-applications"></a>Correlação de telemetria em aplicações assíncronas java

Para aprender a correlacionar a telemetria numa aplicação assíncrona de Botas de primavera, consulte O [Rastreio Distribuído em Aplicações Java Assíncronas.](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) Este artigo fornece orientação para a instrumentação do [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) da Spring e [do ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html).


<a name="java-role-name"></a>
## <a name="role-name"></a>Nome da função

É melhor personalizar a forma como os nomes dos componentes são apresentados no Mapa de [Aplicações](../../azure-monitor/app/app-map.md). Para tal, pode definir manualmente o `cloud_RoleName` tomando uma das seguintes ações:

- Com insights de aplicação Java SDK 2.5.0 e mais tarde, pode especificar o `cloud_RoleName` adicionando `<RoleName>` ao seu ficheiro ApplicationInsights.xml:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Se utilizar a Bota de Mola com o Arranque de Arranque de Arranque de Mola Insights de Aplicação, basta definir o seu nome personalizado para a aplicação no ficheiro application.properties:

  `spring.application.name=<name-of-app>`

  O Arranque de Arranque de primavera atribui automaticamente `cloudRoleName` ao valor que introduz para a propriedade `spring.application.name`.

## <a name="next-steps"></a>Passos seguintes

- Escreva [telemetria personalizada.](../../azure-monitor/app/api-custom-events-metrics.md)
- Para cenários avançados de correlação em ASP.NET Core e ASP.NET, consulte [as operações personalizadas da Track](custom-operations-tracking.md).
- Saiba mais sobre [a definição de cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) para outros SDKs.
- A bordo de todos os componentes do seu microserviço em Insights de Aplicação. Confira as [plataformas suportadas](../../azure-monitor/app/platforms.md).
- Consulte o modelo de [dados](../../azure-monitor/app/data-model.md) para tipos de Insights de Aplicação.
- Aprenda a estender e filtrar a [telemetria.](../../azure-monitor/app/api-filtering-sampling.md)
- Reveja a referência de [config de Insights](configuration-with-applicationinsights-config.md)de Aplicação .
