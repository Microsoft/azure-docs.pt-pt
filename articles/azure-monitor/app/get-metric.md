---
title: Get-Metric em Azure Monitor Application Insights
description: Saiba como utilizar eficazmente a chamada GetMetric() para capturar métricas localmente pré-agregadas para aplicações .NET e .NET Core com Insights de Aplicação do Monitor Azure
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 0ce2651d5cfcb1578d78982af109a004aaac11f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101719785"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>Coleção métrica personalizada em .NET e .NET Core

Os Azure Monitor Application Insights .NET e .NET Core SDKs têm dois métodos diferentes de recolha de métricas `TrackMetric()` personalizadas, e `GetMetric()` . A principal diferença entre estes dois métodos é a agregação local. `TrackMetric()` carece de pré-agregação enquanto `GetMetric()` tem pré-agregação. A abordagem recomendada é utilizar a agregação, portanto, `TrackMetric()` já não é o método preferido de recolher métricas personalizadas. Este artigo irá levá-lo através do método GetMetric,.e algumas das razões por trás de como funciona.

## <a name="trackmetric-versus-getmetric"></a>TrackMetric versus GetMetric

`TrackMetric()` envia telemetria crua denotando uma métrica. É ineficiente enviar um único item de telemetria por cada valor. `TrackMetric()` é também ineficiente em termos de desempenho, uma vez que cada `TrackMetric(item)` um passa pelo oleoduto SDK completo de inicializadores e processadores de telemetria. Ao contrário `TrackMetric()` de , lida com a `GetMetric()` pré-agregação local para si e, em seguida, apenas submete uma métrica de resumo agregado num intervalo fixo de um minuto. Por isso, se necessitar de monitorizar de perto alguma métrica personalizada ao segundo ou mesmo ao nível milissegundo, pode fazê-lo apenas incorrendo no custo de armazenamento e tráfego da rede apenas de monitorização a cada minuto. Isto também reduz consideravelmente o risco de estrangulamento, uma vez que o número total de itens de telemetria que precisam de ser enviados para uma métrica agregada são muito reduzidos.

No Application Insights, as métricas personalizadas recolhidas através `TrackMetric()` e `GetMetric()` não estão sujeitas a [amostragem.](./sampling.md) A amostragem de métricas importantes pode levar a cenários em que o alerta que pode ter construído em torno dessas métricas pode tornar-se pouco fiável. Ao nunca provar as suas métricas personalizadas, pode geralmente estar confiante de que quando os seus limiares de alerta são violados, um alerta disparará.  Mas como as métricas personalizadas não são amostradas, existem algumas preocupações potenciais.

Se precisar acompanhar as tendências numa métrica a cada segundo, ou num intervalo ainda mais granular, isso pode resultar em:

- Aumento dos custos de armazenamento de dados. Há um custo associado à quantidade de dados que envia para o Azure Monitor. (Quanto mais dados enviar, maior é o custo global de monitorização.)
- Aumento do tráfego de rede/sobrecarga de desempenho. (Em alguns cenários, isto poderia ter um custo de desempenho monetário e de aplicação.)
- Risco de estrangulamento de ingestão. (O serviço Azure Monitor deixa cair pontos de dados ("aceleradores") quando a sua aplicação envia uma taxa muito elevada de telemetria num curto intervalo de tempo.)

O estrangulamento é particularmente preocupante na medida em que, tal como a amostragem, o estrangulamento pode levar a alertas perdidos, uma vez que a condição para desencadear um alerta pode ocorrer localmente e depois ser largada no ponto final de ingestão devido ao envio de demasiados dados. É por isso que para .NET e .NET Core não recomendamos a utilização `TrackMetric()` a menos que tenha implementado a sua própria lógica de agregação local. Se estiver a tentar rastrear cada caso em que um evento ocorra durante um determinado período de tempo, poderá descobrir que [`TrackEvent()`](./api-custom-events-metrics.md#trackevent) é um ajuste melhor. Embora tenha em mente que, ao contrário das métricas personalizadas, os eventos personalizados estão sujeitos a amostragem. Claro que ainda pode usar `TrackMetric()` mesmo sem escrever a sua própria pré-agregação local, mas se o fizer, esteja ciente das armadilhas.

Em resumo `GetMetric()` é a abordagem recomendada, uma vez que faz pré-agregação, acumula valores de todas as chamadas track() e envia um resumo/agregado uma vez a cada minuto. Isto pode reduzir significativamente o custo e a sobrecarga de desempenho enviando menos pontos de dados, enquanto ainda recolhe todas as informações relevantes.

> [!NOTE]
> Apenas os SDKs .NET e .NET têm um método GetMetric(). Se estiver a utilizar Java, pode utilizar [métricas de Micrometro ou](./micrometer-java.md) `TrackMetric()` . Para o JavaScript e Node.js ainda `TrackMetric()` utilizarias , mas tenhas em mente as ressalvas que foram delineadas na secção anterior. Para Python pode utilizar [OpenCensus.stats](./opencensus-python.md#metrics) para enviar métricas personalizadas, mas a implementação das métricas é diferente.

## <a name="getting-started-with-getmetric"></a>Começar com GetMetric

Para os nossos exemplos, vamos utilizar uma aplicação básica de serviço de trabalhador .NET Core 3.1. Se quiser replicar exatamente o ambiente de teste que foi utilizado com estes exemplos, siga os passos 1-6 do artigo de serviço de [monitorização](./worker-service.md#net-core-30-worker-service-application) para adicionar Insights de Aplicação a um modelo de projeto de serviço básico do trabalhador. Estes conceitos aplicam-se a qualquer aplicação geral onde o SDK possa ser usado, incluindo aplicações web e aplicações de consola.

### <a name="sending-metrics"></a>Envio de métricas

Substitua o conteúdo do seu `worker.cs` ficheiro pelo seguinte:

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
                _telemetryClient.GetMetric("ComputersSold").TrackValue(42);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(1000, stoppingToken);
            }
        }
    }
}
```

Se executar o código acima e ver a telemetria ser enviada através da janela de saída do Estúdio Visual ou uma ferramenta como o Fiddler de Telerik, verá o loop de execução repetidamente sem que nenhuma telemetria seja enviada e, em seguida, um único item de telemetria será enviado por volta da marca de 60 segundos, que no caso do nosso teste parece o seguinte :

```json
Application Insights Telemetry: {"name":"Microsoft.ApplicationInsights.Dev.00000000-0000-0000-0000-000000000000.Metric", "time":"2019-12-28T00:54:19.0000000Z",
"ikey":"00000000-0000-0000-0000-000000000000",
"tags":{"ai.application.ver":"1.0.0.0",
"ai.cloud.roleInstance":"Test-Computer-Name",
"ai.internal.sdkVersion":"m-agg2c:2.12.0-21496",
"ai.internal.nodeName":"Test-Computer-Name"},
"data":{"baseType":"MetricData",
"baseData":{"ver":2,"metrics":[{"name":"ComputersSold",
"kind":"Aggregation",
"value":1722,
"count":41,
"min":42,
"max":42,
"stdDev":0}],
"properties":{"_MS.AggregationIntervalMs":"42000",
"DeveloperMode":"true"}}}}
```

Este item de telemetria única representa um agregado de 41 medições métricas distintas. Uma vez que enviamos o mesmo valor uma e outra vez temos um *desvio padrão (stDev)* de 0 com um máximo idêntico *(máximo)* e *valores mínimos (min).* A propriedade *de valor* representa uma soma de todos os valores individuais que foram agregados.

> [!NOTE]
> A GetMetric não suporta rastrear o último valor (isto é, "bitola") ou rastrear histogramas/distribuições.

Se examinarmos o nosso recurso Application Insights na experiência Logs (Analytics), este item de telemetria individual será o seguinte:

![Vista de consulta log analytics](./media/get-metric/log-analytics.png)

> [!NOTE]
> Embora o item de telemetria bruta não contenha uma propriedade/campo de soma explícita uma vez ingerido, criamos um para si. Neste caso, tanto a propriedade como a `value` propriedade representam a mesma `valueSum` coisa.

Também pode aceder à sua telemetria métrica personalizada na secção [_Métricas_](../essentials/metrics-charts.md) do portal. Como uma [métrica baseada em log e personalizado.](pre-aggregated-metrics-log-metrics.md) (A imagem abaixo é um exemplo de log-based.) ![Vista exploradora de métricas](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>Caching referência métrica para utilização de alta produção

Em alguns casos, os valores métricos são observados com muita frequência. Por exemplo, um serviço de alta produção que processa 500 pedidos/segundo pode querer emitir 20 métricas de telemetria para cada pedido. Isto significa rastrear 10.000 valores por segundo. Nestes cenários de alta produção, os utilizadores podem precisar de ajudar o SDK evitando algumas procuras.

Por exemplo, neste caso, o exemplo acima realizou uma procura de um cabo para a métrica "ComputersSold" e, em seguida, rastreou um valor observado 42. Em vez disso, a pega pode ser em cache para várias invocações de faixa:

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

Além de caching o cabo métrico, o exemplo acima também reduziu o `Task.Delay` para 50 milissegundos para que o loop executasse com mais frequência resultando em 772 `TrackValue()` invocações.

## <a name="multi-dimensional-metrics"></a>Métricas multidimensionais

Os exemplos na secção anterior mostram métricas de dimensão zero. As métricas também podem ser multidimensionais. Atualmente apoiamos até 10 dimensões.

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

A execução deste código durante pelo menos 60 segundos resultará no envio de três itens distintos de telemetria para Azure, cada um representando a agregação de um dos três fatores de forma. Como antes pode examiná-los na vista Logs (Analytics):

![Vista de análise de log da métrica multidimensional](./media/get-metric/log-analytics-multi-dimensional.png)

Bem como na experiência de explorador métricas:

![Métricas personalizadas](./media/get-metric/custom-metrics.png)

No entanto, você vai notar que você não é capaz de dividir a métrica pela sua nova dimensão personalizada, ou ver a sua dimensão personalizada com a vista métrica:

![Apoio de divisão](./media/get-metric/splitting-support.png)

Por padrão, as métricas multidimensionais dentro da experiência do explorador métrico não são ligadas nos recursos de Insights de Aplicação.

### <a name="enable-multi-dimensional-metrics"></a>Permitir métricas multidimensionais

Para permitir métricas multidimensionais para um recurso Application Insights, selecione **o uso e os custos**  >  **estimados As métricas personalizadas**  >  **Permitem alertar sobre as dimensões métricas personalizadas**  >  **OK**. Mais detalhes sobre isto podem ser encontrados [aqui.](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)

Uma vez que tenha feito essa alteração e envie uma nova telemetria multidimensional, poderá aplicar a **divisão**.

> [!NOTE]
> Só as métricas recentemente enviadas depois de a funcionalidade ter sido ligada no portal terão dimensões armazenadas.

![Aplicar divisão](./media/get-metric/apply-splitting.png)

E veja as suas agregações métricas para cada dimensão _FormFactor:_

![Fatores de forma](./media/get-metric/formfactor.png)

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions&quot;></a>Como usar o MetricIdentifier quando há mais de três dimensões

Atualmente são suportadas 10 dimensões, no entanto, mais de três dimensões requer a utilização `MetricIdentifier` de:

```csharp
// Add &quot;using Microsoft.ApplicationInsights.Metrics;&quot; to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier(&quot;[metricNamespace]&quot;,&quot;[metricId],&quot;[dim1]&quot;,&quot;[dim2]&quot;,&quot;[dim3]&quot;,&quot;[dim4]&quot;,&quot;[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

## <a name="custom-metric-configuration"></a>Configuração métrica personalizada

Se quiser alterar a configuração métrica, tem de o fazer no local onde a métrica é inicializada.

### <a name="special-dimension-names"></a>Nomes de dimensão especial

As métricas não usam o contexto de telemetria dos `TelemetryClient` usados para aceder a eles, nomes de dimensão especial disponíveis como constantes na `MetricDimensionNames` classe é a melhor solução para esta limitação.

Os agregados métricos enviados pela métrica abaixo "Special Operation Request Size" **não** terão o seu `Context.Operation.Name` conjunto para "Operação Especial". Considerando `TrackMetric()` que ou qualquer outro TrackXXX() terá `OperationName` definido corretamente para "Operação Especial".

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

Nestas circunstâncias, utilize os nomes de dimensão especial listados `MetricDimensionNames` na classe para especificar `TelemetryContext` valores.

Por exemplo, quando o agregado métrico resultante da próxima declaração for enviado para o ponto final da nuvem Application Insights, o seu `Context.Operation.Name` campo de dados será definido como "Operação Especial":

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

Os valores desta dimensão especial serão copiados para a `TelemetryContext` dimensão e não serão utilizados como uma dimensão 'normal'. Se também pretender manter uma dimensão de funcionamento para a exploração métrica normal, tem de criar uma dimensão separada para o efeito:

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>Dimensionamento de dimensões e séries de tempo

 Para evitar que o subsistema de telemetria utilize acidentalmente os seus recursos, pode controlar o número máximo de séries de dados por métrica. Os limites predefinidos não são mais de 1000 séries totais de dados por métrica, e não mais de 100 valores diferentes por dimensão.

 No contexto da dimensão e da cobertura das séries de tempo, utilizamos `Metric.TrackValue(..)` para garantir que os limites são respeitados. Se os limites já estiverem atingidos, `Metric.TrackValue(..)` devolverá "Falso" e o valor não será rastreado. Caso contrário, voltará a "Verdadeiro". Isto é útil se os dados para uma métrica mente forem originários da entrada do utilizador.

O `MetricConfiguration` construtor toma algumas opções sobre como gerir diferentes séries dentro da respetiva métrica e um objeto de uma implementação de classe `IMetricSeriesConfiguration` que especifica o comportamento de agregação para cada série individual da métrica:

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

* `seriesCountLimit` é o número máximo de datas séries que uma métrica pode conter. Uma vez atingido este limite, as chamadas `TrackValue()` não serão rastreadas.
* `valuesPerDimensionLimit` limita o número de valores distintos por dimensão de forma semelhante.
* `restrictToUInt32Values` determina se os valores inteiros não negativos devem ou não ser rastreados.

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

* [Saiba mais ](./worker-service.md)sobre a monitorização das aplicações de serviço dos trabalhadores.
* Para mais detalhes sobre [métricas baseadas em registos e pré-agregadas](./pre-aggregated-metrics-log-metrics.md).
* [Explorador métrico](../essentials/metrics-getting-started.md)
* Como ativar insights de aplicações para [aplicações ASP.NET core](asp-net-core.md)
* Como ativar insights de aplicações para [aplicações ASP.NET](asp-net.md)
