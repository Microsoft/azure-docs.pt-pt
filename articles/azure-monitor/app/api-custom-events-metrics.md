---
title: Aplicação Insights API para eventos e métricas personalizados / Microsoft Docs
description: Insira algumas linhas de código no seu dispositivo ou na aplicação de desktop, página web ou serviço, para rastrear problemas de utilização e diagnóstico.
ms.topic: conceptual
ms.date: 03/27/2019
ms.openlocfilehash: d6cb2f5ab418e8d3b5935fef535565ccf55a3906
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536952"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>API do Application Insights para métricas e eventos personalizados

Insira algumas linhas de código na sua aplicação para saber o que os utilizadores estão a fazer com ela, ou para ajudar a diagnosticar problemas. Pode enviar telemetria a partir de aplicações de dispositivos e desktop, clientes web e servidores web. Utilize a API core telemetria de insights de [aplicação Azure](../../azure-monitor/app/app-insights-overview.md) para enviar eventos e métricas personalizados, e as suas próprias versões de telemetria padrão. Esta API é a mesma API que os colecionadores de dados de Aplicação Padrão Insights utilizam.

## <a name="api-summary"></a>Resumo da API

A API central é uniforme em todas as plataformas, além de algumas variações como `GetMetric`(apenas NET).

| Método | Utilizado para |
| --- | --- |
| [`TrackPageView`](#page-views) |Páginas, ecrãs, lâminas ou formulários. |
| [`TrackEvent`](#trackevent) |Ações de utilizador e outros eventos. Usado para monitorizar o comportamento do utilizador ou para monitorizar o desempenho. |
| [`GetMetric`](#getmetric) |Métricas zero e multidimensionais, agregação configurada centralmente, apenas C# |
| [`TrackMetric`](#trackmetric) |Medições de desempenho tais como comprimentos de fila não relacionados com eventos específicos. |
| [`TrackException`](#trackexception) |Exceções para o diagnóstico. Trace onde ocorrem em relação a outros eventos e examine os vestígios da pilha. |
| [`TrackRequest`](#trackrequest) |Registando a frequência e duração dos pedidos do servidor para análise de desempenho. |
| [`TrackTrace`](#tracktrace) |Mensagens de registo de diagnóstico de recursos. Também pode capturar registos de terceiros. |
| [`TrackDependency`](#trackdependency) |Registando a duração e frequência das chamadas para componentes externos de que a sua aplicação depende. |

Pode [anexar propriedades e métricas](#properties) à maioria destas chamadas de telemetria.

## <a name="before-you-start"></a><a name="prep"></a>Antes de começar

Se ainda não tiver uma referência no SDK de Insights de Aplicação:

* Adicione o SDK de Insights de Aplicação ao seu projeto:

  * [projeto ASP.NET](../../azure-monitor/app/asp-net.md)
  * [projeto ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
  * [Projeto Java](../../azure-monitor/app/java-get-started.md)
  * [Projeto Node.js](../../azure-monitor/app/nodejs.md)
  * [JavaScript em cada página web](../../azure-monitor/app/javascript.md) 
* No seu dispositivo ou código de servidor web, inclua:

    *C#:*`using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:*`import com.microsoft.applicationinsights.TelemetryClient;`

    *Nó.js:*`var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Obtenha uma instância telemetriaClient

Obtenha uma `TelemetryClient` instância de (exceto no JavaScript nas páginas web):

Para ASP.NET aplicações [Core](asp-net-core.md#how-can-i-track-telemetry-thats-not-automatically-collected) e Non HTTP/Worker para as aplicações [.NET/.NET Core,](worker-service.md#how-can-i-track-telemetry-thats-not-automatically-collected) recomenda-se obter uma instância do recipiente de injeção de `TelemetryClient` dependência, conforme explicado na respetiva documentação.

Se utilizar o AzureFunctions v2+ ou o Azure WebJobs v3+ - siga este documento:https://docs.microsoft.com/azure/azure-functions/functions-monitoring#version-2x-and-higher

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```
Para quem vê este método são mensagens obsoletas, visite [microsoft/ApplicationInsights-dotnet#1152](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1152) para mais detalhes.

*Básico Visual*

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

TelemetriaClient é seguro para fios.

Para ASP.NET e java projetos, os pedidos http estão automaticamente capturados. É melhor criar instâncias adicionais de TelemettryClient para outro módulo da sua aplicação. Por exemplo, pode ter uma instância telemetriaClient na sua classe de middleware para reportar eventos de lógica empresarial. Pode definir propriedades como UserId e DeviceId para identificar a máquina. Esta informação está anexada a todos os eventos que a instância envia.

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

Nos projetos Node.js, `new applicationInsights.TelemetryClient(instrumentationKey?)` você pode usar para criar uma nova instância, mas isso `defaultClient`é recomendado apenas para cenários que requerem configuração isolada do singleton .

## <a name="trackevent"></a>TrackEvent

Em Application Insights, um *evento personalizado* é um ponto de dados que pode exibir no [Metrics Explorer](../../azure-monitor/platform/metrics-charts.md) como uma contagem agregada, e em Pesquisa de [Diagnóstico](../../azure-monitor/app/diagnostic-search.md) como ocorrências individuais. (Não está relacionado com MVC ou outros "eventos" de "eventos.")

Insira `TrackEvent` chamadas no seu código para contar vários eventos. Quantas vezes os utilizadores escolhem uma determinada funcionalidade, quantas vezes alcançam determinados objetivos, ou talvez com que frequência cometem erros específicos.

Por exemplo, numa aplicação de jogo, envie um evento sempre que um utilizador ganhar o jogo:

*JavaScript*

```javascript
appInsights.trackEvent({name:"WinGame"});
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Básico Visual*

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

A telemetria está `customEvents` disponível na tabela em [Application Insights Analytics](analytics.md). Cada linha representa uma `trackEvent(..)` chamada para a sua aplicação.

Se a [amostragem](../../azure-monitor/app/sampling.md) estiver em funcionamento, a propriedade do Conde mostra um valor superior a 1. Por exemplo, o itemCount==10 significa que de 10 chamadas para trackEvent(), o processo de amostragem apenas transmitiu uma delas. Para obter uma contagem correta de eventos personalizados, deve, portanto, utilizar códigos como `customEvents | summarize sum(itemCount)`.

## <a name="getmetric"></a>GetMetric

### <a name="examples"></a>Exemplos

*C#*

```csharp
namespace User.Namespace.Example01
{
    using System;
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    /// <summary>
    /// Most simple cases are one-liners.
    /// This is all possible without even importing an additional namespace.
    /// </summary>

    public class Sample01
    {
        /// <summary />
        public static void Exec()
        {
            // *** SENDING METRICS ***

            // Recall how you send custom telemetry with Application Insights in other cases, e.g. Events.
            // The following will result in an EventTelemetry object to be sent to the cloud right away.
            TelemetryClient client = new TelemetryClient();
            client.TrackEvent("SomethingInterestingHappened");

            // Metrics work very similar. However, the value is not sent right away.
            // It is aggregated with other values for the same metric, and the resulting summary (aka "aggregate" is sent automatically every minute.
            // To mark this difference, we use a pattern that is similar, but different from the established TrackXxx(..) pattern that sends telemetry right away:

            client.GetMetric("CowsSold").TrackValue(42);

            // *** MULTI-DIMENSIONAL METRICS ***

            // The above example shows a zero-dimensional metric.
            // Metrics can also be multi-dimensional.
            // In the initial version we are supporting up to 2 dimensions, and we will add support for more in the future as needed.
            // Here is an example for a one-dimensional metric:

            Metric animalsSold = client.GetMetric("AnimalsSold", "Species");

            animalsSold.TrackValue(42, "Pigs");
            animalsSold.TrackValue(24, "Horses");

            // The values for Pigs and Horses will be aggregated separately from each other and will result in two distinct aggregates.
            // You can control the maximum number of number data series per metric (and thus your resource usage and cost).
            // The default limits are no more than 1000 total data series per metric, and no more than 100 different values per dimension.
            // We discuss elsewhere how to change them.
            // We use a common .NET pattern: TryXxx(..) to make sure that the limits are observed.
            // If the limits are already reached, Metric.TrackValue(..) will return False and the value will not be tracked. Otherwise it will return True.
            // This is particularly useful if the data for a metric originates from user input, e.g. a file:

            Tuple<int, string> countAndSpecies = ReadSpeciesFromUserInput();
            int count = countAndSpecies.Item1;
            string species = countAndSpecies.Item2;

            if (!animalsSold.TrackValue(count, species))

            {
                client.TrackTrace($"Data series or dimension cap was reached for metric {animalsSold.Identifier.MetricId}.", SeverityLevel.Error);
            }

            // You can inspect a metric object to reason about its current state. For example:
            int currentNumberOfSpecies = animalsSold.GetDimensionValues(1).Count;
        }

        private static void ResetDataStructure()
        {
            // Do stuff
        }

        private static Tuple<int, string> ReadSpeciesFromUserInput()
        {
            return Tuple.Create(18, "Cows");
        }

        private static int AddItemsToDataStructure()
        {
            // Do stuff
            return 5;
        }
    }
}
```

## <a name="trackmetric"></a>TrackMétrico

> [!NOTE]
> Microsoft.ApplicationInsights.TelemettryClient.TrackMetric não é o método preferido para o envio de métricas. As métricas devem ser sempre pré-agregadas num período de tempo antes de serem enviadas. Utilize uma das sobrecargas GetMetric(..) para obter um objeto métrico para aceder às capacidades de pré-agregação de SDK. Se estiver a implementar a sua própria lógica de pré-agregação, pode utilizar o método TrackMetric() para enviar os agregados resultantes. Se a sua aplicação necessitar de enviar um item de telemetria separado em todas as ocasiões sem agregação ao longo do tempo, provavelmente terá um caso de utilização para telemetria de eventos; ver TelemettryClient.TrackEvent (Microsoft.ApplicationInsights.DataContracts.EventTelemettry).

Os Insights de Aplicação podem traçar métricas que não estão ligadas a eventos específicos. Por exemplo, pode monitorizar um comprimento de fila em intervalos regulares. Com as métricas, as medições individuais são de menor interesse do que as variações e tendências, e assim os gráficos estatísticos são úteis.

Para enviar métricas para Application Insights, `TrackMetric(..)` pode utilizar a API. Há duas maneiras de enviar uma métrica:

* Um único valor. Sempre que efetua uma medição na sua aplicação, envia o valor correspondente para O Insights de Aplicação. Por exemplo, assuma que tem uma métrica descrevendo o número de itens num recipiente. Durante um determinado período de tempo, coloca-se primeiro três itens no recipiente e depois remove-se dois itens. Assim, ligaria `TrackMetric` duas vezes: `3` primeiro passando `-2`o valor e depois o valor. Application Insights armazena ambos os valores em seu nome.

* Agregação. Quando se trabalha com métricas, cada medição raramente é interessante. Em vez disso, é importante um resumo do que aconteceu durante um determinado período de tempo. Tal resumo chama-se _agregação._ No exemplo acima, a soma métrica agregada para esse período `1` `2`é e a contagem dos valores métricos é . Ao utilizar a abordagem de agregação, basta invocar `TrackMetric` uma vez por período de tempo e enviar os valores agregados. Esta é a abordagem recomendada, uma vez que pode reduzir significativamente o custo e a sobrecarga de desempenho enviando menos pontos de dados para a Application Insights, ao mesmo tempo que recolhe todas as informações relevantes.

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

A telemetria está `customMetrics` disponível na tabela em [Application Insights Analytics](analytics.md). Cada linha representa uma `trackMetric(..)` chamada para a sua aplicação.

* `valueSum`- Esta é a soma das medições. Para obter o valor `valueCount`médio, divida por .
* `valueCount`- O número de medições que `trackMetric(..)` foram agregadas nesta chamada.

## <a name="page-views"></a>Vistas da página

Numa aplicação de dispositivo ou página web, a telemetria de visualização de página é enviada por padrão quando cada ecrã ou página é carregado. Mas pode alterar isso para rastrear as visualizações da página em momentos adicionais ou diferentes. Por exemplo, numa aplicação que exibe separadores ou lâminas, é possível que queira rastrear uma página sempre que o utilizador abre uma nova lâmina.

Os dados do utilizador e da sessão são enviados como propriedades juntamente com as visualizações da página, para que os gráficos de utilizador e sessão se alegrem quando há telemetria de visualização de página.

### <a name="custom-page-views"></a>Vistas de página personalizadas

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Básico Visual*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

Se tiver vários separadores em diferentes páginas html, também pode especificar o URL:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Visualizações da página de cronometragem

Por predefinição, os tempos reportados como tempo de carregamento de **visualização** da página são medidos a partir do momento em que o navegador envia o pedido, até que o evento de carga de página do navegador seja chamado.

Em vez disso, pode:

* Delineie uma duração explícita `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`na chamada pageview da [faixa:](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/API.md#trackpageview) .
* Utilize as chamadas `startTrackPage` de `stopTrackPage`tempo da visualização da página e .

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

O nome que usa como primeiro parâmetro associa as chamadas de início e paragem. Falha no nome da página atual.

As durações de carga da página resultantes apresentadas no Metrics Explorer são derivadas do intervalo entre as chamadas de início e paragem. Cabe-te a ti o intervalo que realmente tens.

### <a name="page-telemetry-in-analytics"></a>Telemetria de página em Analytics

No [Analytics](analytics.md) duas tabelas mostram dados das operações do navegador:

* A `pageViews` tabela contém dados sobre o URL e o título da página
* A `browserTimings` tabela contém dados sobre o desempenho do cliente, tais como o tempo que se demorou a processar os dados que chegam

Para saber quanto tempo o navegador demora a processar diferentes páginas:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Para descobrir as popularidades dos diferentes navegadores:

```kusto
pageViews
| summarize count() by client_Browser
```

Para associar as vistas da página às chamadas do AJAX, junte-se a dependências:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>Pedido de rastreio

O servidor SDK utiliza o TrackRequest para registar pedidos HTTP.

Também pode chamá-lo você mesmo se quiser simular pedidos num contexto em que não tem o módulo de serviço web em funcionamento.

No entanto, a forma recomendada de enviar telemetria de pedido é quando o pedido funciona como <a href="#operation-context">um contexto</a>de operação .

## <a name="operation-context"></a>Contexto de funcionamento

Pode correlacionar itens de telemetria, associando-os ao contexto de funcionamento. O módulo padrão de rastreio de pedidos faz isto para exceções e outros eventos que são enviados enquanto um pedido HTTP está sendo processado. Em [Search](../../azure-monitor/app/diagnostic-search.md) and [Analytics,](analytics.md)pode facilmente encontrar quaisquer eventos associados ao pedido utilizando o seu ID de operação.

Consulte a [correlação de Telemetria em Insights de Aplicação](../../azure-monitor/app/correlation.md) para obter mais detalhes sobre a correlação.

Ao rastrear a telemetria manualmente, a maneira mais fácil de garantir a correlação de telemetria utilizando este padrão:

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

Juntamente com a `StartOperation` definição de um contexto de funcionamento, cria um item de telemetria do tipo que especifica. Envia o item de telemetria quando elimina a operação, ou se ligar `StopOperation`explicitamente . Se utilizar `RequestTelemetry` como tipo de telemetria, a sua duração é definida no intervalo cronometrado entre o início e a paragem.

Os artigos de telemetria relatados no âmbito da operação tornam-se "crianças" de tal operação. Os contextos de funcionamento podem ser aninhados.

Em Pesquisa, o contexto de operação é usado para criar a lista de **Itens Relacionados:**

![Itens relacionados](./media/api-custom-events-metrics/21.png)

Consulte [as operações personalizadas do Track com Application Insights .NET SDK](../../azure-monitor/app/custom-operations-tracking.md) para obter mais informações sobre o rastreio de operações personalizadas.

### <a name="requests-in-analytics"></a>Pedidos em Analytics

Na [Aplicação Insights Analytics,](analytics.md)os `requests` pedidos aparecem na tabela.

Se a [amostragem](../../azure-monitor/app/sampling.md) estiver em funcionamento, a propriedade do ItemCount mostrará um valor superior a 1. Por exemplo, o itemCount==10 significa que de 10 chamadas para rastrearPedido(), o processo de amostragem apenas transmitiu uma delas. Para obter uma contagem correta de pedidos e duração média segmentada por nomes de pedido, utilize código como:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Enviar exceções aos Insights de Aplicação:

* Para [contá-las,](../../azure-monitor/platform/metrics-charts.md)como indicação da frequência de um problema.
* Para [examinar as ocorrências individuais.](../../azure-monitor/app/diagnostic-search.md)

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

Os SDKs apanham automaticamente muitas exceções, pelo que nem sempre é preciso ligar explicitamente para a TrackException.

* ASP.NET: [Escreva código para capturar exceções](../../azure-monitor/app/asp-net-exceptions.md).
* Java EE: [As exceções são apanhadas automaticamente](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures).
* JavaScript: As exceções são apanhadas automaticamente. Se pretender desativar a recolha automática, adicione uma linha ao código que insere nas suas páginas web:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Exceções em Analytics

Na [Aplicação Insights Analytics,](analytics.md)as `exceptions` exceções aparecem na tabela.

Se a [amostragem](../../azure-monitor/app/sampling.md) `itemCount` estiver em funcionamento, a propriedade mostra um valor superior a 1. Por exemplo, o itemCount==10 significa que de 10 chamadas para trackException(), o processo de amostragem apenas transmitiu uma delas. Para obter uma contagem correta de exceções segmentadas por tipo de exceção, utilize código sinuoso como:

```kusto
exceptions
| summarize sum(itemCount) by type
```

A maior parte da informação importante da pilha já está `details` extraída em variáveis separadas, mas você pode desmontar a estrutura para obter mais. Uma vez que esta estrutura é dinâmica, deve lançar o resultado para o tipo que espera. Por exemplo:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Para associar exceções aos seus pedidos relacionados, utilize uma adesão:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

Utilize o TrackTrace para ajudar a diagnosticar problemas enviando um "rasto de migalhas de pão" para a Aplicação Insights. Pode enviar pedaços de dados de diagnóstico e inspecioná-los em [Pesquisa de Diagnóstico](../../azure-monitor/app/diagnostic-search.md).

Em .NET [Os adaptadores](../../azure-monitor/app/asp-net-trace-logs.md) de log utilizam esta API para enviar registos de terceiros para o portal.

Em Java para [madeireiros Standard como o Log4J, o Logback](../../azure-monitor/app/java-trace-logs.md) utiliza o Application Insights Log4j ou os Apendes de Logback para enviar registos de terceiros para o portal.

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

*JavaScript lado cliente/browser*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: SeverityLevel)
```

Faça loga num evento de diagnóstico como entrar ou deixar um método.

 Parâmetro | Descrição
---|---
`message` | Dados de diagnóstico. Pode ser muito mais longo que um nome.
`properties` | Mapa da cadeia para a cadeia: Dados adicionais utilizados para [filtrar exceções](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) no portal. Incumprimentos para esvaziar.
`severityLevel` | Valores suportados: [SeverityLevel.ts](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/shared/AppInsightsCommon/src/Interfaces/Contracts/Generated/SeverityLevel.ts)

Pode pesquisar o conteúdo da mensagem, mas (ao contrário dos valores de propriedade) não pode filtrar nele.

O limite `message` de tamanho é muito superior ao limite de propriedades.
Uma vantagem do TrackTrace é que pode colocar dados relativamente longos na mensagem. Por exemplo, pode codificar os dados do POST.  

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

Em [Search,](../../azure-monitor/app/diagnostic-search.md)pode filtrar facilmente todas as mensagens de um determinado nível de gravidade que se relacionam com uma determinada base de dados.

### <a name="traces-in-analytics"></a>Vestígios em Analytics

Na [Aplicação Insights Analytics,](analytics.md)as chamadas `traces` para trackTrace aparecem na tabela.

Se a [amostragem](../../azure-monitor/app/sampling.md) estiver em funcionamento, a propriedade do Conde mostra um valor superior a 1. Por exemplo, o itemCount==10 significa `trackTrace()`que de 10 chamadas para , o processo de amostragem apenas transmitiu uma delas. Para obter uma contagem correta de chamadas de rastreio, deve utilizar, portanto, código como `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency

Utilize a chamada TrackDependency para acompanhar os tempos de resposta e as taxas de sucesso das chamadas para um código externo. Os resultados aparecem nos gráficos de dependência no portal. O código abaixo precisa de ser adicionado onde quer que seja feita uma chamada de dependência.

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

Lembre-se que os SDKs do servidor incluem um módulo de [dependência](../../azure-monitor/app/asp-net-dependencies.md) que descobre e rastreia automaticamente certas chamadas de dependência - por exemplo, para bases de dados e APIs REST. Tem de instalar um agente no seu servidor para que o módulo funcione. 

Em Java, certas chamadas de dependência podem ser rastreadas automaticamente usando [o Agente Java](../../azure-monitor/app/java-agent.md).

Usa esta chamada se quiser rastrear chamadas que o rastreio automatizado não apanha, ou se não quiser instalar o agente.

Para desligar o módulo padrão de rastreio da dependência em C#, editar `DependencyCollector.DependencyTrackingTelemetryModule` [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) e eliminar a referência a . Em Java, por favor, não instale o agente Java se não quiser recolher as dependências padrão automaticamente.

### <a name="dependencies-in-analytics"></a>Dependências em Analytics

Na [Aplicação Insights Analytics,](analytics.md)as chamadas `dependencies` trackDependency aparecem na tabela.

Se a [amostragem](../../azure-monitor/app/sampling.md) estiver em funcionamento, a propriedade do Conde mostra um valor superior a 1. Por exemplo, o itemCount==10 significa que de 10 chamadas para rastrearA Dependência(), o processo de amostragem apenas transmitiu uma delas. Para obter uma contagem correta de dependências segmentadas por componente-alvo, utilize código sinuoso como:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Para associar dependências com os seus pedidos relacionados, utilize uma adesão:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Dados de descarga

Normalmente, o SDK envia dados em intervalos fixos (tipicamente 30 segundos) ou sempre que o tampão está cheio (normalmente 500 itens). No entanto, em alguns casos, é melhor descarregar o tampão- por exemplo, se estiver a usar o SDK numa aplicação que se desliga.

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

A função é assíncrona para o canal de [telemetria](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/)do servidor .

Idealmente, o método de descarga () deve ser utilizado na atividade de encerramento da Aplicação.

## <a name="authenticated-users"></a>Utilizadores autenticados

Numa aplicação web, os utilizadores são (por padrão) identificados por cookies. Um utilizador pode ser contado mais de uma vez se aceder à sua aplicação a partir de uma máquina ou navegador diferente, ou se apagar cookies.

Se os utilizadores iniciarem sessão na sua aplicação, poderá obter uma contagem mais precisa, definindo o ID do utilizador autenticado no código do navegador:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

Numa aplicação ASP.NET web MVC, por exemplo:

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

Não é necessário usar o nome de inscrição real do utilizador. Só tem de ser um ID que seja exclusivo desse utilizador. Não deve incluir espaços ou `,;=|`qualquer um dos caracteres.

O ID do utilizador também é definido num cookie de sessão e enviado para o servidor. Se o SDK do servidor estiver instalado, o ID do utilizador autenticado é enviado como parte das propriedades de contexto da telemetria do cliente e do servidor. Em seguida, pode filtrar e procurar nele.

Se a sua aplicação agrupa os utilizadores em contas, também pode passar um identificador para a conta (com as mesmas restrições de caracteres).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

No [Metrics Explorer,](../../azure-monitor/platform/metrics-charts.md)pode criar um gráfico que conta com contas de **Utilizadores, Autenticados**e **Utilizadores.**

Também pode [pesquisar](../../azure-monitor/app/diagnostic-search.md) pontos de dados do cliente com nomes e contas de utilizadores específicos.

## <a name="filtering-searching-and-segmenting-your-data-by-using-properties"></a><a name="properties"></a>Filtrar, pesquisar e segmentar os seus dados utilizando propriedades

Pode anexar propriedades e medições aos seus eventos (e também a métricas, vistas de página, exceções e outros dados de telemetria).

*As propriedades* são valores de cordas que pode usar para filtrar a sua telemetria nos relatórios de utilização. Por exemplo, se a sua aplicação fornecer vários jogos, pode anexar o nome do jogo a cada evento para que possa ver quais os jogos mais populares.

Há um limite de 8192 no comprimento das cordas. (Se pretender enviar grandes pedaços de dados, utilize o parâmetro de mensagem do TrackTrace.)

*As métricas* são valores numéricos que podem ser apresentados graficamente. Por exemplo, talvez queiras ver se há um aumento gradual das pontuações que os teus jogadores conseguem. Os gráficos podem ser segmentados pelas propriedades que são enviadas com o evento, para que você possa obter gráficos separados ou empilhados para diferentes jogos.

Para que os valores métricos sejam corretamente apresentados, devem ser maiores ou iguais a 0.

Existem [alguns limites para o número de propriedades, valores de propriedade e métricas](#limits) que você pode usar.

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

*Básico Visual*

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
> Não reutilize a mesma instância de artigo`event` de telemetria (neste exemplo) para ligar várias vezes ao Track*() Isto pode fazer com que a telemetria seja enviada com uma configuração incorreta.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Medições e propriedades personalizadas em Analytics

Em [Analytics,](analytics.md)métricas e propriedades `customMeasurements` `customDimensions` personalizadas mostram nos atributos de cada disco de telemetria.

Por exemplo, se adicionou uma propriedade chamada "game" à sua telemetria de pedido, esta consulta conta as ocorrências de diferentes valores de "jogo", e mostra a média da métrica personalizada "pontuação":

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Note que:

* Quando extrai um valor das Dimensões personalizadas ou personalizados JSON, tem um `tostring` `todouble`tipo dinâmico, pelo que deve lançá-lo ou .
* Para ter em conta a possibilidade `sum(itemCount)`de `count()` [amostragem,](../../azure-monitor/app/sampling.md)deve utilizar, não .

## <a name="timing-events"></a><a name="timed"></a>Eventos de cronometragem

Às vezes queremos traçar o tempo que demora a realizar uma ação. Por exemplo, talvez queira saber quanto tempo os utilizadores demoram a considerar escolhas num jogo. Pode utilizar o parâmetro de medição para isto.

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

## <a name="default-properties-for-custom-telemetry"></a><a name="defaults"></a>Propriedades padrão para telemetria personalizada

Se quiser definir valores de propriedade padrão para alguns dos eventos personalizados que escreve, pode defini-los numa instância TelemettryClient. Estão ligados a todos os artigos de telemetria que são enviados daquele cliente.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.GlobalProperties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Básico Visual*

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

As chamadas de telemetria individuais podem sobrepor-se aos valores padrão nos seus dicionários de propriedade.

*Para clientes web JavaScript,* utilize os iniciais de telemetria JavaScript.

*Para adicionar propriedades a toda a telemetria,* incluindo os dados dos módulos de recolha padrão, [implemente `ITelemetryInitializer` ](../../azure-monitor/app/api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Amostragem, filtragem e telemetria de processamento

Pode escrever código para processar a sua telemetria antes de ser enviada do SDK. O processamento inclui dados enviados a partir dos módulos padrão de telemetria, tais como recolha de pedidos http e recolha de dependência.

[Adicione propriedades](../../azure-monitor/app/api-filtering-sampling.md#add-properties) à telemetria `ITelemetryInitializer`implementando . Por exemplo, pode adicionar números de versão ou valores que são calculados a partir de outras propriedades.

[A filtragem](../../azure-monitor/app/api-filtering-sampling.md#filtering) pode modificar ou descartar a telemetria antes de `ITelemetryProcessor`ser enviada do SDK implementando . Controla o que é enviado ou descartado, mas tem de explicar o efeito nas suas métricas. Dependendo da forma como descarta itens, pode perder a capacidade de navegar entre itens relacionados.

[A amostragem](../../azure-monitor/app/api-filtering-sampling.md) é uma solução embalada para reduzir o volume de dados que é enviado da sua app para o portal. Fá-lo sem afetar as métricas expostas. E fá-lo sem afetar a sua capacidade de diagnosticar problemas navegando entre itens relacionados, como exceções, pedidos e visualizações de páginas.

[Saiba mais](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Telemetria incapacitante

Para *parar e iniciar dinamicamente* a recolha e transmissão da telemetria:

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

Para *desativar os colecionadores padrão selecionados*-- por exemplo, contadores de desempenho, pedidos HTTP ou dependências -- excluir ou comentar as linhas relevantes em [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Pode fazê-lo, por exemplo, se quiser enviar os seus próprios dados do TrackRequest.

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

Para desativar estes colecionadores após a inicialização, utilize o objeto de configuração:`applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="developer-mode"></a><a name="debug"></a>Modo de desenvolvimento

Durante a depuração, é útil ter a sua telemetria acelerada através do oleoduto para que possa ver os resultados imediatamente. Também recebe mensagens adicionais que o ajudam a rastrear qualquer problema com a telemetria. Desligue-o em produção, pois pode abrandar a sua aplicação.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Básico Visual*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

Para node.js, pode ativar o modo `setInternalLogging` de `maxBatchSize` desenvolvimento, permitindo a exploração interna através e a regulação para 0, o que faz com que a sua telemetria seja enviada assim que for recolhida.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="setting-the-instrumentation-key-for-selected-custom-telemetry"></a><a name="ikey"></a>Definição da chave de instrumentação para telemetria personalizada selecionada

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a>Chave de instrumentação dinâmica

Para evitar misturar telemetria a partir de ambientes de desenvolvimento, teste e produção, pode [criar recursos separados](../../azure-monitor/app/create-new-resource.md ) de Insights de Aplicação e alterar as suas chaves, dependendo do ambiente.

Em vez de obter a chave de instrumentação do ficheiro de configuração, pode defini-la no seu código. Desloque a chave num método de inicialização, como global.aspx.cs num serviço ASP.NET:

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

Nas páginas web, é melhor defini-lo a partir do estado do servidor web, em vez de o codificar literalmente no script. Por exemplo, numa página web gerada numa aplicação ASP.NET:

*JavaScript em Navalha*

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

TelemettryClient tem uma propriedade Context, que contém valores que são enviados juntamente com todos os dados de telemetria. São normalmente definidos pelos módulos padrão de telemetria, mas também pode defini-los por si mesmo. Por exemplo:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Se você próprio definir algum destes valores, considere remover a linha relevante do [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), para que os seus valores e valores padrão não fiquem confusos.

* **Componente**: A aplicação e a sua versão.
* **Dispositivo**: Dados sobre o dispositivo onde a aplicação está em execução. (Nas aplicações web, este é o servidor ou dispositivo cliente de que a telemetria é enviada.)
* **InstrumentaçãoChaveChave**: O recurso Application Insights em Azure onde aparece a telemetria. É geralmente recolhido em ApplicationInsights.config.
* **Localização**: A localização geográfica do dispositivo.
* **Funcionamento**: Nas aplicações web, o pedido http atual. Em outros tipos de aplicações, pode definir isto para organizar eventos em conjunto.
  * **ID**: Um valor gerado que correlaciona diferentes eventos, para que, quando inspeciona qualquer evento em Pesquisa de Diagnóstico, possa encontrar itens relacionados.
  * **Nome**: Um identificador, normalmente o URL do pedido HTTP.
  * **SintéticoFonte**: Se não for nulo ou vazio, uma cadeia que indique que a origem do pedido foi identificada como um robô ou um teste web. Por padrão, está excluído dos cálculos no Metrics Explorer.
* **Propriedades**: Propriedades que são enviadas com todos os dados de telemetria. Pode ser ultrapassado em chamadas individuais de Track*.
* **Sessão**: A sessão do utilizador. O ID está definido para um valor gerado, que é alterado quando o utilizador não está ativo há algum tempo.
* **Utilizador**: Informações do utilizador.

## <a name="limits"></a>Limites

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Para evitar atingir o limite da taxa de dados, utilize [amostras.](../../azure-monitor/app/sampling.md)

Para determinar quanto tempo os dados são mantidos, consulte a [retenção de dados e a privacidade.](../../azure-monitor/app/data-retention-privacy.md)

## <a name="reference-docs"></a>Doutorados de referência

* [referência ASP.NET](https://docs.microsoft.com/dotnet/api/overview/azure/insights?view=azure-dotnet)
* [Java reference](https://docs.microsoft.com/java/api/overview/azure/appinsights?view=azure-java-stable/) (Referência de Java)
* [Referência JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)


## <a name="sdk-code"></a>Código do SDK 

* [SDK de ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Pacotes do Servidor do Windows](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [SDK Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [SDK Node.js](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [SDK JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="questions"></a>Perguntas

* *Que exceções podem Track_() chamadas?*

    Nenhum. Não precisas de os embrulhar em cláusulas de tentativa de captura. Se o SDK encontrar problemas, irá registar mensagens na saída da consola de depuração e-- se as mensagens forem através de -- pesquisa de diagnóstico.
* *Existe uma API REST para obter dados do portal?*

    Sim, os [dados acedem à API.](https://dev.applicationinsights.io/) Outras formas de extrair dados incluem [a exportação de Analytics para Power BI](../../azure-monitor/app/export-power-bi.md ) e [exportação contínua.](../../azure-monitor/app/export-telemetry.md)

## <a name="next-steps"></a><a name="next"></a>Passos seguintes

* [Pesquisar eventos e registos](../../azure-monitor/app/diagnostic-search.md)
* [Resolução de problemas](../../azure-monitor/app/troubleshoot-faq.md)
