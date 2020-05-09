---
title: Get-Metric in Azure Monitor Application Insights
description: Saiba como utilizar eficazmente a chamada GetMetric para capturar métricas pré-agregadas localmente para aplicações .NET e .NET Core com Insights de Aplicação do Monitor Azure
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/28/2020
ms.openlocfilehash: 94525ce901a89935c4ee7800ada44a9dff84b27a
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927909"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>Coleção métrica personalizada em .NET e .NET Core

Os Insights de Aplicação do Monitor Azure .NET e .NET Core SDKs têm dois métodos diferentes de recolha de métricas personalizadas, `TrackMetric()`e `GetMetric()`. A diferença-chave entre estes dois métodos é a agregação local. `TrackMetric()`falta pré-agregação enquanto `GetMetric()` tem pré-agregação. A abordagem recomendada é utilizar a agregação, portanto, `TrackMetric()` já não é o método preferido de recolher métricas personalizadas. Este artigo irá acompanhá-lo através do método GetMetric() e algumas das razões por trás de como funciona.

## <a name="trackmetric-versus-getmetric"></a>TrackMetric versus GetMetric

`TrackMetric()`envia telemetria crua denotando uma métrica. É ineficiente enviar um único item de telemetria para cada valor. `TrackMetric()`é também ineficiente em termos `TrackMetric(item)` de desempenho, uma vez que cada um passa pelo oleoduto SDK completo de iniciantes e processadores de telemetria. Ao `TrackMetric()` `GetMetric()` contrário, trata da pré-agregação local para si e, em seguida, apenas apresenta uma métrica de resumo agregado num intervalo fixo de um minuto. Por isso, se precisar de monitorizar de perto alguma métrica personalizada no segundo ou mesmo milissegundo, pode fazê-lo enquanto apenas incorre no custo de armazenamento e tráfego de rede de apenas monitorização a cada minuto. Isto também reduz consideravelmente o risco de estrangulamento que ocorre, uma vez que o número total de artigos de telemetria que precisam de ser enviados para uma métrica agregada são muito reduzidos.

Em Application Insights, as `TrackMetric()` métricas personalizadas recolhidas via e `GetMetric()` não estão sujeitas a [amostragem.](https://docs.microsoft.com/azure/azure-monitor/app/sampling) A amostragem de métricas importantes pode levar a cenários em que alertar pode ter construído em torno dessas métricas pode tornar-se pouco fiável. Ao nunca provar as suas métricas personalizadas, geralmente pode estar confiante de que quando os seus limiares de alerta forem violados, um alerta disparará.  Mas como as métricas personalizadas não são amostradas, há algumas preocupações potenciais.

Se precisar acompanhar as tendências numa métrica a cada segundo, ou num intervalo ainda mais granular, isto pode resultar em:

- Aumento dos custos de armazenamento de dados. Há um custo associado à quantidade de dados que envia ao Monitor Azure. (Quanto mais dados enviarmos, maior o custo global de monitorização.)
- Aumento do tráfego/desempenho da rede. (Em alguns cenários, isto poderia ter um custo de desempenho monetário e de aplicação.)
- Risco de estrangulamento da ingestão. (O serviço Azure Monitor cai ("aceleradores") pontos de dados quando a sua aplicação envia uma taxa muito elevada de telemetria num curto intervalo de tempo.)

O estrangulamento é particularmente preocupante na medida em que, tal como a amostragem, o estrangulamento pode levar a alertas perdidos, uma vez que a condição de desencadear um alerta pode ocorrer localmente e depois ser retirada no ponto final da ingestão devido ao envio de demasiados dados. É por isso que para .NET e .NET Core não recomendamos usar `TrackMetric()` a menos que tenha implementado a sua própria lógica de agregação local. Se estiver a tentar rastrear cada caso que um evento ocorre [`TrackEvent()`](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics#trackevent) durante um determinado período de tempo, pode descobrir que é um melhor encaixe. Embora tenha em mente que, ao contrário das métricas personalizadas, os eventos personalizados estão sujeitos a amostragem. É claro que `TrackMetric()` ainda pode usar mesmo sem escrever a sua própria pré-agregação local, mas se o fizer, esteja ciente das armadilhas.

Em resumo `GetMetric()` é a abordagem recomendada uma vez que faz a pré-agregação, acumula valores de todas as chamadas da Pista e envia um resumo/agregado uma vez a cada minuto. Isto pode reduzir significativamente o custo e a sobrecarga de desempenho enviando menos pontos de dados, ao mesmo tempo que recolhe todas as informações relevantes.

> [!NOTE]
> Apenas os SDKs .NET e .NET Core têm um método GetMetric() Se estiver a utilizar java, pode `TrackMetric()`utilizar métricas de [Micrometro](https://docs.microsoft.com/azure/azure-monitor/app/micrometer-java) ou . Para python pode utilizar [OpenCensus.stats](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#metrics) para enviar métricas personalizadas. Para JavaScript e Node.js `TrackMetric()`ainda utilizaria, mas tenha em mente as ressalvas que foram descritas na secção anterior.

## <a name="getting-started-with-getmetric"></a>Começando com GetMetric

Para os nossos exemplos, vamos usar uma aplicação básica de serviço de trabalhador .NET Core 3.1. Se quiser replicar exatamente o ambiente de teste que foi utilizado com estes exemplos, siga os passos 1-6 do artigo de serviço dos trabalhadores de [monitorização](https://docs.microsoft.com/azure/azure-monitor/app/worker-service#net-core-30-worker-service-application) para adicionar insights de aplicação a um modelo de projeto de serviço de trabalho básico. Estes conceitos aplicam-se a qualquer aplicação geral onde o SDK possa ser usado, incluindo aplicações web e aplicações de consola.

### <a name="sending-metrics"></a>Envio de métricas

Substitua o `worker.cs` conteúdo do seu ficheiro pelo seguinte:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.ApplicationInsights;

namespace WorkerService3
{
    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {   // The following line demonstrates usages of GetMetric API.
            // Here "computersSold", a custom metric name, is being tracked with a value of 42 every second.
            while (!stoppingToken.IsCancellationRequested)
            {
                _telemetryClient.GetMetric("computersSold").TrackValue(42);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(1000, stoppingToken);
            }
        }
    }
}
```

Se executar o código acima e ver a telemetria a ser enviada através da janela de saída do Estúdio Visual ou de uma ferramenta como o Violinista de Telerik, verá o loop enquanto executa repetidamente sem que seja enviada telemetria e, em seguida, um único item de telemetria será enviado por cerca de 60 segundos, que no caso do nosso teste parece o seguinte :

```json
Application Insights Telemetry: {"name":"Microsoft.ApplicationInsights.Dev.00000000-0000-0000-0000-000000000000.Metric", "time":"2019-12-28T00:54:19.0000000Z",
"ikey":"00000000-0000-0000-0000-000000000000",
"tags":{"ai.application.ver":"1.0.0.0",
"ai.cloud.roleInstance":"Test-Computer-Name",
"ai.internal.sdkVersion":"m-agg2c:2.12.0-21496",
"ai.internal.nodeName":"Test-Computer-Name"},
"data":{"baseType":"MetricData",
"baseData":{"ver":2,"metrics":[{"name":"computersSold",
"kind":"Aggregation",
"value":1722,
"count":41,
"min":42,
"max":42,
"stdDev":0}],
"properties":{"_MS.AggregationIntervalMs":"42000",
"DeveloperMode":"true"}}}}
```

Este único item de telemetria representa um agregado de 41 medições métricas distintas. Como estávamos enviando o mesmo valor repetidamente temos um *desvio padrão (stDev)* de 0 com um máximo idêntico *(máx)* e *valores mínimos (min).* O *valor* patrimonial representa uma soma de todos os valores individuais agregados.

Se examinarmos o nosso recurso Application Insights na experiência Logs (Analytics), este item de telemetria individual seria o seguinte:

![Vista de consulta de Log Analytics](./media/get-metric/log-analytics.png)

> [!NOTE]
> Embora o item de telemetria cru não contenha uma propriedade/campo de soma explícita uma vez ingerido, criamos um para si. Neste caso, `value` tanto `valueSum` a propriedade como a propriedade representam a mesma coisa.

Também pode aceder à sua telemetria métrica personalizada na secção [_Métricas_](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) do portal. Como uma [métrica baseada em diário de bordo e personalizada.](pre-aggregated-metrics-log-metrics.md) (A imagem abaixo é um exemplo de log-based.) ![Visão do explorador de métricas](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>Referência métrica de cache para uso de alto-consumo

Em alguns casos, os valores métricos são observados com muita frequência. Por exemplo, um serviço de alta-adesão que processa 500 pedidos/segundo pode querer emitir 20 métricas de telemetria para cada pedido. Isto significa rastrear 10.000 valores por segundo. Nestes cenários de alta-adesão, os utilizadores podem precisar de ajudar o SDK evitando algumas procurações.

Por exemplo, neste caso, o exemplo acima realizou um look up para um cabo para a métrica "ComputersSold" e, em seguida, rastreou um valor observado 42. Em vez disso, a pega pode ser colocada em cache para múltiplas invocações de faixa:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is where the cache is stored to handle faster lookup
            Metric computersSold = _telemetryClient.GetMetric("ComputersSold");
            while (!stoppingToken.IsCancellationRequested)
            {

                computersSold.TrackValue(42);

                computersSold.TrackValue(142);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

Além de cortar o cabo métrico, o `Task.Delay` exemplo acima também reduziu os 50 milissegundos `TrackValue()` para que o loop executasse com mais frequência resultando em 772 invocações.

## <a name="multi-dimensional-metrics"></a>Métricas multidimensionais

Os exemplos na secção anterior mostram métricas unidimensionais zero. As métricas também podem ser multidimensionais. Atualmente apoiamos até 10 dimensões.

 Aqui está um exemplo de como criar uma métrica unidimensional:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is an example of a metric with a single dimension.
            // FormFactor is the name of the dimension.
            Metric computersSold= _telemetryClient.GetMetric("ComputersSold", "FormFactor");

            while (!stoppingToken.IsCancellationRequested)
            {
                // The number of arguments (dimension values)
                // must match the number of dimensions specified while GetMetric.
                // Laptop, Tablet, etc are values for the dimension "FormFactor"
                computersSold.TrackValue(42, "Laptop");
                computersSold.TrackValue(20, "Tablet");
                computersSold.TrackValue(126, "Desktop");


                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

Executar este código durante pelo menos 60 segundos resultará na envio de três itens de telemetria distintos para o Azure, cada um representando a agregação de um dos três fatores de forma. Como antes de poder examiná-los na vista Registos (Analytics):

![Vista de análise de log da métrica multidimensional](./media/get-metric/log-analytics-multi-dimensional.png)

Assim como na experiência do explorador de Métricas:

![Métricas personalizadas](./media/get-metric/custom-metrics.png)

No entanto, você vai notar que você não é capaz de dividir a métrica pela sua nova dimensão personalizada, ou ver a sua dimensão personalizada com a visão métrica:

![Apoio à divisão](./media/get-metric/splitting-support.png)

Por padrão, as métricas multidimensionais dentro da experiência do explorador métrico não são ligadas nos recursos da Application Insights.

### <a name="enable-multi-dimensional-metrics"></a>Ativar métricas multidimensionais

Para ativar métricas multidimensionais para um recurso Deinsights de Aplicação, Selecione **o uso e os custos** > estimados As**métricas personalizadas** > **Ative alertar sobre dimensões métricas personalizadas** > **OK**. Mais detalhes sobre isto podem ser encontrados [aqui.](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)

Depois de ter feito essa alteração e enviar uma nova telemetria multidimensional, poderá **aplicar a divisão**.

> [!NOTE]
> Só as métricas recém-enviadas após a adesão ter sido ligada no portal terão dimensões armazenadas.

![Aplicar divisão](./media/get-metric/apply-splitting.png)

E veja as suas agregações métricas para cada dimensão _do FormFactor:_

![Fatores de forma](./media/get-metric/formfactor.png)

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions"></a>Como utilizar o MetricIdentifier quando existem mais de três dimensões

Atualmente, 10 dimensões são suportadas, no entanto, `MetricIdentifier`mais de três dimensões requer a utilização de:

```csharp
// Add "using Microsoft.ApplicationInsights.Metrics;" to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier("[metricNamespace]","[metricId],"[dim1]","[dim2]","[dim3]","[dim4]","[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

## <a name="custom-metric-configuration"></a>Configuração métrica personalizada

Se quiser alterar a configuração métrica, tem de o fazer no local onde a métrica é inicializada.

### <a name="special-dimension-names"></a>Nomes de dimensão especial

As métricas não usam o contexto `TelemetryClient` de telemetria do uso utilizado `MetricDimensionNames` para aceder a eles, nomes de dimensão especial disponíveis como constantes na aula é a melhor supressão para esta limitação.

Os agregados métricos enviados pela métrica abaixo **not** da `Context.Operation.Name` "Operação Especial Solicitação tamanho" não terão o seu conjunto de "Operação Especial". Considerando `TrackMetric()` que ou qualquer outra TrackXXX() terá `OperationName` definido corretamente a "Operação Especial".

``` csharp
        //...
        TelemetryClient specialClient;
        private static int GetCurrentRequestSize()
        {
            // Do stuff
            return 1100;
        }
        int requestSize = GetCurrentRequestSize()

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                //...
                specialClient.Context.Operation.Name = "Special Operation";
                specialClient.GetMetric("Special Operation Request Size").TrackValue(requestSize);
                //...
            }
                   
        }
```

Nestas circunstâncias, utilize os nomes `MetricDimensionNames` de dimensão `TelemetryContext` especial listados na classe para especificar valores.

Por exemplo, quando o agregado métrico resultante da próxima declaração for `Context.Operation.Name` enviado para o ponto final da nuvem Deinsights de Aplicação, o seu campo de dados será definido para "Operação Especial":

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

Os valores desta dimensão especial `TelemetryContext` serão copiados para a dimensão "normal". Se também quiser manter uma dimensão de operação para a exploração métrica normal, precisa de criar uma dimensão separada para o efeito:

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>Dimensione e séries temporais

 Para evitar que o subsistema de telemetria utilize acidentalmente os seus recursos, pode controlar o número máximo de séries de dados por métrica. Os limites predefinidos não são mais do que 1000 séries totais de dados por métrica, e não mais de 100 valores diferentes por dimensão.

 No contexto da dimensionação e da `Metric.TrackValue(..)` limitação da série temporal, utilizamos para garantir que os limites são observados. Se os limites já `Metric.TrackValue(..)` estiverem atingidos, devolverá "Falso" e o valor não será rastreado. Caso contrário, devolverá "True". Isto é útil se os dados de uma métrica forem originários da entrada do utilizador.

O `MetricConfiguration` construtor toma algumas opções sobre como gerir diferentes séries `IMetricSeriesConfiguration` dentro da respetiva métrica e um objeto de implementação de classe que especifica o comportamento de agregação para cada série individual da métrica:

``` csharp
var metConfig = new MetricConfiguration(seriesCountLimit: 100, valuesPerDimensionLimit:2,
                new MetricSeriesConfigurationForMeasurement(restrictToUInt32Values: false));

Metric computersSold = _telemetryClient.GetMetric("ComputersSold", "Dimension1", "Dimension2", metConfig);

// Start tracking.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value1");
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value2");

// The following call gives 3rd unique value for dimension2, which is above the limit of 2.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3");
// The above call does not track the metric, and returns false.
```

* `seriesCountLimit`é o número máximo de data time series que uma métrica pode conter. Uma vez atingido este limite, chama para `TrackValue()`.
* `valuesPerDimensionLimit`limita o número de valores distintos por dimensão de forma semelhante.
* `restrictToUInt32Values`determina se apenas devem ser rastreados valores inteiros não negativos.

Aqui está um exemplo de como enviar uma mensagem para saber se os limites da tampa são ultrapassados:

```csharp
if (! computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3"))
{
// Add "using Microsoft.ApplicationInsights.DataContract;" to use SeverityLevel.Error
_telemetryClient.TrackTrace("Metric value not tracked as value of one of the dimension exceeded the cap. Revisit the dimensions to ensure they are within the limits",
SeverityLevel.Error);
}
```

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais ](https://docs.microsoft.com/azure/azure-monitor/app/worker-service)sobre a monitorização das aplicações de serviço dos trabalhadores.
* Para mais detalhes sobre [métricas baseadas em log e pré-agregadas](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics).
* [Explorador Métrico](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)
* Como ativar insights de aplicação para [aplicações centrais ASP.NET](asp-net-core.md)
* Como ativar insights de aplicação para [aplicações ASP.NET](asp-net.md)
