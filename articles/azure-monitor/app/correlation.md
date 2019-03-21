---
title: Correlação de telemetria do Application Insights do Azure | Documentos da Microsoft
description: Correlação de telemetria do Application Insights
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/14/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: d3aad8f1b032960786564bbb18f99c260fd72113
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092723"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlação de telemetria no Application Insights

No mundo dos microsserviços, cada operação lógica requer trabalho a ser feito em vários componentes do serviço. Cada um desses componentes pode ser monitorizada por separadamente [do Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). O componente de aplicação web se comunica com o componente de provedor de autenticação para validar as credenciais de utilizador e com o componente de API para obter dados para visualização. O componente de API pode consultar dados de outros serviços e usar componentes de fornecedor de cache para notificar o componente de faturação sobre esta chamada. Application Insights suporta distribuída correlação de telemetria, que utilizar para detetar que componente é responsável por falhas ou degradação do desempenho.

Este artigo explica o modelo de dados usado pelo Application Insights para correlacionar a telemetria enviada por vários componentes. São abordadas técnicas de propagação de contexto e protocolos. Também aborda de que a implementação dos conceitos de correlação em plataformas e idiomas diferentes.

## <a name="data-model-for-telemetry-correlation"></a>Modelo de dados da correlação de telemetria

O Application Insights define um [modelo de dados](../../azure-monitor/app/data-model.md) de correlação de telemetria distribuída. Para associar o funcionamento lógico telemetria, todos os itens de telemetria tem um campo de contexto chamado `operation_Id`. Este identificador é partilhado por todos os itens de telemetria no rastreio distribuído. Por isso, mesmo com a perda de telemetria a partir de uma única camada, pode ainda associar telemetria comunicada pelos outros componentes.

Normalmente, uma operação lógica distribuída consiste num conjunto de operações menores, que são pedidos processados por um dos componentes. Estas operações são definidas pelas [telemetria de pedido](../../azure-monitor/app/data-model-request-telemetry.md). Telemetria de cada pedido tem seu próprio `id` que identifica com exclusividade e globalmente. E todos os itens de telemetria (como rastreios e exceções) que estão associados este pedido devem ser definido a `operation_parentId` para o valor da solicitação `id`.

Cada operação de saída, como uma chamada HTTP para outro componente, é representada por [telemetria de dependência](../../azure-monitor/app/data-model-dependency-telemetry.md). Telemetria de dependência também define sua própria `id` que seja globalmente exclusivo. Pedido de telemetria, iniciada por chamada de dependência, usa isso `id` como seu `operation_parentId`.

Pode criar uma vista da operação lógica distribuída através de `operation_Id`, `operation_parentId`, e `request.id` com `dependency.id`. Estes campos também definem a ordem de causalidade das chamadas de telemetria.

Num ambiente de microsserviços, os rastreios a partir de componentes podem aceder a itens de armazenamento diferente. Todos os componentes podem ter sua própria chave de instrumentação no Application Insights. Para obter telemetria para o funcionamento lógico, tem de consultar dados a partir de todos os itens de armazenamento. Quando o número de itens de armazenamento é enorme, terá uma dica sobre onde procurar seguinte. O modelo de dados do Application Insights define dois campos para resolver este problema: `request.source` e `dependency.target`. O primeiro campo identifica o componente a solicitação de dependência e o segundo identifica que componente devolveu a resposta da chamada de dependência.

## <a name="example"></a>Exemplo

Vejamos um exemplo de um aplicativo chamado Stock os preços, que mostra o preço do mercado atual de uma ação com uma API externa, chamada `Stock`. O aplicativo de preços de Stock tem uma página chamada `Stock page` que o navegador da web cliente abre utilizando `GET /Home/Stock`. As consultas de aplicações do `Stock` API através da utilização de uma chamada HTTP `GET /api/stock/value`.

Pode analisar a telemetria resultante executando uma consulta:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Nos resultados, tenha em atenção que todos os itens de telemetria partilham raiz `operation_Id`. Quando uma chamada Ajax é feita a partir da página, um novo ID exclusivo (`qJSXU`) é atribuído para a telemetria de dependência e o ID da visualização de página é utilizado como `operation_ParentId`. O pedido de servidor, em seguida, utiliza o ID de Ajax como `operation_ParentId`.

| itemType   | nome                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Página de ações                |              | STYz               | STYz         |
| dependência | GET /Home/estoque           | qJSXU        | STYz               | STYz         |
| pedido    | GET Home/estoque            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependência | OBTER /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Quando a chamada `GET /api/stock/value` é feita para um serviço externo, deseja saber a identidade desse servidor, para que possa definir o `dependency.target` campo adequadamente. Quando o serviço externo não suporta a monitorização, `target` está definido como o nome de anfitrião do serviço (por exemplo, `stock-prices-api.com`). No entanto, se o serviço identifica-se ao devolver um cabeçalho HTTP predefinido, `target` contém a identidade de serviço que permite-Application Insights criar um rastreio distribuído através da consulta de telemetria a partir desse serviço.

## <a name="correlation-headers"></a>Cabeçalhos de correlação

Estamos a trabalhar numa proposta de RFC para o [correlação protocolo HTTP](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Essa proposta seja define dois cabeçalhos de:

- `Request-Id`: Carrega o ID exclusivo global da chamada.
- `Correlation-Context`: Carrega a coleção de pares nome-valor das propriedades de rastreio distribuído.

O padrão também define dois esquemas para `Request-Id` geração: simples e hierárquicas. Com o esquema simples, um conhecido `Id` chave está definida para o `Correlation-Context` coleção.

O Application Insights define a [extensão](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) para a correlação de protocolo HTTP. Ele usa `Request-Context` pares de nome-valor para propagar a coleção de propriedades utilizadas pelo chamador imediato ou receptor. O SDK do Application Insights utiliza este cabeçalho para definir `dependency.target` e `request.source` campos.

### <a name="w3c-distributed-tracing"></a>Rastreio distribuído do W3C

Está a fazer a transição [formato de rastreio distribuído do W3C](https://w3c.github.io/trace-context/). Ela define:

- `traceparent`: Carrega o ID de operação globalmente exclusivo e o identificador exclusivo da chamada.
- `tracestate`: Executa diversas contexto específico do sistema de rastreamento.

#### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Ativar o suporte de rastreamento do W3C distribuído para aplicações ASP.NET clássicos

Esta funcionalidade está disponível no `Microsoft.ApplicationInsights.Web` e `Microsoft.ApplicationInsights.DependencyCollector` pacotes a partir do 2.8.0-beta1 de versão.
Ele está desativado por predefinição. Para ativá-lo, altere `ApplicationInsights.config`:

- Sob `RequestTrackingTelemetryModule`, adicione o `EnableW3CHeadersExtraction` elemento com o valor definido `true`.
- Sob `DependencyTrackingTelemetryModule`, adicione o `EnableW3CHeadersInjection` elemento com o valor definido `true`.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Ativar o suporte de rastreamento do W3C distribuído para aplicações ASP.NET Core

Esta funcionalidade está `Microsoft.ApplicationInsights.AspNetCore` 2.5.0-beta1 de versão e, em `Microsoft.ApplicationInsights.DependencyCollector` 2.8.0-beta1 de versão.
Ele está desativado por predefinição. Para ativá-lo, defina `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` para `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Ativar o suporte de rastreamento do W3C distribuído para aplicações Java

- **Configuração de entrada**

  - Para aplicações de Java EE, adicione o seguinte para o `<TelemetryModules>` marca applicationinsights. XML:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - Para aplicações do Spring Boot, adicione as seguintes propriedades:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Configuração de saída**

  Adicione o seguinte ao IA-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > O modo de compatibilidade com versões anteriores está ativado por predefinição e o `enableW3CBackCompat` parâmetro é opcional. Utilize-o apenas quando pretender desativar a compatibilidade com versões anteriores.
  >
  > Idealmente, deve desativar esta definição quando todos os seus serviços foram atualizados para versões mais recentes de SDKs que suportam o protocolo de W3C. É altamente recomendável que passar para estes SDKs mais recentes logo que possível.

> [!IMPORTANT]
> Certifique-se de que as configurações de entrada e de saída são exatamente os mesmos.

## <a name="opentracing-and-application-insights"></a>OpenTracing e o Application Insights

O [especificação do modelo de dados de OpenTracing](https://opentracing.io/) e modelos de dados do Application Insights do mapa da seguinte forma:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` com o `span.kind = server`                  |
| `Dependency`                          | `Span` com o `span.kind = client`                  |
| `Id` de `Request` e `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` do tipo `ChildOf` (o intervalo de principal)   |

Para obter mais informações, consulte a [modelo de dados de telemetria do Application Insights](../../azure-monitor/app/data-model.md). 

Para obter definições de OpenTracing conceitos, veja o OpenTracing [especificação](https://github.com/opentracing/specification/blob/master/specification.md) e [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-net"></a>Correlação de telemetria no .NET

Ao longo do tempo, o .NET definido várias formas de correlacionar os registos de telemetria e diagnóstico:

- `System.Diagnostics.CorrelationManager` permite que o controlo de [LogicalOperationStack e ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- `System.Diagnostics.Tracing.EventSource` e o rastreio de eventos para Windows (ETW) definem os [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) método.
- `ILogger` utiliza [âmbitos de registo](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) e HTTP conectar "current" propagação de contexto.

No entanto, esses métodos não tiver ativado o suporte de rastreio distribuído automática. `DiagnosticSource` é uma forma para oferecer suporte a correlação de várias máquinas automática. Bibliotecas .NET "DiagnosticSource" de suporte e permitem a propagação automática de várias máquinas do contexto correlação por meio de transporte, como o HTTP.

O [guia para atividades](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) no `DiagnosticSource` explica as noções básicas de atividades de controlo.

ASP.NET Core 2.0 oferece suporte a extração dos cabeçalhos HTTP e a partir de uma nova atividade.

`System.Net.HttpClient`, a partir da versão 4.1.0, oferece suporte a injeção automática dos cabeçalhos HTTP de correlação e controle o HTTP chamar como uma atividade.

Existe um novo módulo HTTP, [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), para o ASP.NET clássico. Este módulo implementa a correlação de telemetria utilizando `DiagnosticSource`. Inicia uma atividade com base nos cabeçalhos de pedido de entrada. Ele também correlaciona telemetria a partir dos diferentes estágios de processamento, mesmo para casos em que cada estágio do processamento de serviços de informação Internet (IIS) é executado num thread gerenciado diferente da solicitação.

O SDK do Application Insights, começando com a versão 2.4.0-beta1, usa `DiagnosticSource` e `Activity` recolher telemetria e associá-lo com a atividade atual.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Correlação de telemetria no SDK do Java

O [Application Insights SDK para Java](../../azure-monitor/app/java-get-started.md) oferece suporte a correlação automática de início de telemetria com a versão 2.0.0. Povoa automaticamente `operation_id` para toda a telemetria (por exemplo, rastreios, exceções e eventos personalizados) emitida dentro do escopo de um pedido. Ele também se encarrega de propagar os cabeçalhos de correlação (descritos anteriormente) para chamadas de serviço para serviço via HTTP, se o [agente Java SDK](../../azure-monitor/app/java-agent.md) está configurado.

> [!NOTE]
> Apenas as chamadas efetuadas através do Apache HTTPClient são suportadas para a funcionalidade de correlação. Se estiver a utilizar o Spring RestTemplate ou Feign, ambos podem ser utilizadas com o Apache HTTPClient, nos bastidores.

Atualmente, não é suportada a propagação automática de contexto em tecnologias (tal Kafka, RabbitMQ ou do Azure Service Bus) de mensagens. No entanto, é possível codificar esses cenários manualmente utilizando o `trackDependency` e `trackRequest` APIs. Essas APIs, telemetria de dependência representa uma mensagem a ser colocados em fila por um produtor e o pedido representa uma mensagem a ser processada por um consumidor. Neste caso, ambos `operation_id` e `operation_parentId` deve ser propagadas nas propriedades da mensagem.

<a name="java-role-name"></a>
## <a name="role-name"></a>Nome da função

Às vezes, pode querer personalizar a forma como os nomes de componentes são apresentados no [mapa da aplicação](../../azure-monitor/app/app-map.md). Para fazer isso, pode definir manualmente o `cloud_RoleName` efetuando um dos seguintes procedimentos:

- Se utilizar o Spring Boot com o arranque de Spring Boot do Application Insights, a única alteração necessária é definir seu nome personalizado para o aplicativo no arquivo Application.

  `spring.application.name=<name-of-app>`

  Atribui automaticamente o starter Spring Boot `cloudRoleName` para o valor introduzido para o `spring.application.name` propriedade.

- Se estiver a utilizar o `WebRequestTrackingFilter`, o `WebAppNameContextInitializer` define o nome do aplicativo automaticamente. Adicione o seguinte ao ficheiro de configuração (applicationinsights. xml):

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- Se usar a classe de contexto de cloud:

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>Passos Seguintes

- Escrever [telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md).
- Saiba mais sobre [definição cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud_rolename) para outros SDKs.
- Carregar todos os componentes de seus microsserviços no Application Insights. Veja a [plataformas suportadas](../../azure-monitor/app/platforms.md).
- Consulte a [modelo de dados](../../azure-monitor/app/data-model.md) para tipos do Application Insights.
- Saiba como [estender e filtrar telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- Reveja os [referência de configuração do Application Insights](configuration-with-applicationinsights-config.md).
