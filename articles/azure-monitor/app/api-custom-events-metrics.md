---
title: API do Application Insights para métricas e eventos personalizados | Documentos da Microsoft
description: Insira umas poucas linhas de código no seu serviço, para controlar a utilização e diagnosticar problemas, a página Web ou a aplicação de ambiente de trabalho ou dispositivo.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: mbullwin
ms.openlocfilehash: fca93c002fba4d54e8e87ca31e0fd1c258826665
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369748"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>API do Application Insights para métricas e eventos personalizados

Insira umas poucas linhas de código na sua aplicação para saber o que os utilizadores estão a fazer com ele, ou para ajudar a diagnosticar problemas. Pode enviar telemetria de aplicações de ambiente de trabalho e dispositivos, os clientes web e servidores web. Utilize o [do Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) core API de telemetria para enviar eventos personalizados e métricas e suas próprias versões de telemetria standard. Esta API é a mesma API que utilizam os recoletores de dados padrão do Application Insights.

> [!NOTE]
> `TrackMetric()` não se encontra o método preferencial de enviar métricas personalizadas para o .NET com base em aplicações. Na [versão 2.60 beta 3](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/CHANGELOG.md#version-260-beta3) do Application Insights SDK do .NET um novo método, [ `TelemetryClient.GetMetric()` ](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet) foi introduzido. Desde o .NET SDK do Application Insights [versão 2.72](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet) esta funcionalidade está agora parte da versão estável.

## <a name="api-summary"></a>Resumo da API

A API principal é uniforme em todas as plataformas, exceto por algumas variações como `GetMetric`(apenas para o .NET).

| Método | Utilizado para |
| --- | --- |
| [`TrackPageView`](#page-views) |Páginas, ecrãs, os painéis ou formulários. |
| [`TrackEvent`](#trackevent) |Ações do usuário e outros eventos. Usado para controlar o comportamento do utilizador ou para monitorizar o desempenho. |
| [`GetMetric`](#getmetric) |Zero e métricas multidimensionais, agregação configurada centralmente, c# apenas. |
| [`TrackMetric`](#trackmetric) |Medidas de desempenho, tais como os comprimentos de fila não relacionada com eventos específicos. |
| [`TrackException`](#trackexception) |Exceções de registo para diagnósticos adicionais. Em que ocorrem em relação a outros eventos e examinar os rastreios de pilha de rastreio. |
| [`TrackRequest`](#trackrequest) |Registo a frequência e duração dos pedidos de servidor para análise de desempenho. |
| [`TrackTrace`](#tracktrace) |Mensagens de registo de diagnóstico. Também é possível capturar os registos de terceiros. |
| [`TrackDependency`](#trackdependency) |Registo a duração e a frequência de chamadas para componentes externos que depende da sua aplicação. |

Pode [anexar propriedades e as métricas](#properties) à maioria dessas chamadas de telemetria.

## <a name="prep"></a>Antes de começar

Se ainda não tem uma referência no SDK do Application Insights:

* Adicione o Application Insights SDK ao seu projeto:

  * [Projeto ASP.NET](../../azure-monitor/app/asp-net.md)
  * [Projeto de Java](../../azure-monitor/app/java-get-started.md)
  * [Projeto de node. js](../../azure-monitor/app/nodejs.md)
  * [JavaScript em cada página da Web](../../azure-monitor/app/javascript.md) 
* No código do seu dispositivo ou servidor Web, inclua:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Obter uma instância de TelemetryClient

Obter uma instância de `TelemetryClient` (exceto em JavaScript em páginas da Web):

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

Para projetos do ASP.NET e Java, pedidos HTTP recebidos são automaticamente capturados. Pode desejar criar instâncias adicionais de TelemetryClient para outro módulo da sua aplicação. Por exemplo, poderá ter uma instância de TelemetryClient em sua classe de middleware para eventos de lógica de negócios do relatório. Pode definir propriedades, tais como o ID de utilizador e o DeviceId para identificar a máquina. Estas informações estão ligadas a todos os eventos que envia a instância. 

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

Em projetos de node. js, pode usar `new applicationInsights.TelemetryClient(instrumentationKey?)` para criar uma nova instância, mas isso é recomendado apenas para cenários que exigem configuração isolada desde o singleton `defaultClient`.

## <a name="trackevent"></a>TrackEvent

No Application Insights, um *evento personalizado* é um ponto de dados que pode apresentar num [Explorador de métricas](../../azure-monitor/app/metrics-explorer.md) como uma contagem agregada e, na [pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md) como individuais ocorrências. (Ele não está relacionado MVC ou outra estrutura "eventos".)

Inserir `TrackEvent` chama em seu código para a contagem de vários eventos. A frequência com que os usuários escolhem um determinado recurso, a frequência com que eles obtenham objetivos específicos ou talvez a frequência com que eles tornam a determinados tipos de erros.

Por exemplo, num aplicativo de jogo, envie um evento sempre que um usuário ganha o jogo:

*JavaScript*

```javascript
appInsights.trackEvent("WinGame");
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

### <a name="custom-events-in-analytics"></a>Eventos personalizados no Analytics

A telemetria está disponível na `customEvents` de tabela no [Application Insights Analytics](analytics.md). Cada linha representa uma chamada para `trackEvent(..)` na sua aplicação.

Se [amostragem](../../azure-monitor/app/sampling.md) estar em funcionamento, a propriedade itemCount mostra um valor superior a 1. Para o exemplo itemCount = = 10 significa que de 10 chamadas de trackevent (), o processo de amostragem apenas transmitida uma delas. Para obter um número correto de eventos personalizados, deve usar, por isso, use um código como `customEvents | summarize sum(itemCount)`.

## <a name="getmetric"></a>GetMetric

### <a name="examples"></a>Exemplos

*C#*

```csharp
#pragma warning disable CA1716  // Namespace naming

namespace User.Namespace.Example01
{
    using System;
    using Microsoft.ApplicationInsights;
    using TraceSeverityLevel = Microsoft.ApplicationInsights.DataContracts.SeverityLevel;

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
                client.TrackTrace($"Data series or dimension cap was reached for metric {animalsSold.Identifier.MetricId}.", TraceSeverityLevel.Error);
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
> Microsoft.ApplicationInsights.TelemetryClient.TrackMetric foi preterido no SDK do .NET. As métricas devem sempre ser pré-agregados num período de tempo antes de serem enviados. Utilize uma das sobrecargas GetMetric(..) para obter um objeto de métrica para aceder a recursos de agregação de pré-lançamento do SDK. Se estiver a implementar sua própria lógica de agregação de pré-lançamento, pode utilizar o método do controle (ITelemetry metricTelemetry) para enviar as agregações resultantes. Se seu aplicativo requer o envio de um item de telemetria separado em cada ocasião sem agregação para sempre, é provável que tenha um caso de utilização para a telemetria de evento; Ver TelemetryClient.TrackEvent (Microsoft.ApplicationInsights.DataContracts.EventTelemetry).

O Application Insights pode gráfico de métricas que não estão anexadas a eventos específicos. Por exemplo, pode monitorar um comprimento de fila em intervalos regulares. Com a métrica, as medidas individuais são menos interessantes que as variações e tendências e os gráficos estatísticos por isso, são úteis.

Para enviar métricas para o Application Insights, pode usar o `TrackMetric(..)` API. Existem duas formas de enviar uma métrica:

* Valor único. Sempre que executar uma medida em seu aplicativo, envia o valor correspondente para o Application Insights. Por exemplo, suponha que tenha uma métrica que descreve o número de itens num contentor. Durante um período de tempo específico, que primeiro colocar três itens para o contentor e, em seguida, remover dois itens. Da mesma forma, chamaria `TrackMetric` duas vezes: primeiro, passando o valor `3` e, em seguida, o valor `-2`. O Application Insights armazena os dois valores em seu nome.

* Agregação. Ao trabalhar com as métricas, cada medição única raramente é de interesse. Em vez disso, um resumo do que aconteceu durante um período de tempo específico é importante. Denomina-se um resumo dessa _agregação_. No exemplo acima, é a soma de métrica agregada para esse período de tempo `1` e a contagem de valores de métrica é `2`. Ao utilizar a abordagem de agregação, apenas invocar `TrackMetric` uma vez por período de tempo e enviar os valores agregados. Esta é a abordagem recomendada, uma vez que ele pode reduzir significativamente o custo e desempenho sobrecarga através do envio de menos pontos de dados para o Application Insights, enquanto ainda recolher todas as informações relevantes.

### <a name="examples"></a>Exemplos

#### <a name="single-values"></a>Valores únicos

Para enviar um valor de métrica único:

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

A telemetria está disponível na `customMetrics` de tabela no [Application Insights Analytics](analytics.md). Cada linha representa uma chamada para `trackMetric(..)` na sua aplicação.

* `valueSum` -Essa é a soma das medidas. Para obter o valor médio, divida por `valueCount`.
* `valueCount` -O número de medidas que foram agregados deste `trackMetric(..)` chamar.

## <a name="page-views"></a>Visualizações da página

Num aplicativo de dispositivo ou página Web, telemetria de vista de página é enviada por predefinição, quando cada ecrã ou uma página for carregada. Mas pode alterá-la para controlar as vistas de página em momentos diferentes ou adicionais. Por exemplo, numa aplicação que apresenta os separadores ou painéis, pode querer controlar uma página sempre que o usuário abre um painel novo.

Dados de utilizador e uma sessão são enviados como propriedades, juntamente com vistas de página, os gráficos de sessão de utilizador e dê vida quando existe telemetria de vista de página.

### <a name="custom-page-views"></a>Vistas de página personalizada

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

Se tiver várias guias em diferentes páginas HTML, pode especificar o URL também:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Vistas de página de temporização

Por predefinição, os tempos são comunicados como **tempo de carregamento da vista de página** são medido desde que o browser envia o pedido, até que o evento de carregamento de página do navegador é chamado.

Em vez disso, pode:

* Defina uma duração de explícita na [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) chamar: `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Utilize a vista de página exceder o tempo chamadas `startTrackPage` e `stopTrackPage`.

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

O nome que irá utilizar como o primeiro parâmetro associa as chamadas de início e fim. Assume como predefinição o nome da página atual.

As durações resultante de carregamento de página apresentadas no Explorador de métricas são derivadas no intervalo entre as chamadas de início e fim. Cabe a o intervalo de tempo, na verdade.

### <a name="page-telemetry-in-analytics"></a>Telemetria de página no Analytics

Na [Analytics](analytics.md) duas tabelas mostram dados de operações do browser:

* O `pageViews` tabela contém dados sobre o título de URL e página
* O `browserTimings` tabela contém dados sobre o desempenho do cliente, como o tempo despendido para processar os dados de entrada

Para localizar o navegador de tempo demora a processar páginas diferentes:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Para detetar os popularities de diferentes navegadores:

```kusto
pageViews
| summarize count() by client_Browser
```

Associar vistas de página para chamadas AJAX, Junte-se com as dependências:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

O servidor SDK utiliza TrackRequest para registar pedidos HTTP.

Pode também chamá-la se pretender simular pedidos num contexto em que não tem o módulo do serviço web em execução.

No entanto, a forma recomendada para enviar a telemetria de pedido é onde o pedido funciona como um <a href="#operation-context">contexto de operação</a>.

## <a name="operation-context"></a>Contexto de operação

Pode correlacionar os itens de telemetria em conjunto, associando-os com o contexto de operação. O módulo de controlo de solicitação padrão faz isso para exceções e outros eventos que são enviados enquanto está a ser processada uma solicitação HTTP. Na [pesquisa](../../azure-monitor/app/diagnostic-search.md) e [Analytics](analytics.md), pode encontrar facilmente todos os eventos associados à solicitação com o seu ID de operação.

Ver [correlação de telemetria no Application Insights](../../azure-monitor/app/correlation.md) para obter mais detalhes sobre a correlação.

Se a monitorização telemetria manualmente, a maneira mais fácil para se certificar de correlação de telemetria ao utilizar este padrão:

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

Juntamente com a definição de um contexto de operação, `StartOperation` cria um item de telemetria do tipo que especificou. Envia o item de telemetria ao descartar a operação, ou se chamar explicitamente `StopOperation`. Se usar `RequestTelemetry` como o tipo de telemetria, sua duração está definida para o intervalo de tempo entre o início e fim.

Itens de telemetria comunicadas dentro de um âmbito da operação de se tornar filhos de tal operação. Contextos de operação podem ser aninhados.

Na pesquisa, o contexto de operação é utilizado para criar o **itens relacionados** lista:

![Itens relacionados](./media/api-custom-events-metrics/21.png)

Ver [controlar operações personalizadas com o .NET SDK do Application Insights](../../azure-monitor/app/custom-operations-tracking.md) para obter mais informações sobre operações personalizadas de controlo.

### <a name="requests-in-analytics"></a>Pedidos no Analytics

Na [Application Insights Analytics](analytics.md), os pedidos show se o `requests` tabela.

Se [amostragem](../../azure-monitor/app/sampling.md) está em operação, a propriedade itemCount irá mostrar um valor superior a 1. Para o exemplo itemCount = = 10 significa que de 10 chamadas para trackRequest(), o processo de amostragem apenas transmitida uma delas. Para obter uma contagem correta de pedidos e duração média segmentados por nomes de pedido, utilize o código como:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Envie exceções para o Application Insights:

* Para [contá-las](../../azure-monitor/app/metrics-explorer.md), como uma indicação de que a frequência de um problema.
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

Os SDKs capturam muitas exceções automaticamente, nem sempre tem que chamar TrackException explicitamente.

* ASP.NET: [Escrever código para interceptar exceções](../../azure-monitor/app/asp-net-exceptions.md).
* Java EE: [Exceções são capturadas automaticamente](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures).
* JavaScript: Exceções são capturadas automaticamente. Se pretender desativar a recolha automática, adicione uma linha para o fragmento de código que insere em suas páginas da Web:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Exceções no Analytics

Na [Application Insights Analytics](analytics.md), exceções de aparecem no `exceptions` tabela.

Se [amostragem](../../azure-monitor/app/sampling.md) estar em funcionamento, o `itemCount` propriedade mostra um valor superior a 1. Para o exemplo itemCount = = 10 significa que de 10 chamadas para trackexception (), o processo de amostragem apenas transmitida uma delas. Para obter um número correto de exceções segmentados por tipo de exceção, utilize o código como:

```kusto
exceptions
| summarize sum(itemCount) by type
```

A maioria das informações importantes de pilha já é extraído em variáveis separadas, mas pode extrair afastados o `details` estrutura para obter mais. Uma vez que esta estrutura é dinâmica, deverá converter o resultado para o tipo esperado. Por exemplo:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Para associar os pedidos relacionados exceções, utilize uma associação:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

Utilize TrackTrace para ajudar a diagnosticar problemas com o envio de um "trilho" para o Application Insights. Pode enviar os segmentos de dados de diagnóstico e Inspecione-os na [pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md).

No .NET [iniciar sessão adaptadores](../../azure-monitor/app/asp-net-trace-logs.md) utilizar esta API para enviar registos de terceiros para o portal.

Em Java para [loggers padrão, como Log4J, Logback](../../azure-monitor/app/java-trace-logs.md) utilizar o Application Insights Log4j ou Logback Appenders para enviar registos de terceiros para o portal.

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

*JavaScript do lado do cliente/Browser*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: AI.SeverityLevel)
```

Registar um evento de diagnóstico, como entrada ou a um método de saída.

 Parâmetro | Descrição
---|---
`message` | Dados de diagnóstico. Pode ser muito mais do que um nome.
`properties` | Mapa da cadeia de caracteres para a cadeia de caracteres: Dados adicionais utilizados para [filtrar exceções](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) no portal. Predefinição é vazio.
`severityLevel` | Valores suportados: [SeverityLevel.ts](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/JavaScript/JavaScriptSDK.Interfaces/Contracts/Generated/SeverityLevel.ts)

Pode pesquisar no conteúdo da mensagem, mas (ao contrário dos valores de propriedade) não é possível filtrar no mesmo.

O tamanho limite no `message` é muito maior do que o limite de propriedades.
Uma vantagem TrackTrace é que pode colocar os dados relativamente longos na mensagem. Por exemplo, pode codificar dados de POSTAGEM lá.  

Além disso, pode adicionar um nível de gravidade a sua mensagem. E, como outra telemetria, pode adicionar valores de propriedade para o ajudar a filtrar ou de pesquisa para diferentes conjuntos de rastreamentos. Por exemplo:

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

Na [pesquisa](../../azure-monitor/app/diagnostic-search.md), pode então filtrar facilmente todas as mensagens de um nível de gravidade específico que se relacionam com a base de dados específica.

### <a name="traces-in-analytics"></a>Rastreios do Analytics

Na [Application Insights Analytics](analytics.md), chamadas para TrackTrace aparecem no `traces` tabela.

Se [amostragem](../../azure-monitor/app/sampling.md) estar em funcionamento, a propriedade itemCount mostra um valor superior a 1. Para o exemplo itemCount = = 10 significa que de 10 chamadas para `trackTrace()`, o processo de amostragem transmitidos apenas um deles. Para obter uma contagem correta de chamadas de rastreio, deve usar, por conseguinte, código como `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency

Utilize a chamada de TrackDependency para controlar os tempos de resposta e taxas de êxito de chamadas para uma parte externa do código. Os resultados são apresentados nos gráficos de dependência no portal.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
     // The call above has been made obsolete in the latest SDK. The updated call follows this format:
     // TrackDependency (string dependencyTypeName, string dependencyName, string data, DateTimeOffset startTime, TimeSpan duration, bool success);
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

Lembre-se de que os SDKs de servidor incluem uma [módulo de dependência](../../azure-monitor/app/asp-net-dependencies.md) que Deteta e controla certas chamadas de dependência automaticamente – por exemplo, para bases de dados e REST APIs. Tem de instalar um agente no seu servidor para tornar o módulo de trabalho. 

No Java, certas chamadas de dependência podem ser automaticamente rastreadas com [agente Java](../../azure-monitor/app/java-agent.md).

Utilize esta chamada se desejar rastrear chamadas não captura o rastreamento automatizado, ou se não pretender instalar o agente.

Para desativar o módulo de controlo de dependência padrão em c#, edite [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) e exclua a referência ao `DependencyCollector.DependencyTrackingTelemetryModule`. No Java, não instale o agente java se não pretender recolher padrão dependências automaticamente.

### <a name="dependencies-in-analytics"></a>Dependências do Analytics

Na [Application Insights Analytics](analytics.md), trackDependency chama show cópia de segurança no `dependencies` tabela.

Se [amostragem](../../azure-monitor/app/sampling.md) estar em funcionamento, a propriedade itemCount mostra um valor superior a 1. Para o exemplo itemCount = = 10 significa que de 10 chamadas para trackDependency(), o processo de amostragem apenas transmitida uma delas. Para obter uma contagem correta das dependências segmentados por componente de destino, utilize o código como:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Para associar as dependências com os pedidos relacionados, utilize uma associação:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Dados de liberar

Normalmente, o SDK envia dados, às vezes escolhidos para minimizar o impacto no utilizador. No entanto, em alguns casos, pode querer liberar o buffer – por exemplo, se estiver a utilizar o SDK num aplicativo que seja fechado.

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

O ideal é que o método Flush () deve ser usado na atividade de encerramento do aplicativo.

## <a name="authenticated-users"></a>Utilizadores autenticados

Num aplicativo web, os utilizadores são (por predefinição) identificados por cookies. Um utilizador pode contar mais de uma vez se eles aceder à sua aplicação de um computador diferente ou o browser ou se eles eliminar cookies.

Se os utilizadores iniciarem sessão na sua aplicação, pode obter uma contagem mais precisa, definindo o ID de utilizador autenticado no código do browser:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

Numa web ASP.NET, aplicativo MVC, por exemplo:

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

Não é necessário utilizar o nome do usuário real início de sessão. Só tem de ser uma ID exclusiva para esse utilizador. Não pode incluir espaços ou qualquer um dos carateres `,;=|`.

O ID de utilizador também é definido num cookie de sessão e enviado para o servidor. Se o SDK do servidor estiver instalado, o ID de utilizador autenticado é enviado como parte de propriedades de contexto de telemetria de cliente e servidor. Em seguida, pode filtrar e pesquisa no mesmo.

Se a sua aplicação agrupa os utilizadores em contas, também pode passar um identificador para a conta (com as mesmas restrições de carateres).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

Na [Explorador de métricas](../../azure-monitor/app/metrics-explorer.md), pode criar um gráfico que conte **utilizadores, autenticado**, e **contas de utilizador**.

Também pode [pesquisa](../../azure-monitor/app/diagnostic-search.md) para pontos de dados de cliente com contas e nomes de utilizador específico.

## <a name="properties"></a>Filtragem, a pesquisa e a segmentar os seus dados utilizando as propriedades

Pode anexar propriedades e medidas para seus eventos (e também para métricas, página vistas, exceções e outros dados de telemetria).

*Propriedades* são valores de cadeia de caracteres que pode utilizar para filtrar a telemetria nos relatórios de utilização. Por exemplo, se a aplicação fornece vários jogos, pode anexar o assunto da cada evento, para que pode ver os jogos são mais populares.

Existe um limite de 8192 o comprimento de cadeia de caracteres. (Se deseja enviar grandes segmentos de dados, utilize o parâmetro de mensagem de TrackTrace.)

*Métricas* são valores numéricos que podem ser apresentados graficamente. Por exemplo, poderá ver se existe um aumento gradual nas classificações que os jogadores alcançar. Os gráficos podem ser segmentados pelas propriedades que são enviadas com o evento, para que pode obter separado ou empilhados gráficos de jogos diferentes.

Para valores de métrica ser apresentado corretamente, eles devem ser maior ou igual a 0.

Existem algumas [limites no número de propriedades, valores de propriedade e métricas](#limits) que pode utilizar.

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
> Tenha cuidado para não registar informações de identificação pessoal nas propriedades.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Forma alternativa para definir propriedades e métricas

Se for mais conveniente, é possível recolher os parâmetros de um evento num objeto separado:

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
> Não reutilize a mesma instância de item de telemetria (`event` neste exemplo) para chamar Track*() várias vezes. Isso pode causar a telemetria a ser enviados com a configuração incorreta.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Medições personalizadas e as propriedades no Analytics

Na [Analytics](analytics.md), métricas personalizadas e propriedades de mostram a `customMeasurements` e `customDimensions` atributos de cada registo de telemetria.

Por exemplo, se tiver adicionado uma propriedade chamada "game" à sua telemetria de pedido, esta consulta contagens as ocorrências dos valores diferentes de "game" e mostrar a média do "score" métrica personalizada:

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Tenha em atenção que:

* Quando extrair um valor do customDimensions ou customMeasurements JSON, ele tem o tipo dinâmico e, por isso, tem convertê-lo `tostring` ou `todouble`.
* Levar em conta a possibilidade de [amostragem](../../azure-monitor/app/sampling.md), deve usar `sum(itemCount)`, e não `count()`.

## <a name="timed"></a> Eventos de tempo

Às vezes deseja quanto tempo leva para executar uma ação de gráfico. Por exemplo, pode querer saber quanto usuários demoram para considerar as opções num jogo. Pode utilizar o parâmetro de medida para isso.

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
metrics.put("ProcessingTime", endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="defaults"></a>Propriedades padrão para telemetria personalizada

Se pretender definir valores de propriedade de padrão para alguns dos eventos personalizados que escreva, pode defini-las numa instância TelemetryClient. Estão ligados a todos os itens de telemetria é enviado desse cliente.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.Properties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.Properties("Game") = currentGame.Name
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

Chamadas de telemetria individuais podem substituir os valores predefinidos nos seus dicionários de propriedade.

*Para JavaScript clientes web*, utilize os inicializadores de telemetria de JavaScript.

*Para adicionar propriedades para toda a telemetria*, incluindo os dados a partir de módulos de coleção padrão [implementar `ITelemetryInitializer` ](../../azure-monitor/app/api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Amostragem, a filtragem e processamento de telemetria

Pode escrever código para processar a telemetria antes do envio do SDK. O processamento incluem dados que são enviados dos módulos de telemetria standard, como a coleção de pedido HTTP e coleção de dependência.

[Adicionar propriedades](../../azure-monitor/app/api-filtering-sampling.md#add-properties) à telemetria implementando `ITelemetryInitializer`. Por exemplo, pode adicionar números de versão ou valores que são calculadas a partir de outras propriedades.

[Filtragem](../../azure-monitor/app/api-filtering-sampling.md#filtering) pode modificar ou eliminar telemetria antes do envio do SDK ao implementar `ITelemetryProcessor`. Controlar o que é enviado ou eliminado, mas a considerar para o efeito sobre as suas métricas. Dependendo de como descartar itens, poderá perder a capacidade de navegar entre os itens relacionados.

[Amostragem](../../azure-monitor/app/api-filtering-sampling.md) é uma solução em pacote para reduzir o volume de dados que são enviados a partir da sua aplicação para o portal. Ele faz isso sem afetar as métricas apresentadas. E ele faz isso sem afetar a capacidade de diagnosticar problemas ao navegar entre os itens relacionados, tais como exceções, pedidos e vistas de página.

[Saiba mais](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Desativar a telemetria

Para *dinamicamente parar e iniciar* a recolha e a transmissão de telemetria:

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

Para *desativar recoletores padrão selecionadas*– por exemplo, os contadores de desempenho, pedidos de HTTP ou dependências – eliminar ou comente as linhas relevantes na [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Pode fazer isso, por exemplo, se pretender enviar os seus próprios dados TrackRequest.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Para *desativar selecionados recoletores padrão*– por exemplo, contadores de desempenho, pedidos de HTTP ou dependências – no momento da inicialização, encadeiam os métodos de configuração para o seu código de inicialização do SDK:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Para desativar estas recoletores após a inicialização, use o objeto de configuração: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Modo de programador

Durante a depuração, é útil para que a sua telemetria emitida através do pipeline, para que pode ver os resultados imediatamente. Também get mensagens adicionais que o ajudam a quaisquer problemas com a telemetria de rastreio. Desativar a definição na produção, porque pode reduzir a sua aplicação.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

Para node. js, pode ativar o modo de programador ao ativar o registo interno via `setInternalLogging` e a definição `maxBatchSize` como 0, que faz com que a telemetria ser enviado assim que são recolhido.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="ikey"></a> Definir a chave de instrumentação para telemetria personalizada selecionada

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-ikey"></a> Chave de instrumentação dinâmico

Para evitar a mistura de telemetria a partir de ambientes de desenvolvimento, teste e produção, pode [criar recursos do Application Insights separados](../../azure-monitor/app/create-new-resource.md ) e alterar as chaves, dependendo do ambiente.

Em vez de obter a chave de instrumentação do ficheiro de configuração, pode configurá-lo em seu código. Defina a chave num método de inicialização, como global.aspx.cs num serviço ASP.NET:

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

Páginas da Web, poderá configurá-lo a partir do servidor web Estado, em vez de codificação-lo, literalmente, para o script. Por exemplo, numa página Web gerada num aplicativo do ASP.NET:

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

## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient tem uma propriedade de contexto, que contém valores que são enviados juntamente com todos os dados de telemetria. Eles normalmente são definidos por módulos de telemetria standard, mas também pode defini-los por conta própria. Por exemplo:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Se definir qualquer um destes valores por conta própria, considere remover a linha relevante partir [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), para que não fiquem confusos seus valores e os valores padrão.

* **Componente**: A aplicação e sua versão.
* **Dispositivo**: Dados sobre o dispositivo em que a aplicação está em execução. (Em aplicações web, isso é o servidor ou dispositivo de cliente que a telemetria é enviada a partir.)
* **InstrumentationKey**: O recurso do Application Insights no Azure, onde a telemetria é apresentada. Ele é normalmente escolhido do applicationinsights. config.
* **Localização**: A localização geográfica do dispositivo.
* **Operação**: Nas aplicações web, a solicitação HTTP atual. Outros tipos de aplicação, pode definir esta opção para agrupar eventos em conjunto.
  * **ID**: Um valor gerado que correlaciona eventos diferentes, para que quando inspecionar qualquer evento na pesquisa de diagnósticos, pode encontrar itens relacionados.
  * **Nome**: Um identificador, geralmente, o URL da solicitação HTTP.
  * **SyntheticSource**: Se não for nulo ou vazio, uma cadeia que indica que a origem do pedido foi identificada como um teste de robô ou web. Por predefinição, esta é excluída da cálculos no Explorador de métricas.
* **Propriedades**: Propriedades que são enviadas com todos os dados de telemetria. Pode ser substituído no Roteiro * chamadas individuais.
* **Sessão**: A sessão do utilizador. O ID é definido como um valor gerado, o que é alterado quando o utilizador não esteve ativo durante algum tempo.
* **Utilizador**: Informações do utilizador.

## <a name="limits"></a>Limites

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Para evitar atingir o limite de taxa de dados, utilize [amostragem](../../azure-monitor/app/sampling.md).

Para determinar o tempo que os dados são mantidos, consulte [retenção de dados e privacidade](../../azure-monitor/app/data-retention-privacy.md).

## <a name="reference-docs"></a>Documentos de referência

* [Referência do ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Referência de Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Referência de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [SDK do Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [SDK do iOS](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>Código do SDK

* [SDK de ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Pacotes do Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [SDK Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [SDK JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)
* [Todas as plataformas](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Dúvidas

* *As exceções poderão lançar Track_() chamadas?*

    Nenhum. Não precisa de encapsulá-las nas cláusulas de try-catch. Se o SDK detectarem problemas, ele registrará mensagens na saída da consola de depuração e --se as mensagens de obtém por meio de – na pesquisa de diagnósticos.
* *Existe uma API REST para obter dados a partir do portal?*

    Sim, o [API de acesso a dados](https://dev.applicationinsights.io/). Outras formas de extrair dados incluem [exportar do Analytics ao Power BI](../../azure-monitor/app/export-power-bi.md ) e [a exportação contínua](../../azure-monitor/app/export-telemetry.md).

## <a name="next"></a>Passos seguintes

* [Pesquisar eventos e registos](../../azure-monitor/app/diagnostic-search.md)
* [Resolução de problemas](../../azure-monitor/app/troubleshoot-faq.md)
