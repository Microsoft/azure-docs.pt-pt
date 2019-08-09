---
title: Correlação de telemetria do Aplicativo Azure insights | Microsoft Docs
description: Correlação de telemetria Application Insights
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: 1c6a0ce3e4e8d098d2bc048a331b0ae0cb5c6b13
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881392"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlação de telemetria no Application Insights

No mundo dos microservices, cada operação lógica requer que o trabalho seja feito em vários componentes do serviço. Cada um desses componentes pode ser monitorado separadamente pelo [aplicativo Azure](../../azure-monitor/app/app-insights-overview.md)insights. O componente de aplicativo Web se comunica com o componente do provedor de autenticação para validar as credenciais do usuário e com o componente de API para obter dados para visualização. O componente de API pode consultar dados de outros serviços e usar componentes do provedor de cache para notificar o componente de cobrança sobre essa chamada. O Application Insights dá suporte à correlação de telemetria distribuída, que você usa para detectar qual componente é responsável por falhas ou degradação de desempenho.

Este artigo explica o modelo de dados usado pelo Application Insights para correlacionar a telemetria enviada por vários componentes. Ele aborda técnicas e protocolos de propagação de contexto. Ele também aborda a implementação de conceitos de correlação em diferentes linguagens e plataformas.

## <a name="data-model-for-telemetry-correlation"></a>Modelo de dados para correlação de telemetria

Application Insights define um [modelo de dados](../../azure-monitor/app/data-model.md) para correlação de telemetria distribuída. Para associar a telemetria à operação lógica, cada item de telemetria tem um `operation_Id`campo de contexto chamado. Esse identificador é compartilhado por cada item de telemetria no rastreamento distribuído. Portanto, mesmo com a perda de telemetria de uma única camada, você ainda pode associar a telemetria relatada por outros componentes.

Uma operação lógica distribuída normalmente consiste em um conjunto de operações menores, que são solicitações processadas por um dos componentes. Essas operações são definidas pela [telemetria de solicitação](../../azure-monitor/app/data-model-request-telemetry.md). Cada telemetria de solicitação tem `id` sua própria que a identifica de forma exclusiva e global. E todos os itens de telemetria (como rastreamentos e exceções) associados a essa solicitação devem definir `operation_parentId` o como o valor da solicitação `id`.

Cada operação de saída, como uma chamada HTTP para outro componente, é representada pela telemetria de [dependência](../../azure-monitor/app/data-model-dependency-telemetry.md). A telemetria de dependência também `id` define seu próprio que é globalmente exclusivo. A telemetria de solicitação, iniciada por essa chamada `id` de dependência `operation_parentId`, a usa como sua.

Você pode criar uma exibição da operação `operation_Id`lógica distribuída usando, `operation_parentId`e `request.id` com `dependency.id`. Esses campos também definem a ordem causalidade das chamadas de telemetria.

Em um ambiente de microserviços, os rastreamentos de componentes podem ir para itens de armazenamento diferentes. Cada componente pode ter sua própria chave de instrumentação em Application Insights. Para obter a telemetria para a operação lógica, o Application Insights UX consulta dados de cada item de armazenamento. Quando o número de itens de armazenamento for enorme, você precisará de uma dica sobre onde procurar em seguida. O modelo de dados Application insights define dois campos para resolver esse problema `request.source` : `dependency.target`e. O primeiro campo identifica o componente que iniciou a solicitação de dependência e o segundo identifica qual componente retornou a resposta da chamada de dependência.

## <a name="example"></a>Exemplo

Vamos pegar um exemplo de um aplicativo chamado preços de ações, que mostra o preço de mercado atual de um estoque usando uma API externa chamada `Stock`. O aplicativo de preços de ações tem uma `Stock page` página chamada que o navegador da Web do `GET /Home/Stock`cliente abre usando o. O aplicativo consulta a `Stock` API usando uma chamada `GET /api/stock/value`http.

Você pode analisar a telemetria resultante executando uma consulta:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Nos resultados, observe que todos os itens de telemetria compartilham `operation_Id`a raiz. Quando uma chamada AJAX é feita da página, uma nova ID exclusiva (`qJSXU`) é atribuída à telemetria de dependência e a ID de pageView é usada como. `operation_ParentId` Em seguida, a solicitação do servidor usa a `operation_ParentId`ID do AJAX como.

| itemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Página de ações                |              | STYz               | STYz         |
| dependência | OBTER/Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | OBTER página inicial/estoque            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependência | OBTER/API/Stock/Value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Quando a chamada `GET /api/stock/value` é feita a um serviço externo, você deseja saber a identidade desse servidor para poder definir o `dependency.target` campo adequadamente. Quando o serviço externo não dá suporte ao `target` monitoramento, é definido como o nome do host do serviço (por `stock-prices-api.com`exemplo,). No entanto, se o serviço se identificar retornando um cabeçalho http `target` predefinido, contém a identidade do serviço que permite que Application insights crie um rastreamento distribuído consultando a telemetria desse serviço.

## <a name="correlation-headers"></a>Cabeçalhos de correlação

Estamos trabalhando em uma proposta RFC para o [protocolo http de correlação](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Esta proposta define dois cabeçalhos:

- `Request-Id`: Transporta a ID global exclusiva da chamada.
- `Correlation-Context`: Transporta a coleção de pares nome-valor das propriedades de rastreamento distribuído.

O padrão também define dois esquemas para `Request-Id` geração: simples e hierárquica. Com o esquema simples, uma chave conhecida `Id` é definida para a `Correlation-Context` coleção.

Application Insights define a [extensão](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) para o protocolo http de correlação. Ele usa `Request-Context` pares de nome-valor para propagar a coleção de propriedades usadas pelo chamador ou receptor imediato. O SDK do Application insights usa esse cabeçalho para `dependency.target` definir `request.source` os campos e.

### <a name="w3c-distributed-tracing"></a>Rastreamento distribuído W3C

Estamos fazendo a transição para o [formato de rastreamento distribuído W3C](https://w3c.github.io/trace-context/). Ele define:

- `traceparent`: Transporta a ID da operação globalmente exclusiva e o identificador exclusivo da chamada.
- `tracestate`: Transporta o contexto específico do sistema.

#### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Habilitar o suporte de rastreamento distribuído W3C para aplicativos ASP.NET clássicos

Esse recurso está disponível no `Microsoft.ApplicationInsights.Web` e `Microsoft.ApplicationInsights.DependencyCollector` pacotes a partir da versão 2.8.0-beta1.
Ele é desabilitado por padrão. Para habilitá-lo `ApplicationInsights.config`, altere:

- Em `RequestTrackingTelemetryModule`, adicione o `EnableW3CHeadersExtraction` elemento com o valor definido `true`como.
- Em `DependencyTrackingTelemetryModule`, adicione o `EnableW3CHeadersInjection` elemento com o valor definido `true`como.
- Adicionar `W3COperationCorrelationTelemetryInitializer` sob o `TelemetryInitializers` semelhante a 

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers> 
```

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Habilitar o suporte ao rastreamento distribuído W3C para aplicativos ASP.NET Core

Esse recurso está na `Microsoft.ApplicationInsights.AspNetCore` versão 2.5.0-beta1 e na `Microsoft.ApplicationInsights.DependencyCollector` versão 2.8.0-beta1.
Ele é desabilitado por padrão. Para habilitá-lo `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` , `true`defina como:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Habilitar o suporte ao rastreamento distribuído W3C para aplicativos Java

- **Configuração de entrada**

  - Para aplicativos Java EE, adicione o seguinte à marca `<TelemetryModules>` dentro de ApplicationInsights. xml:

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
  > O modo de compatibilidade com versões anteriores é habilitado por `enableW3CBackCompat` padrão e o parâmetro é opcional. Use-o somente quando quiser desativar a compatibilidade com versões anteriores.
  >
  > O ideal é que você desative essa opção quando todos os seus serviços tiverem sido atualizados para versões mais recentes de SDKs que dão suporte ao protocolo W3C. É altamente recomendável que você mova para esses SDKs mais recentes assim que possível.

> [!IMPORTANT]
> Certifique-se de que as configurações de entrada e saída sejam exatamente as mesmas.

#### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Habilitar o suporte ao rastreamento distribuído W3C para aplicativos Web

Este recurso está em `Microsoft.ApplicationInsights.JavaScript`. Ele é desabilitado por padrão. Para habilitá-lo `distributedTracingMode` , use config. O AI_AND_W3C é fornecido para compatibilidade com os serviços instrumentados Application Insightss herdados:

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

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span`por`span.kind = server`                  |
| `Dependency`                          | `Span`por`span.kind = client`                  |
| `Id`de `Request` e`Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference`do tipo `ChildOf` (a extensão pai)   |

Para obter mais informações, consulte o [modelo de dados de telemetria Application insights](../../azure-monitor/app/data-model.md). 

Para obter definições de conceitos de OpenTracing, consulte a [especificação](https://github.com/opentracing/specification/blob/master/specification.md) OpenTracing e [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-net"></a>Correlação de telemetria no .NET

Ao longo do tempo, o .NET definiu várias maneiras de correlacionar os logs de telemetria e diagnóstico:

- `System.Diagnostics.CorrelationManager`permite o acompanhamento de [LogicalOperationStack e ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- `System.Diagnostics.Tracing.EventSource`e o ETW (rastreamento de eventos para Windows) definem o método [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) .
- `ILogger`usa escopos de [log](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) e HTTP conectam a propagação de contexto "atual".

No entanto, esses métodos não habilitam o suporte automático a rastreamento distribuído. `DiagnosticSource`é uma maneira de dar suporte à correlação automática entre computadores. As bibliotecas .NET dão suporte a ' Diagnosticname ' e permitem a propagação automática entre computadores do contexto de correlação por meio do transporte, como HTTP.

O [guia para atividades](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) no `DiagnosticSource` explica os conceitos básicos do acompanhamento de atividades.

ASP.NET Core 2,0 dá suporte à extração de cabeçalhos HTTP e à inicialização de uma nova atividade.

`System.Net.HttpClient`, a partir da versão 4.1.0, dá suporte à injeção automática dos cabeçalhos HTTP de correlação e ao acompanhamento da chamada HTTP como uma atividade.

Há um novo módulo HTTP, [Microsoft. AspNet. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), para ASP.net clássicas. Esse módulo implementa a correlação de telemetria usando `DiagnosticSource`o. Ele inicia uma atividade com base nos cabeçalhos de solicitação de entrada. Ele também correlaciona a telemetria de diferentes estágios de processamento de solicitação, mesmo para casos em que cada estágio do processamento de Serviços de Informações da Internet (IIS) é executado em um thread gerenciado diferente.

O SDK do Application insights, começando com a versão 2.4.0-beta1 `DiagnosticSource` , `Activity` usa e para coletar telemetria e associá-la à atividade atual.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Correlação de telemetria no SDK do Java

O [SDK do Application insights para Java](../../azure-monitor/app/java-get-started.md) dá suporte à correlação automática de telemetria, começando com a versão 2.0.0. Ele preenche `operation_id` automaticamente todas as telemetrias (como rastreamentos, exceções e eventos personalizados) emitidos dentro do escopo de uma solicitação. Ele também cuida da propagação dos cabeçalhos de correlação (descritos anteriormente) para chamadas de serviço a serviço via HTTP, se o agente do [SDK do Java](../../azure-monitor/app/java-agent.md) estiver configurado.

> [!NOTE]
> Somente as chamadas feitas por meio do Apache HTTPClient têm suporte para o recurso de correlação. Se você estiver usando o Spring Resttemplate ou feign, ambos poderão ser usados com o Apache HTTPClient nos bastidores.

Atualmente, a propagação automática de contexto em tecnologias de mensagens (como Kafka, RabbitMQ ou barramento de serviço do Azure) não tem suporte. No entanto, é possível codificar esses cenários manualmente usando as `trackDependency` APIs e. `trackRequest` Nessas APIs, uma telemetria de dependência representa uma mensagem sendo enfileirada por um produtor e a solicitação representa uma mensagem sendo processada por um consumidor. Nesse caso, `operation_id` e `operation_parentId` devem ser propagadas nas propriedades da mensagem.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Correlação de telemetria no aplicativo Java assíncrono

Para correlacionar a telemetria no aplicativo de Spring boot assíncrono, siga [este](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) artigo detalhado. Ele fornece diretrizes para instrumentação de [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) da mola, bem como [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html). 


<a name="java-role-name"></a>
## <a name="role-name"></a>Nome da função

Às vezes, talvez você queira personalizar a maneira como os nomes de componentes são exibidos no [mapa do aplicativo](../../azure-monitor/app/app-map.md). Para fazer isso, você pode definir manualmente o `cloud_RoleName` seguindo um destes procedimentos:

- Se você usar o Spring boot com o iniciador do Spring boot Application Insights, a única alteração necessária será definir seu nome personalizado para o aplicativo no arquivo Application. Properties.

  `spring.application.name=<name-of-app>`

  O iniciador do Spring boot atribui `cloudRoleName` automaticamente ao valor inserido para a `spring.application.name` propriedade.

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
- Saiba mais sobre como [definir cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) para outros SDKs.
- Integre todos os componentes do seu microserviço em Application Insights. Confira as [plataformas com suporte](../../azure-monitor/app/platforms.md).
- Consulte o [modelo de dados](../../azure-monitor/app/data-model.md) para tipos de Application insights.
- Saiba como [estender e filtrar](../../azure-monitor/app/api-filtering-sampling.md)a telemetria.
- Examine a [referência de configuração de Application insights](configuration-with-applicationinsights-config.md).
