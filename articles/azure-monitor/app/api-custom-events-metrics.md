---
title: API de Application Insights para métricas e eventos personalizados | Microsoft Docs
description: Insira algumas linhas de código em seu dispositivo ou aplicativo de área de trabalho, página da Web ou serviço para acompanhar o uso e diagnosticar problemas.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/27/2019
ms.openlocfilehash: 8bb144c78c5346f3351a6ada779a808410dbb30d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73667994"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>API de Application Insights para métricas e eventos personalizados

Insira algumas linhas de código em seu aplicativo para descobrir o que os usuários estão fazendo com ele ou para ajudar a diagnosticar problemas. Você pode enviar telemetria do dispositivo e de aplicativos da área de trabalho, clientes Web e servidores Web. Use a API de telemetria de núcleo do [aplicativo Azure insights](../../azure-monitor/app/app-insights-overview.md) para enviar eventos e métricas personalizados e suas próprias versões de telemetria padrão. Essa API é a mesma API que os coletores de dados padrão do Application Insights usam.

## <a name="api-summary"></a>Resumo da API

A API principal é uniforme em todas as plataformas, além de algumas variações, como `GetMetric`(somente .NET).

| Método | Utilizado para |
| --- | --- |
| [`TrackPageView`](#page-views) |Páginas, telas, folhas ou formulários. |
| [`TrackEvent`](#trackevent) |Ações do usuário e outros eventos. Usado para acompanhar o comportamento do usuário ou para monitorar o desempenho. |
| [`GetMetric`](#getmetric) |Métricas de zero e multidimensionais, C# somente agregação configurada centralmente. |
| [`TrackMetric`](#trackmetric) |Medidas de desempenho como comprimentos de fila não relacionados a eventos específicos. |
| [`TrackException`](#trackexception) |Registrando exceções para diagnóstico. Rastreie onde eles ocorrem em relação a outros eventos e examine os rastreamentos de pilha. |
| [`TrackRequest`](#trackrequest) |Registrar em log a frequência e a duração das solicitações do servidor para análise de desempenho. |
| [`TrackTrace`](#tracktrace) |Mensagens de log de diagnóstico. Você também pode capturar logs de terceiros. |
| [`TrackDependency`](#trackdependency) |Registrar em log a duração e a frequência de chamadas para componentes externos dos quais seu aplicativo depende. |

Você pode [anexar Propriedades e métricas](#properties) à maioria dessas chamadas de telemetria.

## <a name="prep"></a>Antes de começar

Se você ainda não tem uma referência no SDK Application Insights:

* Adicione o SDK do Application Insights ao seu projeto:

  * [Projeto ASP.NET](../../azure-monitor/app/asp-net.md)
  * [ASP.NET Core projeto](../../azure-monitor/app/asp-net-core.md)
  * [Projeto Java](../../azure-monitor/app/java-get-started.md)
  * [Projeto do node. js](../../azure-monitor/app/nodejs.md)
  * [JavaScript em cada página da Web](../../azure-monitor/app/javascript.md) 
* No seu dispositivo ou código do servidor Web, inclua:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

    *Node. js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Obter uma instância de TelemetryClient

Obter uma instância de `TelemetryClient` (exceto em JavaScript em páginas da Web):

Para aplicativos [ASP.NET Core](asp-net-core.md#how-can-i-track-telemetry-thats-not-automatically-collected) e [não http/Worker para aplicativos .net/.NET Core](worker-service.md#how-can-i-track-telemetry-thats-not-automatically-collected) , é recomendável obter uma instância de `TelemetryClient` do contêiner de injeção de dependência, conforme explicado em sua respectiva documentação.

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```

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

TelemetryClient é thread-safe.

Para projetos ASP.NET e Java, as solicitações HTTP de entrada são capturadas automaticamente. Talvez você queira criar instâncias adicionais de TelemetryClient para outro módulo do seu aplicativo. Por exemplo, você pode ter uma instância de TelemetryClient em sua classe de middleware para relatar eventos de lógica de negócios. Você pode definir propriedades como UserId e DeviceID para identificar o computador. Essas informações são anexadas a todos os eventos enviados pela instância.

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

Em projetos node. js, você pode usar `new applicationInsights.TelemetryClient(instrumentationKey?)` para criar uma nova instância, mas isso é recomendado apenas para cenários que exigem configuração isolada da `defaultClient`singleton.

## <a name="trackevent"></a>TrackEvent

No Application Insights, um *evento personalizado* é um ponto de dados que você pode exibir em [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) como uma contagem agregada e na [pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md) como ocorrências individuais. (Ele não está relacionado ao MVC ou a outros "eventos" da estrutura.)

Inserir chamadas de `TrackEvent` em seu código para contar vários eventos. Com que frequência os usuários escolhem um recurso específico, com que frequência eles atingem metas específicas ou talvez com que frequência eles fazem com que eles sejam determinados tipos de erros.

Por exemplo, em um aplicativo de jogo, envie um evento sempre que um usuário vence o jogo:

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

### <a name="custom-events-in-analytics"></a>Eventos personalizados na análise

A telemetria está disponível na tabela de `customEvents` no [Application insights Analytics](analytics.md). Cada linha representa uma chamada para `trackEvent(..)` em seu aplicativo.

Se a [amostragem](../../azure-monitor/app/sampling.md) estiver em operação, a propriedade ItemCount mostrará um valor maior que 1. Por exemplo, itemCount = = 10 significa que de 10 chamadas para trackEvent (), o processo de amostragem só transmitirá um deles. Para obter uma contagem correta de eventos personalizados, você deve, portanto, usar um código como `customEvents | summarize sum(itemCount)`.

## <a name="getmetric"></a>Getmetric

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

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> Microsoft. ApplicationInsights. TelemetryClient. TrackMetric não é o método preferencial para enviar métricas. As métricas devem sempre ser agregadas previamente em um período de tempo antes de serem enviadas. Use uma das sobrecargas de getmetric (..) para obter um objeto de métrica para acessar os recursos de pré-agregação do SDK. Se você estiver implementando sua própria lógica de pré-autenticação, poderá usar o método TrackMetric () para enviar as agregações resultantes. Se seu aplicativo exigir o envio de um item de telemetria separado a cada ocasião sem agregação ao longo do tempo, provavelmente você terá um caso de uso para telemetria de eventos; consulte TelemetryClient. TrackEvent (Microsoft. ApplicationInsights. DataContracts. EventTelemetry).

Application Insights pode modelar as métricas que não estão anexadas a eventos específicos. Por exemplo, você pode monitorar um comprimento de fila em intervalos regulares. Com as métricas, as medidas individuais são de menos interesse do que as variações e tendências, e os gráficos estatísticos são úteis.

Para enviar métricas para Application Insights, você pode usar a API `TrackMetric(..)`. Há duas maneiras de enviar uma métrica:

* Valor único. Sempre que você executa uma medição em seu aplicativo, você envia o valor correspondente para Application Insights. Por exemplo, suponha que você tenha uma métrica que descreva o número de itens em um contêiner. Durante um período de tempo específico, você primeiro coloca três itens no contêiner e, em seguida, remove dois itens. Da mesma forma, você chamaria `TrackMetric` duas vezes: primeiro passando o valor `3` e, em seguida, o valor `-2`. Application Insights armazena os dois valores em seu nome.

* Aggregation. Ao trabalhar com métricas, cada medida única raramente é de interesse. Em vez disso, um resumo do que aconteceu durante um período de tempo específico é importante. Esse resumo é chamado de _agregação_. No exemplo acima, a soma da métrica agregada para esse período de tempo é `1` e a contagem dos valores de métrica é `2`. Ao usar a abordagem de agregação, você só invoca `TrackMetric` uma vez por período de tempo e envia os valores de agregação. Essa é a abordagem recomendada, pois ela pode reduzir significativamente o custo e a sobrecarga de desempenho enviando menos pontos de dados para Application Insights e, ao mesmo tempo, coletando todas as informações relevantes.

### <a name="examples"></a>Exemplos

#### <a name="single-values"></a>Valores únicos

Para enviar um único valor de métrica:

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

### <a name="custom-metrics-in-analytics"></a>Métricas personalizadas no Analytics

A telemetria está disponível na tabela de `customMetrics` no [Application insights Analytics](analytics.md). Cada linha representa uma chamada para `trackMetric(..)` em seu aplicativo.

* `valueSum`-esta é a soma das medidas. Para obter o valor médio, divida por `valueCount`.
* `valueCount`-o número de medições agregadas a esta chamada de `trackMetric(..)`.

## <a name="page-views"></a>Exibições de página

Em um aplicativo de dispositivo ou página da Web, a telemetria de exibição de página é enviada por padrão quando cada tela ou página é carregada. Mas você pode alterar isso para rastrear exibições de página em momentos adicionais ou diferentes. Por exemplo, em um aplicativo que exibe guias ou folhas, talvez você queira controlar uma página sempre que o usuário abrir uma nova folha.

Os dados de sessão e de usuário são enviados como propriedades junto com exibições de página, de modo que os gráficos de sessão e de usuário ficam ativos quando há telemetria de exibição de página.

### <a name="custom-page-views"></a>Exibições de página personalizadas

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

Se você tiver várias guias em páginas HTML diferentes, poderá especificar também a URL:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Exibições de página de tempo

Por padrão, os horários relatados como **tempo de carregamento da exibição de página** são medidos de quando o navegador envia a solicitação, até que o evento de carregamento de página do navegador seja chamado.

Em vez disso, você pode:

* Defina uma duração explícita na chamada [trackPageView](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/API.md#trackpageview) : `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Use as chamadas de tempo de exibição de página `startTrackPage` e `stopTrackPage`.

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

O nome que você usa como o primeiro parâmetro associa as chamadas de início e parada. O padrão é o nome da página atual.

As durações de carregamento de página resultantes exibidas no Metrics Explorer são derivadas do intervalo entre as chamadas iniciar e parar. Cabe a você qual intervalo você realmente tem.

### <a name="page-telemetry-in-analytics"></a>Telemetria de página no Analytics

Na [análise](analytics.md) , duas tabelas mostram dados de operações do navegador:

* A tabela `pageViews` contém dados sobre a URL e o título da página
* A tabela `browserTimings` contém dados sobre o desempenho do cliente, como o tempo necessário para processar os dados de entrada

Para descobrir quanto tempo o navegador leva para processar páginas diferentes:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Para descobrir as popularidades de diferentes navegadores:

```kusto
pageViews
| summarize count() by client_Browser
```

Para associar exibições de página a chamadas AJAX, ingresse com dependências:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

O SDK do servidor usa TrackRequest para registrar em log solicitações HTTP.

Você também pode chamá-lo se quiser simular solicitações em um contexto em que você não tem o módulo de serviço Web em execução.

No entanto, a maneira recomendada para enviar telemetria de solicitação é onde a solicitação atua como um <a href="#operation-context">contexto de operação</a>.

## <a name="operation-context"></a>Contexto de operação

Você pode correlacionar os itens de telemetria associando-os ao contexto de operação. O módulo de rastreamento de solicitação padrão faz isso para exceções e outros eventos que são enviados enquanto uma solicitação HTTP está sendo processada. Em [pesquisa](../../azure-monitor/app/diagnostic-search.md) e [análise](analytics.md), você pode encontrar facilmente todos os eventos associados à solicitação usando sua ID de operação.

Consulte [correlação de telemetria no Application insights](../../azure-monitor/app/correlation.md) para obter mais detalhes sobre a correlação.

Ao controlar a telemetria manualmente, a maneira mais fácil de garantir a correlação de telemetria usando esse padrão:

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

Juntamente com a configuração de um contexto de operação, `StartOperation` cria um item de telemetria do tipo que você especificar. Ele envia o item de telemetria quando você descarta a operação ou se você chama explicitamente `StopOperation`. Se você usar `RequestTelemetry` como o tipo de telemetria, sua duração será definida como o intervalo de tempo entre iniciar e parar.

Os itens de telemetria relatados em um escopo de operação se tornam ' filhos ' dessa operação. Contextos de operação podem ser aninhados.

Na pesquisa, o contexto de operação é usado para criar a lista de **itens relacionados** :

![Itens relacionados](./media/api-custom-events-metrics/21.png)

Consulte [acompanhar operações personalizadas com Application insights SDK do .net](../../azure-monitor/app/custom-operations-tracking.md) para obter mais informações sobre o acompanhamento de operações personalizadas.

### <a name="requests-in-analytics"></a>Solicitações no Analytics

No [Application insights Analytics](analytics.md), as solicitações aparecem na tabela `requests`.

Se a [amostragem](../../azure-monitor/app/sampling.md) estiver em operação, a propriedade ItemCount mostrará um valor maior que 1. Por exemplo, itemCount = = 10 significa que de 10 chamadas para trackRequest (), o processo de amostragem só transmitirá um deles. Para obter uma contagem correta de solicitações e a duração média segmentada por nomes de solicitação, use um código como:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Enviar exceções para Application Insights:

* Para [contá-los](../../azure-monitor/app/metrics-explorer.md), como uma indicação da frequência de um problema.
* Para [examinar ocorrências individuais](../../azure-monitor/app/diagnostic-search.md).

Os relatórios incluem os rastreamentos de pilha.

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

Os SDKs capturam várias exceções automaticamente, de modo que você nem sempre precisa chamar o Trackexception explicitamente.

* ASP.NET: [escreva o código para capturar exceções](../../azure-monitor/app/asp-net-exceptions.md).
* Java EE: as [exceções são capturadas automaticamente](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures).
* JavaScript: as exceções são capturadas automaticamente. Se você quiser desabilitar a coleta automática, adicione uma linha ao trecho de código que você inserir em suas páginas da Web:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Exceções na análise

No [Application insights Analytics](analytics.md), as exceções aparecem na tabela `exceptions`.

Se a [amostragem](../../azure-monitor/app/sampling.md) estiver em operação, a propriedade `itemCount` mostrará um valor maior que 1. Por exemplo, itemCount = = 10 significa que de 10 chamadas para trackexception (), o processo de amostragem transmitiu apenas um deles. Para obter uma contagem correta de exceções segmentadas por tipo de exceção, use um código como:

```kusto
exceptions
| summarize sum(itemCount) by type
```

A maioria das informações de pilha importantes já foi extraída em variáveis separadas, mas você pode separar a estrutura de `details` para obter mais. Como essa estrutura é dinâmica, você deve converter o resultado para o tipo esperado. Por exemplo:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Para associar exceções a suas solicitações relacionadas, use uma junção:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

Use o TrackTrace para ajudar a diagnosticar problemas enviando uma "trilha de navegação estrutural" para Application Insights. Você pode enviar partes de dados de diagnóstico e inspecioná-los na [pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md).

Nos [adaptadores de log](../../azure-monitor/app/asp-net-trace-logs.md) do .net, use essa API para enviar logs de terceiros ao Portal.

Em Java para [agentes padrão como Log4J, Logback](../../azure-monitor/app/java-trace-logs.md) Use Application insights acrescentadores Log4J ou Logback para enviar logs de terceiros ao Portal.

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

Registre um evento de diagnóstico, como inserir ou sair de um método.

 Parâmetro | Descrição
---|---
`message` | Dados de diagnóstico. Pode ser muito mais longo do que um nome.
`properties` | Mapa de cadeia de caracteres para cadeia de caracteres: dados adicionais usados para [Filtrar exceções](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) no Portal. O padrão é vazio.
`severityLevel` | Valores com suporte: [nível. TS](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/shared/AppInsightsCommon/src/Interfaces/Contracts/Generated/SeverityLevel.ts)

Você pode pesquisar o conteúdo da mensagem, mas (diferentemente dos valores da propriedade) não é possível filtrá-lo.

O limite de tamanho em `message` é muito maior do que o limite nas propriedades.
Uma vantagem do TrackTrace é que você pode colocar dados relativamente longos na mensagem. Por exemplo, você pode codificar postar dados lá.  

Além disso, você pode adicionar um nível de severidade à sua mensagem. E, assim como outras telemetrias, você pode adicionar valores de propriedade para ajudá-lo a filtrar ou pesquisar conjuntos diferentes de rastreamentos. Por exemplo:

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

Na [pesquisa](../../azure-monitor/app/diagnostic-search.md), você pode filtrar facilmente todas as mensagens de um nível de severidade específico relacionado a um banco de dados específico.

### <a name="traces-in-analytics"></a>Rastreamentos no Analytics

No [Application insights Analytics](analytics.md), as chamadas para TrackTrace aparecem na tabela `traces`.

Se a [amostragem](../../azure-monitor/app/sampling.md) estiver em operação, a propriedade ItemCount mostrará um valor maior que 1. Por exemplo, itemCount = = 10 significa que de 10 chamadas para `trackTrace()`, o processo de amostragem só transmitirá um deles. Para obter uma contagem correta de chamadas de rastreamento, você deve usar, portanto, um código como `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency

Use a chamada TrackDependency para acompanhar os tempos de resposta e as taxas de sucesso de chamadas para uma parte externa do código. Os resultados aparecem nos gráficos de dependência no Portal. O trecho de código abaixo precisa ser adicionado sempre que uma chamada de dependência é feita.

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
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
}
finally {
    long endTime = System.currentTimeMillis();
    long delta = endTime - startTime;
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    telemetry.setTimeStamp(startTime);
    telemetry.trackDependency(dependencyTelemetry);
}
```

*JavaScript*

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

Lembre-se de que os SDKs do servidor incluem um [módulo de dependência](../../azure-monitor/app/asp-net-dependencies.md) que descobre e controla determinadas chamadas de dependência automaticamente, por exemplo, para bancos de dados e APIs REST. Você precisa instalar um agente em seu servidor para que o módulo funcione. 

No Java, determinadas chamadas de dependência podem ser rastreadas automaticamente usando o [agente Java](../../azure-monitor/app/java-agent.md).

Você usará essa chamada se quiser controlar as chamadas que o rastreamento automatizado não capturará ou se você não quiser instalar o agente.

Para desativar o módulo padrão de rastreamento de dependência no C#, edite [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) e exclua a referência a `DependencyCollector.DependencyTrackingTelemetryModule`. Em Java, não instale o agente Java se você não quiser coletar dependências padrão automaticamente.

### <a name="dependencies-in-analytics"></a>Dependências na análise

No [Application insights Analytics](analytics.md), as chamadas trackDependency aparecem na tabela `dependencies`.

Se a [amostragem](../../azure-monitor/app/sampling.md) estiver em operação, a propriedade ItemCount mostrará um valor maior que 1. Por exemplo, itemCount = = 10 significa que de 10 chamadas para trackDependency (), o processo de amostragem só transmitirá um deles. Para obter uma contagem correta de dependências segmentadas por componente de destino, use um código como:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Para associar as dependências às suas solicitações relacionadas, use uma junção:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Liberando dados

Normalmente, o SDK envia dados em intervalos fixos (normalmente 30 segundos) ou sempre que o buffer estiver cheio (normalmente 500 itens). No entanto, em alguns casos, talvez você queira liberar o buffer – por exemplo, se você estiver usando o SDK em um aplicativo que é desligado.

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

A função é assíncrona para o [canal de telemetria do servidor](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

Idealmente, o método Flush () deve ser usado na atividade de desligamento do aplicativo.

## <a name="authenticated-users"></a>Usuários autenticados

Em um aplicativo Web, os usuários são (por padrão) identificados por cookies. Um usuário pode ser contado mais de uma vez se acessar seu aplicativo de um computador ou navegador diferente, ou se eles excluirem cookies.

Se os usuários entrarem em seu aplicativo, você poderá obter uma contagem mais precisa definindo a ID de usuário autenticado no código do navegador:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

Em um aplicativo Web MVC do ASP.NET, por exemplo:

*Razor*

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

Não é necessário usar o nome de entrada real do usuário. Ele só precisa ser uma ID exclusiva para esse usuário. Ele não deve incluir espaços ou qualquer um dos caracteres `,;=|`.

A ID de usuário também é definida em um cookie de sessão e enviada ao servidor. Se o SDK do servidor estiver instalado, a ID de usuário autenticado será enviada como parte das propriedades de contexto de telemetria do cliente e do servidor. Você pode filtrar e Pesquisar nele.

Se seu aplicativo agrupar usuários em contas, você também poderá passar um identificador para a conta (com as mesmas restrições de caractere).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

No [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md), você pode criar um gráfico que conta **usuários, autenticados**e **contas de usuário**.

Você também pode [Pesquisar](../../azure-monitor/app/diagnostic-search.md) pontos de dados do cliente com contas e nomes de usuário específicos.

## <a name="properties"></a>Filtrando, pesquisando e segmentando seus dados usando propriedades

Você pode anexar Propriedades e medidas a seus eventos (e também a métricas, exibições de página, exceções e outros dados de telemetria).

*As propriedades* são valores de cadeia de caracteres que você pode usar para filtrar a telemetria nos relatórios de uso. Por exemplo, se seu aplicativo fornece vários jogos, você pode anexar o nome do jogo a cada evento para que você possa ver quais jogos são mais populares.

Há um limite de 8192 no comprimento da cadeia de caracteres. (Se você quiser enviar grandes partes de dados, use o parâmetro Message de TrackTrace.)

*Métricas* são valores numéricos que podem ser apresentados graficamente. Por exemplo, talvez você queira ver se há um aumento gradual nas pontuações que os seus jogadores obtêm. Os grafos podem ser segmentados pelas propriedades que são enviadas com o evento, para que você possa obter grafos separados ou empilhados para jogos diferentes.

Para que os valores de métrica sejam exibidos corretamente, eles devem ser maiores ou iguais a 0.

Há alguns [limites no número de propriedades, valores de propriedade e métricas](#limits) que você pode usar.

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
> Tome cuidado para não registrar informações de identificação pessoal em Propriedades.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Maneira alternativa de definir propriedades e métricas

Se for mais conveniente, você poderá coletar os parâmetros de um evento em um objeto separado:

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
> Não reutilize a mesma instância de item de telemetria (`event` neste exemplo) para chamar Track * () várias vezes. Isso pode fazer com que a telemetria seja enviada com configuração incorreta.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Medidas e propriedades personalizadas no Analytics

No [Analytics](analytics.md), as métricas personalizadas e as propriedades são exibidas nos atributos `customMeasurements` e `customDimensions` de cada registro de telemetria.

Por exemplo, se você tiver adicionado uma propriedade chamada "Game" à telemetria de solicitação, essa consulta contará as ocorrências de valores diferentes de "Game" e mostrará a média da "pontuação" da métrica personalizada:

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Observe que:

* Quando você extrai um valor do JSON customDimensions ou customMeasurements, ele tem um tipo dinâmico e, portanto, você deve convertê-lo `tostring` ou `todouble`.
* Para tomar conta da possibilidade de [amostragem](../../azure-monitor/app/sampling.md), você deve usar `sum(itemCount)`, não `count()`.

## <a name="timed"></a>Eventos de tempo

Às vezes, você deseja criar um gráfico do tempo necessário para realizar uma ação. Por exemplo, talvez você queira saber quanto tempo os usuários levam para considerar as opções de um jogo. Você pode usar o parâmetro de medição para isso.

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

## <a name="defaults"></a>Propriedades padrão para telemetria personalizada

Se você quiser definir valores de propriedade padrão para alguns dos eventos personalizados que você escreve, você pode defini-los em uma instância de TelemetryClient. Eles são anexados a cada item de telemetria enviado do cliente.

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

Chamadas de telemetria individuais podem substituir os valores padrão em seus dicionários de propriedade.

*Para clientes Web JavaScript*, use inicializadores de telemetria JavaScript.

*Para adicionar propriedades a toda a telemetria*, incluindo os dados de módulos de coleta padrão, [implemente `ITelemetryInitializer`](../../azure-monitor/app/api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Amostragem, filtragem e processamento de telemetria

Você pode escrever código para processar a telemetria antes que ela seja enviada do SDK. O processamento inclui dados que são enviados dos módulos de telemetria padrão, como coleção de solicitações HTTP e coleção de dependências.

[Adicione Propriedades](../../azure-monitor/app/api-filtering-sampling.md#add-properties) à telemetria implementando `ITelemetryInitializer`. Por exemplo, você pode adicionar números de versão ou valores que são calculados de outras propriedades.

A [filtragem](../../azure-monitor/app/api-filtering-sampling.md#filtering) pode modificar ou descartar a telemetria antes de ser enviada do SDK implementando `ITelemetryProcessor`. Você controla o que é enviado ou descartado, mas precisa considerar o efeito em suas métricas. Dependendo de como você descartar itens, você pode perder a capacidade de navegar entre itens relacionados.

A [amostragem](../../azure-monitor/app/api-filtering-sampling.md) é uma solução empacotada para reduzir o volume de dados enviados de seu aplicativo para o Portal. Ele faz isso sem afetar as métricas exibidas. E faz isso sem afetar sua capacidade de diagnosticar problemas navegando entre itens relacionados, como exceções, solicitações e exibições de página.

[Saiba mais](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Desabilitando a telemetria

Para *parar e iniciar dinamicamente* a coleta e a transmissão da telemetria:

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

Para *desabilitar os coletores padrão selecionados*, por exemplo, contadores de desempenho, solicitações HTTP ou dependências, exclua ou comente as linhas relevantes em [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Você pode fazer isso, por exemplo, se quiser enviar seus próprios dados do TrackRequest.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Para *desabilitar os coletores padrão selecionados*--por exemplo, contadores de desempenho, solicitações HTTP ou dependências – no tempo de inicialização, métodos de configuração de cadeia para o código de inicialização do SDK:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Para desabilitar esses coletores após a inicialização, use o objeto de configuração: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Modo de desenvolvedor

Durante a depuração, é útil ter sua telemetria acelerada por meio do pipeline para que você possa ver os resultados imediatamente. Você também recebe mensagens adicionais que ajudam a rastrear problemas com a telemetria. Desative-a na produção, pois ela pode retardar seu aplicativo.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

Para o Node. js, você pode habilitar o modo de desenvolvedor habilitando o log interno por meio de `setInternalLogging` e configurando `maxBatchSize` como 0, o que faz com que a telemetria seja enviada assim que for coletada.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="ikey"></a>Definindo a chave de instrumentação para a telemetria personalizada selecionada

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-ikey"></a>Chave de instrumentação dinâmica

Para evitar a mistura de telemetria de ambientes de desenvolvimento, teste e produção, você pode [criar Application insights recursos separados](../../azure-monitor/app/create-new-resource.md ) e alterar suas chaves, dependendo do ambiente.

Em vez de obter a chave de instrumentação do arquivo de configuração, você pode defini-la em seu código. Defina a chave em um método de inicialização, como global.aspx.cs em um serviço ASP.NET:

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

Em páginas da Web, talvez você queira defini-lo do estado do servidor da Web, em vez de codificá-lo literalmente no script. Por exemplo, em uma página da Web gerada em um aplicativo ASP.NET:

*JavaScript no Razor*

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

## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient tem uma propriedade de contexto, que contém valores que são enviados junto com todos os dados de telemetria. Normalmente, eles são definidos pelos módulos de telemetria padrão, mas você também pode defini-los por conta própria. Por exemplo:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Se você definir qualquer um desses valores por conta própria, considere remover a linha relevante de [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), para que os valores e os valores padrão não sejam confusos.

* **Componente**: o aplicativo e sua versão.
* **Dispositivo**: dados sobre o dispositivo em que o aplicativo está em execução. (Em aplicativos Web, esse é o servidor ou dispositivo de cliente do qual a telemetria é enviada.)
* **InstrumentationKey**: o recurso de Application insights no Azure em que a telemetria é exibida. Normalmente, ele é coletado em ApplicationInsights. config.
* **Local**: a localização geográfica do dispositivo.
* **Operação**: em aplicativos Web, a solicitação HTTP atual. Em outros tipos de aplicativo, você pode definir isso para agrupar eventos juntos.
  * **ID**: um valor gerado que correlaciona eventos diferentes, de modo que, quando você inspeciona qualquer evento na pesquisa de diagnóstico, você pode encontrar itens relacionados.
  * **Nome**: um identificador, geralmente a URL da solicitação HTTP.
  * **Sintética**: se não for nulo ou vazio, uma cadeia de caracteres que indica que a origem da solicitação foi identificada como um teste de robô ou da Web. Por padrão, ele é excluído dos cálculos em Metrics Explorer.
* **Propriedades**: propriedades que são enviadas com todos os dados de telemetria. Ele pode ser substituído em chamadas de Track * individuais.
* **Sessão**: a sessão do usuário. A ID é definida como um valor gerado, que é alterado quando o usuário não esteve ativo por um tempo.
* **Usuário**: informações do usuário.

## <a name="limits"></a>Limites

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Para evitar atingir o limite de taxa de dados, use [amostragem](../../azure-monitor/app/sampling.md).

Para determinar por quanto tempo os dados são mantidos, consulte [retenção de dados e privacidade](../../azure-monitor/app/data-retention-privacy.md).

## <a name="reference-docs"></a>Documentos de referência

* [Referência de ASP.NET](https://docs.microsoft.com/dotnet/api/overview/azure/insights?view=azure-dotnet)
* [Java reference](https://docs.microsoft.com/java/api/overview/azure/appinsights?view=azure-java-stable/) (Referência de Java)
* [Referência de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)


## <a name="sdk-code"></a>Código do SDK

* [SDK de ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Pacotes do Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [SDK Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [SDK JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="questions"></a>Dúvidas

* *Quais exceções podem Track_ () chamadas throw?*

    Nenhum. Você não precisa encapsulá-los em cláusulas try-catch. Se o SDK encontrar problemas, ele registrará mensagens na saída do console de depuração e--se as mensagens forem passadas--na pesquisa de diagnóstico.
* *Há uma API REST para obter dados do portal?*

    Sim, a [API de acesso a dados](https://dev.applicationinsights.io/). Outras maneiras de extrair dados incluem [exportar do Analytics para Power bi](../../azure-monitor/app/export-power-bi.md ) e [exportação contínua](../../azure-monitor/app/export-telemetry.md).

## <a name="next"></a>Passos seguintes

* [Pesquisar eventos e logs](../../azure-monitor/app/diagnostic-search.md)
* [Resolução de problemas](../../azure-monitor/app/troubleshoot-faq.md)
