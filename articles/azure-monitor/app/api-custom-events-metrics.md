---
title: Aplicações Insights API para eventos e métricas personalizados Microsoft Docs
description: Insira algumas linhas de código no seu dispositivo ou aplicação de ambiente de trabalho, página web ou serviço, para rastrear problemas de utilização e diagnóstico.
ms.topic: conceptual
ms.date: 05/11/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: e9f175e2585a5254922c9e859cf5ece2afbbc3e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91264138"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>API do Application Insights para métricas e eventos personalizados

Insira algumas linhas de código na sua aplicação para saber o que os utilizadores estão a fazer com a sua aplicação ou para ajudar a diagnosticar problemas. Pode enviar telemetria a partir de aplicações de dispositivos e desktop, clientes web e servidores web. Utilize a [Azure Application Insights](./app-insights-overview.md) core telemetria API para enviar eventos e métricas personalizados, e as suas próprias versões de telemetria padrão. Esta API é a mesma API que os colecionadores de dados padrão application insights usam.

## <a name="api-summary"></a>Resumo da API

A API central é uniforme em todas as plataformas, para além de algumas variações como `GetMetric` (apenas.NET).

| Método | Utilizado para |
| --- | --- |
| [`TrackPageView`](#page-views) |Páginas, ecrãs, lâminas ou formulários. |
| [`TrackEvent`](#trackevent) |Ações de utilizador e outros eventos. Usado para monitorizar o comportamento do utilizador ou para monitorizar o desempenho. |
| [`GetMetric`](#getmetric) |Métricas zero e multidimensionais, agregação centralmente configurada, apenas C#. |
| [`TrackMetric`](#trackmetric) |Medições de desempenho, tais como comprimentos de fila não relacionados com eventos específicos. |
| [`TrackException`](#trackexception) |Registando exceções para o diagnóstico. Trace onde ocorrem em relação a outros eventos e examine vestígios de pilha. |
| [`TrackRequest`](#trackrequest) |Registando a frequência e duração dos pedidos do servidor para análise de desempenho. |
| [`TrackTrace`](#tracktrace) |Mensagens de registo de diagnóstico de recursos. Também pode capturar registos de terceiros. |
| [`TrackDependency`](#trackdependency) |Registar a duração e frequência das chamadas para componentes externos de que a sua aplicação depende. |

Pode [anexar propriedades e métricas](#properties) à maioria destas chamadas de telemetria.

## <a name="before-you-start"></a><a name="prep"></a>Antes de começar

Se ainda não tem uma referência sobre a Aplicação Insights SDK:

* Adicione o SDK de Insights de Aplicação ao seu projeto:

  * [projeto ASP.NET](./asp-net.md)
  * [Projeto ASP.NET Core](./asp-net-core.md)
  * [Projeto Java](./java-get-started.md)
  * [ projetoNode.js](./nodejs.md)
  * [JavaScript em cada página web](./javascript.md) 
* No seu dispositivo ou código do servidor web, inclua:

    *C#:*`using Microsoft.ApplicationInsights;`

    *Básico Visual:*`Imports Microsoft.ApplicationInsights`

    *Java:*`import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:*`var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Obtenha um caso de TelemetriaClient

Obtenha um exemplo de `TelemetryClient` (exceto em JavaScript em páginas web):

Para [ASP.NET](asp-net-core.md#how-can-i-track-telemetry-thats-not-automatically-collected) aplicações Core e não HTTP/Worker para aplicações [.NET/.NET Core,](worker-service.md#how-can-i-track-telemetry-thats-not-automatically-collected) recomenda-se obter uma instância `TelemetryClient` do recipiente de injeção de dependência, conforme explicado na respetiva documentação.

Se utilizar AzureFunctions v2+ ou Azure WebJobs v3+ - siga este documento: https://docs.microsoft.com/azure/azure-functions/functions-monitoring#version-2x-and-higher

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```
Para quem vê este método é obsoleta as mensagens, visite [microsoft/ApplicationInsights-dotnet#1152](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1152) para mais detalhes.

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
``` 

*Node.js*

```javascript
var telemetry = applicationInsights.defaultClient;
```

O TelemetriaClient é seguro.

Para ASP.NET e java, os pedidos HTTP de entrada são automaticamente capturados. É melhor criar instâncias adicionais de TelemetriaClient para outro módulo da sua aplicação. Por exemplo, você pode ter um exemplo de TelemetriaClient na sua classe de middleware para reportar eventos de lógica de negócios. Pode definir propriedades como UserId e DeviceId para identificar a máquina. Esta informação está anexada a todos os eventos que a instância envia.

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

Em Node.js projetos, pode utilizar `new applicationInsights.TelemetryClient(instrumentationKey?)` para criar um novo exemplo, mas este é recomendado apenas para cenários que requerem configuração isolada do singleton `defaultClient` .

## <a name="trackevent"></a>TrackEvent

No Application Insights, um *evento personalizado* é um ponto de dados que pode exibir no [Metrics Explorer](../platform/metrics-charts.md) como uma contagem agregada, e na Pesquisa de [Diagnóstico](./diagnostic-search.md) como ocorrências individuais. (Não está relacionado com MVC ou outros "eventos" enquadramento.

Insira `TrackEvent` chamadas no seu código para contar vários eventos. Quantas vezes os utilizadores escolhem uma determinada funcionalidade, com que frequência alcançam objetivos específicos, ou talvez com que frequência cometem erros específicos.

Por exemplo, numa aplicação de jogo, envie um evento sempre que um utilizador ganhe o jogo:

*JavaScript*

```javascript
appInsights.trackEvent({name:"WinGame"});
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.js*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>Eventos personalizados em Analytics

A telemetria está disponível na `customEvents` tabela no Application Insights [Analytics](../log-query/log-query-overview.md). Cada linha representa uma chamada para a `trackEvent(..)` sua aplicação.

Se [a amostragem](./sampling.md) estiver em funcionamento, a propriedade itemCount mostra um valor superior a 1. Por exemplo, o artigoCount==10 significa que de 10 chamadas para rastrear o Event(), o processo de amostragem apenas transmitiu uma delas. Para obter uma contagem correta de eventos personalizados, deve, portanto, utilizar código como `customEvents | summarize sum(itemCount)` .

## <a name="getmetric"></a>GetMetric

Para aprender a utilizar eficazmente a chamada GetMetric() para capturar métricas pré-agregadas localmente para aplicações .NET e .NET Core visite a documentação [GetMetric.](./get-metric.md)

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> Microsoft.ApplicationInsights.TelemetryClient.TrackMetric não é o método preferido para o envio de métricas. As métricas devem ser sempre pré-agregadas durante um período de tempo antes de serem enviadas. Utilize uma das sobrecargas GetMetric (..) para obter um objeto métrico para aceder às capacidades de pré-agregação SDK. Se estiver a implementar a sua própria lógica de pré-agregação, pode utilizar o método TrackMetric () para enviar os agregados resultantes. Se a sua aplicação necessitar de enviar um item de telemetria separado em todas as ocasiões sem agregação ao longo do tempo, é provável que tenha uma caixa de utilização para telemetria de eventos; ver TelemetryClient.TrackEvent (Microsoft.ApplicationInsights.DataContracts.EventTelemetry).

Os Insights de Aplicação podem traçar métricas que não estão anexadas a eventos particulares. Por exemplo, pode monitorizar um comprimento de fila em intervalos regulares. Com as métricas, as medições individuais são de menor interesse do que as variações e tendências, pelo que os gráficos estatísticos são úteis.

Para enviar métricas para Application Insights, pode utilizar a `TrackMetric(..)` API. Há duas maneiras de enviar uma métrica:

* Valor único. Sempre que efetua uma medição na sua aplicação, envia o valor correspondente para Insights de Aplicação. Por exemplo, assuma que tem uma métrica que descreve o número de itens num recipiente. Durante um determinado período de tempo, coloca-se primeiro três itens no recipiente e depois remove-se dois itens. Assim, chamaria `TrackMetric` duas vezes: primeiro passando o valor `3` e depois o valor `-2` . A Application Insights armazena os dois valores em seu nome.

* Agregação. Quando se trabalha com métricas, cada medida raramente é de interesse. Em vez disso, é importante um resumo do que aconteceu durante um determinado período de tempo. Tal resumo é chamado _de agregação._ No exemplo acima, a soma métrica agregada para esse período de tempo é `1` e a contagem dos valores métricos é `2` . Ao utilizar a abordagem de agregação, só invoca `TrackMetric` uma vez por período de tempo e envia os valores agregados. Esta é a abordagem recomendada, uma vez que pode reduzir significativamente o custo e a sobrecarga de desempenho, enviando menos pontos de dados para a Application Insights, enquanto ainda recolhe todas as informações relevantes.

### <a name="examples"></a>Exemplos

#### <a name="single-values"></a>Valores únicos

Para enviar um único valor métrico:

*JavaScript*

 ```javascript
appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
var sample = new MetricTelemetry();
sample.Name = "metric name";
sample.Value = 42.3;
telemetryClient.TrackMetric(sample);
```

*Java*

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.js*

 ```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

### <a name="custom-metrics-in-analytics"></a>Métricas personalizadas em Analytics

A telemetria está disponível na `customMetrics` tabela no Application Insights [Analytics](../log-query/log-query-overview.md). Cada linha representa uma chamada para a `trackMetric(..)` sua aplicação.

* `valueSum` - Esta é a soma das medições. Para obter o valor médio, divida `valueCount` por.
* `valueCount` - O número de medições que foram agregadas nesta `trackMetric(..)` chamada.

## <a name="page-views"></a>Vistas da página

Num dispositivo ou aplicação de página web, a telemetria de visualização da página é enviada por padrão quando cada ecrã ou página está carregado. Mas pode alterar isso para rastrear as visualizações das páginas em momentos adicionais ou diferentes. Por exemplo, numa aplicação que exibe separadores ou lâminas, é melhor seguir uma página sempre que o utilizador abrir uma nova lâmina.

Os dados do utilizador e da sessão são enviados como propriedades juntamente com as visualizações da página, para que os gráficos de utilizador e de sessão vivam quando há telemetria de visualização de página.

### <a name="custom-page-views"></a>Vistas personalizadas da página

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

Se tiver vários separadores dentro de diferentes páginas HTML, pode especificar o URL também:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Vistas da página do tempo

Por predefinição, os tempos relatados à medida do tempo de carga da vista de **página** são medidos a partir de quando o navegador envia o pedido, até que o evento de carga de página do navegador seja chamado.

Em vez disso, pode:

* Desaver uma duração explícita na chamada [trackPageView:](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/API.md#trackpageview) `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);` .
* Utilize as chamadas de cronometragem da página `startTrackPage` e `stopTrackPage` .

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

O nome que usa como primeiro parâmetro associa as chamadas iniciais e stop. Não tem o nome da página atual.

As durações de carga da página resultantes apresentadas no Metrics Explorer são derivadas do intervalo entre as chamadas de início e de paragem. Cabe-te a ti qual é o intervalo que realmente tens tempo.

### <a name="page-telemetry-in-analytics"></a>Telemetria de página em Analytics

No [Analytics,](../log-query/log-query-overview.md) duas tabelas mostram dados das operações do navegador:

* A `pageViews` tabela contém dados sobre o URL e o título da página
* A `browserTimings` tabela contém dados sobre o desempenho do cliente, como o tempo necessário para processar os dados de entrada

Para saber quanto tempo o navegador demora a processar diferentes páginas:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Para descobrir as popularidades de diferentes navegadores:

```kusto
pageViews
| summarize count() by client_Browser
```

Para associar as vistas de página às chamadas AJAX, junte-se às dependências:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

O servidor SDK utiliza TrackRequest para registar pedidos HTTP.

Também pode chamá-lo se quiser simular pedidos num contexto em que não tenha o módulo de serviço web em funcionamento.

No entanto, a forma recomendada de enviar telemetria de pedido é quando o pedido funciona como <a href="#operation-context">um contexto de operação</a>.

## <a name="operation-context"></a>Contexto de operação

Pode correlacionar os itens de telemetria em conjunto associando-os ao contexto de funcionamento. O módulo padrão de rastreio de pedidos faz isso para exceções e outros eventos que são enviados enquanto um pedido HTTP está sendo processado. Em [Search](./diagnostic-search.md) and [Analytics,](../log-query/log-query-overview.md)pode facilmente encontrar quaisquer eventos associados ao pedido utilizando o seu ID de funcionamento.

Consulte [a correlação de telemetria em Insights de Aplicação](./correlation.md) para obter mais detalhes sobre a correlação.

Ao rastrear a telemetria manualmente, a forma mais fácil de garantir a correlação de telemetria utilizando este padrão:

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...

    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

Juntamente com a definição de um contexto de operação, `StartOperation` cria um item de telemetria do tipo que especifica. Envia o item da telemetria quando elimina a operação, ou se ligar explicitamente `StopOperation` . Se utilizar `RequestTelemetry` como tipo de telemetria, a sua duração é definida para o intervalo cronometrado entre o início e a paragem.

Os artigos de telemetria relatados no âmbito de uma operação tornam-se "crianças" de tal operação. Os contextos de operação podem ser aninhados.

Em Busca, o contexto de operação é utilizado para criar a lista **de Itens Relacionados:**

![Itens relacionados](./media/api-custom-events-metrics/21.png)

Consulte [as operações personalizadas track com Application Insights .NET SDK](./custom-operations-tracking.md) para obter mais informações sobre o rastreio de operações personalizadas.

### <a name="requests-in-analytics"></a>Pedidos em Analytics

No [Application Insights Analytics,](../log-query/log-query-overview.md)os pedidos aparecem na `requests` tabela.

Se [a amostragem](./sampling.md) estiver em funcionamento, a propriedade itemCount mostrará um valor superior a 1. Por exemplo, o artigoCount==10 significa que de 10 chamadas para rastrear o Request(), o processo de amostragem apenas transmitiu uma delas. Para obter uma contagem correta de pedidos e duração média segmentada por nomes de pedido, utilize código como:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Envie exceções aos Insights de Aplicação:

* Para [contá-los,](../platform/metrics-charts.md)como indicação da frequência de um problema.
* Examinar [ocorrências individuais.](./diagnostic-search.md)

Os relatórios incluem os vestígios da pilha.

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException(ex);
}
```

*Node.js*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

Os SDKs apanham muitas exceções automaticamente, por isso nem sempre tens de ligar para o TrackException explicitamente.

* ASP.NET: [Escreva código para apanhar exceções](./asp-net-exceptions.md).
* Java EE: [As exceções são capturadas automaticamente](./java-get-started.md#exceptions-and-request-failures).
* JavaScript: As exceções são capturadas automaticamente. Se pretender desativar a recolha automática, adicione uma linha ao corte de código que insere nas suas páginas web:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Exceções em Analytics

No [Application Insights Analytics,](../log-query/log-query-overview.md)as exceções aparecem na `exceptions` tabela.

Se [a amostragem](./sampling.md) estiver em funcionamento, a `itemCount` propriedade mostra um valor superior a 1. Por exemplo, o itemCount==10 significa que de 10 chamadas para rastrear a Conceção do Rastreio(), o processo de amostragem apenas transmitiu uma delas. Para obter uma contagem correta de exceções segmentadas por tipo de exceção, utilize código como:

```kusto
exceptions
| summarize sum(itemCount) by type
```

A maior parte da informação importante da pilha já está extraída em variáveis separadas, mas pode separar a `details` estrutura para obter mais. Uma vez que esta estrutura é dinâmica, deve lançar o resultado para o tipo que espera. Por exemplo:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Para associar exceções aos seus pedidos relacionados, utilize uma junção:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

Utilize trackTrace para ajudar a diagnosticar problemas enviando um "rasto de migalhas de pão" para a Application Insights. Pode enviar pedaços de dados de diagnóstico e inspecioná-los na [Pesquisa de Diagnóstico.](./diagnostic-search.md)

Nos [adaptadores](./asp-net-trace-logs.md) de registo .NET utilizam esta API para enviar registos de terceiros para o portal.

Em Java para [madeireiros standard como Log4J, Logback](./java-trace-logs.md) use Application Insights Log4j ou Logback Appenders para enviar registos de terceiros para o portal.

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.js*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

*JavaScript do lado do cliente/navegador*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: SeverityLevel)
```

Faça um registo de um evento de diagnóstico como entrar ou sair de um método.

 Parâmetro | Descrição
---|---
`message` | Dados de diagnóstico. Pode ser muito mais do que um nome.
`properties` | Mapa da corda para cadeia: Dados adicionais usados para [filtrar exceções](#properties) no portal. Incumprimentos para esvaziar.
`severityLevel` | Valores suportados: [SeverityLevel.ts](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/shared/AppInsightsCommon/src/Interfaces/Contracts/Generated/SeverityLevel.ts)

Pode pesquisar o conteúdo da mensagem, mas (ao contrário dos valores de propriedade) não pode filtrar nele.

O limite de tamanho `message` é muito superior ao limite de propriedades.
Uma vantagem do TrackTrace é que pode colocar dados relativamente longos na mensagem. Por exemplo, pode codificar os dados POST lá.  

Além disso, pode adicionar um nível de gravidade à sua mensagem. E, tal como outras telemetrias, pode adicionar valores de propriedade para ajudá-lo a filtrar ou procurar diferentes conjuntos de vestígios. Por exemplo:

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

Em [Search,](./diagnostic-search.md)pode então filtrar facilmente todas as mensagens de um determinado nível de gravidade que se relacionam com uma determinada base de dados.

### <a name="traces-in-analytics"></a>Vestígios em Analytics

No [Application Insights Analytics,](../log-query/log-query-overview.md)as chamadas para TrackTrace aparecem na `traces` tabela.

Se [a amostragem](./sampling.md) estiver em funcionamento, a propriedade itemCount mostra um valor superior a 1. Por exemplo, o artigoCount==10 significa que de 10 chamadas `trackTrace()` para, o processo de amostragem apenas transmitiu uma delas. Para obter uma contagem correta de chamadas de traços, deve utilizar, portanto, código como `traces | summarize sum(itemCount)` .

## <a name="trackdependency"></a>Acompanhamento

Utilize a chamada TrackDependency para acompanhar os tempos de resposta e as taxas de sucesso das chamadas para uma peça de código externa. Os resultados aparecem nos gráficos de dependência do portal. O código abaixo precisa de ser adicionado onde quer que seja feita uma chamada de dependência.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
catch(Exception ex) 
{
    success = false;
    telemetry.TrackException(ex);
    throw new Exception("Operation went wrong", ex);
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("DependencyType", "myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```java
boolean success = false;
Instant startTime = Instant.now();
try {
    success = dependency.call();
}
finally {
    Instant endTime = Instant.now();
    Duration delta = Duration.between(startTime, endTime);
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    RemoteDependencyTelemetry.setTimeStamp(startTime);
    RemoteDependencyTelemetry.trackDependency(dependencyTelemetry);
}
```

*Node.js*

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

Lembre-se que os SDKs do servidor incluem um [módulo de dependência](./asp-net-dependencies.md) que descobre e rastreia certas chamadas de dependência automaticamente -- por exemplo, para bases de dados e APIs REST. Tem de instalar um agente no seu servidor para que o módulo funcione. 

Em Java, certas chamadas de dependência podem ser rastreadas automaticamente usando [o Agente Java.](./java-agent.md)

Use esta chamada se quiser rastrear chamadas que o rastreio automático não apanha, ou se não quiser instalar o agente.

Para desligar o módulo padrão de rastreio de dependência em C#, edite [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) e elimine a referência a `DependencyCollector.DependencyTrackingTelemetryModule` . Em Java, por favor, não instale o agente java se não quiser recolher automaticamente as dependências padrão.

### <a name="dependencies-in-analytics"></a>Dependências em Analytics

No [Application Insights Analytics,](../log-query/log-query-overview.md)as chamadas de rastreiodependência aparecem na `dependencies` tabela.

Se [a amostragem](./sampling.md) estiver em funcionamento, a propriedade itemCount mostra um valor superior a 1. Por exemplo, o artigoCount==10 significa que de 10 chamadas para rastrear a Independência(), o processo de amostragem apenas transmitiu uma delas. Para obter uma contagem correta de dependências segmentadas por componente-alvo, utilize código como:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Para associar dependências com os seus pedidos relacionados, utilize uma junção:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Dados de descarga

Normalmente, o SDK envia dados em intervalos fixos (normalmente 30 segundos) ou sempre que o tampão esteja cheio (normalmente 500 itens). No entanto, em alguns casos, é melhor descarregar o tampão - por exemplo, se estiver a utilizar o SDK numa aplicação que se desliga.

*C#*

 ```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.js*

```javascript
telemetry.flush();
```

A função é assíncronia para o [canal de telemetria](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/)do servidor .

Idealmente, o método flush() deve ser utilizado na atividade de paragem da Aplicação.

## <a name="authenticated-users"></a>Utilizadores autenticados

Numa aplicação web, os utilizadores são (por padrão) [identificados por cookies.](./usage-segmentation.md#the-users-sessions-and-events-segmentation-tool) Um utilizador pode ser contado mais de uma vez se aceder à sua aplicação a partir de uma máquina ou navegador diferente, ou se eliminar cookies.

Se os utilizadores iniciarem sposição na sua aplicação, poderá obter uma contagem mais precisa definindo o ID do utilizador autenticado no código do navegador:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

Numa aplicação de MVC web ASP.NET, por exemplo:

*Navalha*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

Não é necessário usar o nome de inscrição real do utilizador. Só tem de ser um ID único para esse utilizador. Não deve incluir espaços ou nenhum dos `,;=|` caracteres.

O ID do utilizador também é definido num cookie de sessão e enviado para o servidor. Se o servidor SDK for instalado, o ID do utilizador autenticado é enviado como parte das propriedades de contexto tanto da telemetria do cliente como do servidor. Em seguida, pode filtrar e pesquisar.

Se a sua aplicação agru tiver em conta os utilizadores, também pode passar um identificador para a conta (com as mesmas restrições de caracteres).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

No [Metrics Explorer,](../platform/metrics-charts.md)pode criar um gráfico que conta os **Utilizadores, autenticados**e **as contas do Utilizador.**

Também pode [pesquisar](./diagnostic-search.md) pontos de dados do cliente com nomes de utilizadores e contas específicos.

## <a name="filtering-searching-and-segmenting-your-data-by-using-properties"></a><a name="properties"></a>Filtrar, pesquisar e segmentar os seus dados utilizando propriedades

Pode anexar propriedades e medições aos seus eventos (e também a métricas, vistas de página, exceções e outros dados de telemetria).

*As propriedades* são valores de cadeia que pode utilizar para filtrar a sua telemetria nos relatórios de utilização. Por exemplo, se a tua aplicação fornecer vários jogos, podes anexar o nome do jogo a cada evento para que possas ver quais os jogos mais populares.

Há um limite de 8192 no comprimento das cordas. (Se pretender enviar grandes pedaços de dados, utilize o parâmetro de mensagem trackTrace.)

*As métricas* são valores numéricos que podem ser apresentados graficamente. Por exemplo, talvez queiras ver se há um aumento gradual nas pontuações que os teus jogadores conseguem. Os gráficos podem ser segmentados pelas propriedades que são enviadas com o evento, para que possa obter gráficos separados ou empilhados para diferentes jogos.

Para que os valores métricos sejam corretamente apresentados, devem ser superiores ou iguais a 0.

Existem [alguns limites no número de propriedades, valores de propriedade e métricas](#limits) que você pode usar.

*JavaScript*

```javascript
appInsights.trackEvent
    ("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );

appInsights.trackPageView
    ("page name", "http://fabrikam.com/pageurl.html",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.js*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> Tome cuidado para não registar informações pessoalmente identificáveis em propriedades.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Forma alternativa de definir propriedades e métricas

Se for mais conveniente, pode recolher os parâmetros de um evento num objeto separado:

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Não reutilize a mesma instância de artigo de telemetria `event` (neste exemplo) para ligar para track*() várias vezes. Isto pode fazer com que a telemetria seja enviada com uma configuração incorreta.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Medições e propriedades personalizadas em Analytics

Em [Analytics,](../log-query/log-query-overview.md)as métricas e propriedades personalizadas mostram nos `customMeasurements` atributos e `customDimensions` atributos de cada registo de telemetria.

Por exemplo, se adicionou uma propriedade chamada "jogo" à sua telemetria de pedido, esta consulta conta as ocorrências de diferentes valores de "jogo", e mostra a média da métrica personalizada "score":

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Repare que:

* Quando extrai um valor das medidas personalizadas JSON, tem um tipo dinâmico, pelo que deve lanhá-lo `tostring` ou `todouble` .
* Para ter em conta a possibilidade de [amostragem,](./sampling.md)deve `sum(itemCount)` utilizar, não `count()` .

## <a name="timing-events"></a><a name="timed"></a> Eventos de cronometragem

Às vezes, queremos traçar o tempo que demora a realizar uma ação. Por exemplo, podes querer saber quanto tempo os utilizadores demoram a considerar escolhas num jogo. Pode utilizar o parâmetro de medição para isto.

*C#*

```csharp
var stopwatch = System.Diagnostics.Stopwatch.StartNew();

// ... perform the timed action ...

stopwatch.Stop();

var metrics = new Dictionary <string, double>
    {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

// Set up some properties:
var properties = new Dictionary <string, string>
    {{"signalSource", currentSignalSource.Name}};

// Send the event:
telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", (double)endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="default-properties-for-custom-telemetry"></a><a name="defaults"></a>Propriedades predefinidos para telemetria personalizada

Se quiser definir valores de propriedade predefinidos para alguns dos eventos personalizados que escreve, pode defini-los num caso de TelemetriaClient. Estão ligados a todos os artigos de telemetria que são enviados daquele cliente.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.GlobalProperties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.GlobalProperties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...


TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.js*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

As chamadas individuais de telemetria podem sobrepor-se aos valores predefinidos nos seus dicionários de propriedade.

*Para clientes web JavaScript,* utilize os inicializadores de telemetria JavaScript.

*Para adicionar propriedades a toda a telemetria,* incluindo os dados dos módulos de recolha padrão, [implemente `ITelemetryInitializer` ](./api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Amostragem, filtragem e processamento da telemetria

Pode escrever código para processar a sua telemetria antes de ser enviada do SDK. O processamento inclui dados enviados a partir dos módulos de telemetria padrão, tais como a recolha de pedidos HTTP e a recolha de dependência.

[Adicione propriedades](./api-filtering-sampling.md#add-properties) à telemetria através da implementação. `ITelemetryInitializer` Por exemplo, pode adicionar números de versão ou valores que são calculados a partir de outras propriedades.

[A filtragem](./api-filtering-sampling.md#filtering) pode modificar ou descartar a telemetria antes de ser enviada a partir do SDK através da implementação `ITelemetryProcessor` . Controla o que é enviado ou descartado, mas tem de ter em conta o efeito nas suas métricas. Dependendo da forma como deita fora os itens, pode perder a capacidade de navegar entre itens relacionados.

[A amostragem](./api-filtering-sampling.md) é uma solução embalada para reduzir o volume de dados que é enviado da sua app para o portal. Fá-lo sem afetar as métricas apresentadas. E fá-lo sem afetar a sua capacidade de diagnosticar problemas navegando entre itens relacionados, tais como exceções, pedidos e visualizações de página.

[Saiba mais](./api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Telemetria incapacitante

Para *parar e iniciar dinamicamente* a recolha e transmissão de telemetria:

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

Para *desativar os colecionadores padrão selecionados*-- por exemplo, contadores de desempenho, pedidos HTTP ou dependências -- elimine ou comente as linhas relevantes em [ApplicationInsights.config](./configuration-with-applicationinsights-config.md). Pode fazê-lo, por exemplo, se quiser enviar os seus próprios dados trackRequest.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Para *desativar os colecionadores padrão selecionados*-- por exemplo, contadores de desempenho, pedidos HTTP ou dependências -- no tempo de inicialização, métodos de configuração em cadeia para o seu código de inicialização SDK:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Para desativar estes colecionadores após a inicialização, utilize o objeto de configuração: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="developer-mode"></a><a name="debug"></a>Modo de desenvolvimento

Durante a depuragem, é útil ter a sua telemetria acelerada através do oleoduto para que possa ver os resultados imediatamente. Também recebe mensagens adicionais que o ajudam a rastrear qualquer problema com a telemetria. Desligue-o em produção, porque pode abrandar a sua aplicação.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

Para Node.js, pode ativar o modo de desenvolvimento, ativando a verificação interna através `setInternalLogging` e definição `maxBatchSize` para 0, o que faz com que a sua telemetria seja enviada assim que é recolhida.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="setting-the-instrumentation-key-for-selected-custom-telemetry"></a><a name="ikey"></a> Definição da chave de instrumentação para telemetria personalizada selecionada

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Chave de instrumentação dinâmica

Para evitar misturar telemetria a partir de ambientes de desenvolvimento, teste e produção, pode [criar recursos separados de Insights de Aplicação](./create-new-resource.md) e alterar as suas chaves, dependendo do ambiente.

Em vez de obter a chave de instrumentação do ficheiro de configuração, pode defini-la no seu código. Desa parte num método de inicialização, como global.aspx.cs num serviço ASP.NET:

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

Nas páginas web, é melhor defini-lo a partir do estado do servidor web, em vez de codigem-lo literalmente no script. Por exemplo, numa página web gerada numa aplicação ASP.NET:

*JavaScript em Razor*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="telemetrycontext"></a>TelemetriaContexto

O TelemetriaClient tem uma propriedade Context, que contém valores que são enviados juntamente com todos os dados da telemetria. Normalmente são definidos pelos módulos de telemetria padrão, mas também pode defini-los. Por exemplo:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Se você mesmo definir algum destes valores, considere remover a linha relevante de [ApplicationInsights.config](./configuration-with-applicationinsights-config.md), para que os seus valores e valores padrão não se confundam.

* **Componente**: A aplicação e a sua versão.
* **Dispositivo**: Dados sobre o dispositivo onde a aplicação está em execução. (Nas aplicações web, este é o servidor ou dispositivo cliente de onde a telemetria é enviada.)
* **InstrumentaçãoKey**: O recurso Application Insights em Azure onde aparece a telemetria. É geralmente recolhido de ApplicationInsights.config.
* **Localização**: A localização geográfica do dispositivo.
* **Funcionamento**: Em aplicações web, o atual pedido HTTP. Em outros tipos de aplicativos, você pode definir isto para agrupar eventos em conjunto.
  * **ID**: Um valor gerado que correlaciona diferentes eventos, para que quando inspecione qualquer evento na Pesquisa de Diagnóstico, possa encontrar itens relacionados.
  * **Nome**: Um identificador, normalmente o URL do pedido HTTP.
  * **SintéticoSource**: Se não for nulo ou vazio, uma corda que indique que a origem do pedido foi identificada como um robô ou teste web. Por padrão, é excluído dos cálculos no Metrics Explorer.
* **Propriedades**: Propriedades que são enviadas com todos os dados de telemetria. Pode ser ultrapassado em chamadas individuais track*.
* **Sessão**: Sessão do utilizador. O ID é definido para um valor gerado, que é alterado quando o utilizador não está ativo há algum tempo.
* **Utilizador**: Informações do utilizador.

## <a name="limits"></a>Limites

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Para evitar atingir o limite da taxa de dados, utilize [amostragem](./sampling.md).

Para determinar a duração dos dados, consulte [a retenção de dados e a privacidade.](./data-retention-privacy.md)

## <a name="reference-docs"></a>Documentos de referência

* [referência ASP.NET](/dotnet/api/overview/azure/insights?view=azure-dotnet)
* [Java reference](/java/api/overview/azure/appinsights?view=azure-java-stable/) (Referência de Java)
* [Referência JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)

## <a name="sdk-code"></a>Código do SDK 

* [SDK de ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Pacotes windows server](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [SDK Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [SDK JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="questions"></a>Perguntas

* *Que exceções podem Track_ chamadas lançadas?*

    Nenhum. Não precisas de os embrulhar em cláusulas de captura. Se o SDK encontrar problemas, registará mensagens na saída da consola de depurado e- se as mensagens chegarem através da pesquisa de diagnóstico.
* *Existe uma API REST para obter dados do portal?*

    Sim, os [dados acedem à API.](https://dev.applicationinsights.io/) Outras formas de extrair dados incluem [a exportação da Analytics para o Power BI](./export-power-bi.md) e a [exportação contínua.](./export-telemetry.md)

## <a name="next-steps"></a><a name="next"></a>Passos seguintes

* [Eventos de pesquisa e registos](./diagnostic-search.md)
* [Resolução de problemas](../faq.md)

