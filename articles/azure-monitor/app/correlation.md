---
title: Correlação de telemetria Azure Application Insights / Microsoft Docs
description: Correlação de telemetria de insights de aplicação
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.custom: tracking-python
ms.openlocfilehash: b4facaee44a0bc5c7d64376ca80e5aaf8d0768d0
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323167"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlação de telemetria em Insights de Aplicação

No mundo dos microserviços, cada operação lógica requer trabalho a ser feito em vários componentes do serviço. Pode monitorizar cada um destes componentes separadamente utilizando [o Application Insights](./app-insights-overview.md). A Application Insights suporta a correlação de telemetria distribuída, que utiliza para detetar qual o componente responsável por falhas ou degradação do desempenho.

Este artigo explica o modelo de dados utilizado pela Application Insights para correlacionar a telemetria enviada por vários componentes. Abrange técnicas e protocolos de propagação de contexto. Abrange também a implementação de táticas de correlação em diferentes línguas e plataformas.

## <a name="data-model-for-telemetry-correlation"></a>Modelo de dados para a correlação de telemetria

O Application Insights define um [modelo de dados](./data-model.md) para a correlação de telemetria distribuída. Para associar a telemetria a uma operação lógica, cada item de telemetria tem um campo de contexto chamado `operation_Id` . Este identificador é partilhado por cada item de telemetria no vestígio distribuído. Assim, mesmo que perca a telemetria de uma única camada, ainda pode associar a telemetria relatada por outros componentes.

Uma operação lógica distribuída consiste tipicamente num conjunto de operações mais pequenas que são pedidos processados por um dos componentes. Estas operações são definidas por [telemetria de pedido.](./data-model-request-telemetry.md) Cada item de telemetria de pedido tem o seu próprio `id` que o identifica de forma única e global. E todos os itens de telemetria (tais como vestígios e exceções) que estejam associados ao pedido devem definir `operation_parentId` o valor do pedido `id` .

Todas as operações de saída, como uma chamada HTTP para outro componente, são representadas por [telemetria de dependência.](./data-model-dependency-telemetry.md) A telemetria de dependência também define a sua própria `id` que é globalmente única. Solicite telemetria, iniciada por esta chamada de dependência, usa isto `id` como `operation_parentId` seu .

Pode construir uma visão da operação lógica distribuída utilizando `operation_Id` `operation_parentId` , e com `request.id` `dependency.id` . Estes campos também definem a ordem de causalidade das chamadas de telemetria.

Num ambiente de microserviços, os vestígios de componentes podem ir para diferentes itens de armazenamento. Cada componente pode ter a sua própria chave de instrumentação em Application Insights. Para obter telemetria para a operação lógica, o Application Insights consulta dados de cada item de armazenamento. Quando o número de itens de armazenamento é grande, você precisará de uma pista sobre onde procurar a seguir. O modelo de dados Application Insights define dois campos para resolver este problema: `request.source` e `dependency.target` . O primeiro campo identifica o componente que iniciou o pedido de dependência. O segundo campo identifica qual componente devolveu a resposta da chamada de dependência.

## <a name="example"></a>Exemplo

Vejamos um exemplo. Uma aplicação chamada Stock Prices mostra o preço de mercado atual de uma ação através de uma API externa chamada Stock. A aplicação Preços de Stock tem uma página chamada Página de Stock que o navegador web do cliente abre usando `GET /Home/Stock` . A aplicação consulta a API de Stock utilizando a chamada HTTP `GET /api/stock/value` .

Pode analisar a telemetria resultante executando uma consulta:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Nos resultados, note que todos os itens de telemetria partilham a raiz `operation_Id` . Quando uma chamada do Ajax é feita a partir da página, um novo ID único `qJSXU` () é atribuído à telemetria de dependência, e o ID da páginaView é usado como `operation_ParentId` . O pedido do servidor utiliza então o ID do Ajax como `operation_ParentId` .

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| páginaVer   | Página de stock                |              | STYZ               | STYZ         |
| dependência | GET /Home/Stock           | qJSXU        | STYZ               | STYZ         |
| pedido    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYZ         |
| dependência | GET /api/stock/valor      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYZ         |

Quando a chamada `GET /api/stock/value` é feita para um serviço externo, precisa de saber a identidade desse servidor para que possa definir o campo `dependency.target` adequadamente. Quando o serviço externo não suporta a monitorização, `target` é definido para o nome de anfitrião do serviço (por exemplo, `stock-prices-api.com` ). Mas se o serviço se identificar devolvendo um cabeçalho HTTP predefinido, `target` contém a identidade de serviço que permite que a Application Insights construa um traço distribuído consultando telemetria a partir desse serviço.

## <a name="correlation-headers"></a>Cabeçalhos de correlação

A Application Insights está em transição para [o Contexto de Rastreio W3C,](https://w3c.github.io/trace-context/)que define:

- `traceparent`: Transporta o ID de operação globalmente único e identificador único da chamada.
- `tracestate`: Transporta o contexto de rastreio específico do sistema.

A versão mais recente do Application Insights SDK suporta o protocolo Trace-Context, mas poderá ter de optar por ele. (A retrocompatibilidade com o protocolo de correlação anterior suportado pelo Application Insights SDK será mantida.)

O [protocolo HTTP de correlação, também chamado Request-Id,](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)está a ser depreciado. Este protocolo define dois cabeçalhos:

- `Request-Id`: Transporta a identificação globalmente única da chamada.
- `Correlation-Context`: Transporta a coleção de pares de valor-nome das propriedades de vestígios distribuídos.

Os Insights de Aplicação também definem a [extensão](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) para o protocolo HTTP de correlação. Utiliza `Request-Context` pares de valor-nome para propagar a coleção de propriedades utilizadas pelo chamador ou callee imediato. O Application Insights SDK utiliza este cabeçalho para definir os `dependency.target` campos e `request.source` campos.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Ativar suporte de rastreio distribuído W3C para aplicações de ASP.NET clássicas
 
  > [!NOTE]
  >  A partir de `Microsoft.ApplicationInsights.Web` e , não é necessária nenhuma `Microsoft.ApplicationInsights.DependencyCollector` configuração.

O suporte ao contexto de rastreio W3C é implementado de forma retro-compatível. Espera-se que a correlação funcione com aplicações instrumentadas com versões anteriores do SDK (sem suporte W3C).

Se quiser continuar a utilizar o `Request-Id` protocolo legado, pode desativar o Trace-Context utilizando esta configuração:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Se executar uma versão mais antiga do SDK, recomendamos que a atualize ou aplique a seguinte configuração para ativar o Contexto de Rastreio.
Esta funcionalidade encontra-se disponível nos `Microsoft.ApplicationInsights.Web` pacotes e `Microsoft.ApplicationInsights.DependencyCollector` pacotes, a começar pela versão 2.8.0-beta1.
É desativado por defeito. Para o habilitar, faça estas alterações `ApplicationInsights.config` para:

- Em `RequestTrackingTelemetryModule` , adicione o elemento e `EnableW3CHeadersExtraction` desem este valor para `true` .
- Em `DependencyTrackingTelemetryModule` , adicione o elemento e `EnableW3CHeadersInjection` desem este valor para `true` .
- Adicione `W3COperationCorrelationTelemetryInitializer` `TelemetryInitializers` abaixo . Será semelhante a este exemplo:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Ativar o suporte de rastreio distribuído pela W3C para aplicações ASP.NET Core

 > [!NOTE]
  > Começando pela `Microsoft.ApplicationInsights.AspNetCore` versão 2.8.0, não é necessária configuração.
 
O suporte ao contexto de rastreio W3C é implementado de forma retro-compatível. Espera-se que a correlação funcione com aplicações instrumentadas com versões anteriores do SDK (sem suporte W3C).

Se quiser continuar a utilizar o `Request-Id` protocolo legado, pode desativar o Trace-Context utilizando esta configuração:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Se executar uma versão mais antiga do SDK, recomendamos que a atualize ou aplique a seguinte configuração para ativar o Contexto de Rastreio.

Esta funcionalidade `Microsoft.ApplicationInsights.AspNetCore` encontra-se na versão 2.5.0-beta1 e na `Microsoft.ApplicationInsights.DependencyCollector` versão 2.8.0-beta1.
É desativado por defeito. Para o habilitar, de definido `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` `true` para:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Ativar o suporte de rastreio distribuído pela W3C para aplicações Java

#### <a name="java-30-agent"></a>Java 3.0 agente

  O agente Java 3.0 suporta o W3C fora da caixa e não é necessária nenhuma configuração adicional. 

#### <a name="java-sdk"></a>SDK Java
- **Configuração de entrada**

  - Para aplicações Java EE, adicione o seguinte à `<TelemetryModules>` etiqueta em ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - Para aplicações Boot de mola, adicione estas propriedades:

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
  > O modo de compatibilidade retrógrada é ativado por predefinição e o `enableW3CBackCompat` parâmetro é opcional. Use-o apenas quando quiser desligar a compatibilidade para trás.
  >
  > Idealmente, você iria desativar isto quando todos os seus serviços foram atualizados para versões mais recentes de SDKs que suportam o protocolo W3C. Recomendamos vivamente que se mude para estes Novos SDKs o mais rápido possível.

> [!IMPORTANT]
> Certifique-se de que as configurações de entrada e saída são exatamente as mesmas.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Ativar suporte de rastreio distribuído W3C para aplicações Web

Esta funcionalidade está em `Microsoft.ApplicationInsights.JavaScript` . É desativado por defeito. Para o ativar, utilize `distributedTracingMode` config. AI_AND_W3C é fornecida para retrocompatibilidade com quaisquer serviços legados instrumentados pela Application Insights.

- **configuração npm (ignorar se utilizar a configuração snippet)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Configuração do snippet (ignore se utilizar a configuração npm)**

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

## <a name="opentracing-and-application-insights"></a>Aberturatracing e Insights de Aplicação

A [especificação do modelo de dados OpenTracing](https://opentracing.io/) e os modelos de dados do Application Insights mapeam da seguinte forma:

| Application Insights                   | Abretracção                                        |
|------------------------------------    |-------------------------------------------------    |
| `Request`, `PageView`                  | `Span`com`span.kind = server`                    |
| `Dependency`                           | `Span`com`span.kind = client`                    |
| `Id`de `Request` e`Dependency`     | `SpanId`                                            |
| `Operation_Id`                         | `TraceId`                                           |
| `Operation_ParentId`                   | `Reference`do tipo `ChildOf` (o período dos pais)     |

Para obter mais informações, consulte o [modelo de dados de telemetria Da Aplicação Insights](./data-model.md).

Para definições de conceitos OpenTracing, consulte a [especificação](https://github.com/opentracing/specification/blob/master/specification.md) OpenTracing e [as convenções semânticas.](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)

## <a name="telemetry-correlation-in-opencensus-python"></a>Correlação de telemetria em OpenCensus Python

O OpenCensus Python segue as especificações do `OpenTracing` modelo de dados descritas anteriormente. Também suporta [o contexto de rastreio W3C](https://w3c.github.io/trace-context/) sem necessitar de qualquer configuração.

### <a name="incoming-request-correlation"></a>Correlação de pedido de entrada

O OpenCensus Python correlaciona os cabeçalhos W3C Trace-Context de pedidos de entrada para os vãos que são gerados a partir dos próprios pedidos. O OpenCensus fá-lo-á automaticamente com integrações para estes quadros populares de aplicações web: Flask, Django e Pirâmide. Basta preencher os cabeçalhos W3C Trace-Context com o [formato correto](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) e enviá-los com o pedido. Aqui está uma aplicação de amostra flask que demonstra isto:

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

Este código executa uma aplicação de amostras de frasco na sua máquina local, ouvindo a porta `8080` . Para correlacionar o contexto de rastreio, envia um pedido para o ponto final. Neste exemplo, pode utilizar um `curl` comando:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Ao olhar para o [formato do cabeçalho Trace-Context,](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)pode obter as seguintes informações:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Se olhar para a entrada de pedido que foi enviada para o Azure Monitor, pode ver campos povoados com a informação do cabeçalho de traços. Pode encontrar estes dados em Registos (Analytics) no recurso Azure Monitor Application Insights.

![Solicitar telemetria em Logs (Analytics)](./media/opencensus-python/0011-correlation.png)

O `id` campo está no formato , onde o é retirado do `<trace-id>.<span-id>` `trace-id` cabeçalho de traço que foi passado no pedido e `span-id` o é um conjunto gerado de 8 bytes para este período.

O `operation_ParentId` campo está no formato , onde tanto o e o são `<trace-id>.<parent-id>` `trace-id` `parent-id` retirados do cabeçalho de traço que foi passado no pedido.

### <a name="log-correlation"></a>Correlação de registos

O OpenCensus Python permite-lhe correlacionar registos adicionando um ID de vestígios, uma identificação de envergadura e uma bandeira de amostragem para registar registos. Adiciona estes atributos instalando a [integração de registos](https://pypi.org/project/opencensus-ext-logging/)OpenCensus . Os seguintes atributos serão adicionados aos `LogRecord` objetos Python: `traceId` , e `spanId` `traceSampled` . Note que isto só produz efeitos para os madeireiros que são criados após a integração.

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
Quando este código é executado, as seguintes impressões na consola:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Note que há um `spanId` presente para a mensagem de registo que está dentro do intervalo. Isto é o mesmo `spanId` que pertence ao período chamado `hello` .

Pode exportar os dados de registo utilizando `AzureLogHandler` . Para obter mais informações, consulte [este artigo](./opencensus-python.md#logs).

## <a name="telemetry-correlation-in-net"></a>Correlação de telemetria em .NET

Ao longo do tempo, .NET definiu várias formas de correlacionar registos de telemetria e diagnósticos:

- `System.Diagnostics.CorrelationManager`permite o rastreio da [LogicalOperationStack e activityId.](/dotnet/api/system.diagnostics.correlationmanager?view=netcore-3.1)
- `System.Diagnostics.Tracing.EventSource`e O Rastreio de Eventos para Windows (ETW) define o método [SetCurrentThreadActivityId.](/dotnet/api/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid?view=netcore-3.1#overloads)
- `ILogger`utiliza [miras de log](/aspnet/core/fundamentals/logging#log-scopes).
- A Windows Communication Foundation (WCF) e a HTTP ligam a propagação do contexto "atual".

Mas esses métodos não permitiram o suporte automático de rastreio distribuído. `DiagnosticSource`suporta a correlação automática entre máquinas. .NET as bibliotecas `DiagnosticSource` suportam e permitem a propagação automática do contexto de correlação através do transporte, tais como HTTP.

O [Guia do Utilizador de Atividades](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) explica os `DiagnosticSource` fundamentos das atividades de rastreio.

ASP.NET Core 2.0 suporta a extração de cabeçalhos HTTP e o início de novas atividades.

`System.Net.Http.HttpClient`, a partir da versão 4.1.0, suporta a injeção automática de cabeçalhos HTTP de correlação e rastreio de chamadas HTTP como atividades.

Há um novo módulo HTTP, [Microsoft.AspNet.TelemetryCorrelation,](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/)para ASP.NET clássicos. Este módulo implementa a correlação de telemetria utilizando `DiagnosticSource` . Inicia uma atividade baseada em cabeçalhos de pedidos de entrada. Também correlaciona a telemetria das diferentes fases do processamento de pedidos, mesmo quando todas as fases de processamento dos Serviços de Informação da Internet (IIS) são geridas numa linha gerida diferente.

A Aplicação Insights SDK, a começar pela versão 2.4.0-beta1, utiliza `DiagnosticSource` e `Activity` recolhe telemetria e associou-a à atividade atual.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Correlação de telemetria em Java

[O agente Java,](./java-in-process-agent.md) bem como a versão [Java SDK](../../azure-monitor/app/java-get-started.md) 2.0.0 ou posteriormente suportam a correlação automática de telemetria. Ele povoa automaticamente `operation_id` para toda a telemetria (como vestígios, exceções e eventos personalizados) emitidos no âmbito de um pedido. Também propaga os cabeçalhos de correlação (descritos anteriormente) para chamadas de serviço-a-serviço via HTTP, se o [agente Java SDK](../../azure-monitor/app/java-agent.md) estiver configurado.

> [!NOTE]
> Application Insights Java agente coleta automaticamente pedidos e dependências para JMS, Kafka, Netty/Webflux, e muito mais. Para Java SDK apenas as chamadas feitas via Apache HttpClient são suportadas para a função de correlação. A propagação automática do contexto através de tecnologias de mensagens (como Kafka, RabbitMQ e Azure Service Bus) não é suportada no SDK. 

> [!NOTE]
> Para recolher telemetria personalizada, é necessário instrumentar a aplicação com Java 2.6 SDK. 

### <a name="role-names"></a>Nomes de papéis

É melhor personalizar a forma como os nomes dos componentes são apresentados no Mapa de [Aplicações.](../../azure-monitor/app/app-map.md) Para tal, pode definir manualmente o `cloud_RoleName` através de uma das seguintes ações:

- Para o agente Java 3.0, desaprove o nome da função de nuvem da seguinte forma:

    ```json
    {
      "instrumentationSettings": {
        "preview": {
          "roleName": "my cloud role name"
        }
      }
    }
    ```
    Também pode definir o nome da função de nuvem usando a variável `APPLICATIONINSIGHTS_ROLE_NAME` ambiente.

- Com o Application Insights Java SDK 2.5.0 e mais tarde, pode especificar o `cloud_RoleName` através da adição `<RoleName>` ao seu ficheiro ApplicationInsights.xml:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Se utilizar o Boot De primavera com o Arranque de Arranque de Mola Do Application Insights, basta definir o seu nome personalizado para a aplicação no ficheiro aplicações.properties:

  `spring.application.name=<name-of-app>`

  O Arranque de Arranque de Mola atribui automaticamente `cloudRoleName` ao valor que introduz para a `spring.application.name` propriedade.

## <a name="next-steps"></a>Passos seguintes

- Escreva [telemetria personalizada.](./api-custom-events-metrics.md)
- Para cenários de correlação avançados em ASP.NET Core e ASP.NET, consulte [as operações personalizadas track](custom-operations-tracking.md).
- Saiba mais sobre [a definição cloud_RoleName](./app-map.md#set-cloud-role-name) para outros SDKs.
- A bordo de todos os componentes do seu microserviço no Application Insights. Confira as [plataformas suportadas.](./platforms.md)
- Consulte o [modelo de dados](./data-model.md) para os tipos de Insights de Aplicação.
- Saiba como [estender e filtrar a telemetria.](./api-filtering-sampling.md)
- Reveja a [referência config do Application Insights config](configuration-with-applicationinsights-config.md).

