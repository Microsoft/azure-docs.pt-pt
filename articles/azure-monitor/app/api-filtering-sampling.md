---
title: Filtragem e pré-processamento no Azure Application Insights SDK [ Microsoft Docs
description: Escreva processadores de telemetria e iniciais de telemetria para o SDK filtrar ou adicionar propriedades aos dados antes da telemetria ser enviada para o portal Application Insights.
ms.topic: conceptual
ms.date: 11/23/2016
ms.openlocfilehash: 53b6ecc51961feba35d571eab3115c8e7ccf9964
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366297"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtragem e telemetria pré-processamento no SDK de Insights de Aplicação

Pode escrever e configurar plug-ins para o Application Insights SDK para personalizar como a telemetria pode ser enriquecida e processada antes de ser enviada para o serviço Application Insights.

* [A amostragem](sampling.md) reduz o volume de telemetria sem afetar as suas estatísticas. Mantém juntos pontos de dados relacionados para que possa navegar entre eles ao diagnosticar um problema. No portal, as contagens totais são multiplicadas para compensar a amostragem.
* Filtrar com processadores de telemetria permite filtrar a telemetria no SDK antes de ser enviada para o servidor. Por exemplo, poderia reduzir o volume de telemetria excluindo pedidos de robôs. A filtragem é uma abordagem mais básica para reduzir o tráfego do que a amostragem. Permite-lhe um maior controlo sobre o que é transmitido, mas tem de estar ciente de que afeta as suas estatísticas - por exemplo, se filtrar todos os pedidos bem sucedidos.
* [Os Iniciais de Telemetria adicionam ou modificam propriedades](#add-properties) a qualquer telemetria enviada da sua aplicação, incluindo telemetria dos módulos padrão. Por exemplo, pode adicionar valores calculados; ou números de versão para filtrar os dados no portal.
* [O SDK API](../../azure-monitor/app/api-custom-events-metrics.md) é usado para enviar eventos e métricas personalizados.

Antes de começar:

* Instale o SDK apropriado para a sua aplicação: [ASP.NET](asp-net.md), [ASP.NET Core,](asp-net-core.md) [Non HTTP/Worker para .NET/.NET Core,](worker-service.md) [Java](../../azure-monitor/app/java-get-started.md) ou [JavaScript](javascript.md)

<a name="filtering"></a>

## <a name="filtering"></a>Filtragem

Esta técnica dá-lhe controlo direto sobre o que está incluído ou excluído do fluxo de telemetria. A filtragem pode ser usada para deixar os itens de telemetria de serem enviados para A Aplicação Insights. Pode usá-lo em conjunto com a Amostragem, ou separadamente.

Para filtrar a telemetria, escreve um processador `TelemetryConfiguration`de telemetria e regista-o com o . Toda a telemetria passa pelo seu processador, e pode optar por deixá-la cair do fluxo ou dá-la ao próximo processador da cadeia. Isto inclui telemetria a partir dos módulos padrão, como o colecionador de pedidos HTTP e o colecionador de dependência, e telemetria que você próprio seguiu. Pode, por exemplo, filtrar a telemetria sobre pedidos de robôs ou chamadas de dependência bem sucedidas.

> [!WARNING]
> Filtrar a telemetria enviada do SDK utilizando processadores pode distorcer as estatísticas que vê no portal e dificultar o seguimento de itens relacionados.
>
> Em vez disso, considere usar [amostragem.](../../azure-monitor/app/sampling.md)
>
>

### <a name="create-a-telemetry-processor-c"></a>Criar um processador de telemetria (C#)

1. Para criar um `ITelemetryProcessor`filtro, implemente.

    Note que os processadores de telemetria constroem uma cadeia de processamento. Ao instantaneamente um processador de telemetria, é-lhe dada uma referência ao próximo processador da cadeia. Quando um ponto de dados de telemetria é passado para o método do Processo, ele faz o seu trabalho e, em seguida, chama (ou não chamadas) o próximo Processador de Telemetria na cadeia.

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. Adicione o seu processador.

**aplicações ASP.NET** Insira este corte em ApplicationInsights.config:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

Pode passar valores de cordas a partir do ficheiro .config, fornecendo propriedades nomeadas ao público na sua classe.

> [!WARNING]
> Tome cuidado para combinar com o nome do tipo e quaisquer nomes de propriedade no ficheiro .config com os nomes de classe e propriedade no código. Se o ficheiro .config se referir a um tipo ou propriedade inexistente, o SDK pode não enviar silenciosamente qualquer telemetria.
>

**Em alternativa,** pode inicializar o filtro em código. Numa aula de inicialização adequada - `Global.asax.cs` por exemplo, appStart in - insira o seu processador na cadeia:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

TelemettryClientes criados depois deste ponto usarão os seus processadores.

**ASP.NET aplicativos Core/ Worker Service**

> [!NOTE]
> Adicionar o `ApplicationInsights.config` processador `TelemetryConfiguration.Active` utilizando ou utilizar não é válido para ASP.NET aplicações Core ou se estiver a utilizar o Microsoft.ApplicationInsights.WorkerService SDK.

Para aplicações escritas com [base ASP.NET](asp-net-core.md#adding-telemetry-processors) `TelemetryProcessor` Core ou `AddApplicationInsightsTelemetryProcessor` [WorkerService,](worker-service.md#adding-telemetry-processors)adicionar um novo é feito utilizando o método de extensão, `IServiceCollection`como mostrado abaixo. Este método é `ConfigureServices` chamado `Startup.cs` em método da sua classe.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Filtros de exemplo

#### <a name="synthetic-requests"></a>Pedidos sintéticos

Filtrar bots e testes web. Embora o Metrics Explorer lhe dê a opção de filtrar fontes sintéticas, esta opção reduz o tamanho do tráfego e da ingestão filtrando-as no próprio SDK.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Autenticação falhada

Filtrar os pedidos com uma resposta "401".

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrar chamadas de dependência remota rápida

Se quiser diagnosticar chamadas que sejam lentas, filtre as rápidas.

> [!NOTE]
> Isto vai distorcer as estatísticas que vê no portal.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Diagnosticar problemas de dependência

[Este blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) descreve um projeto para diagnosticar problemas de dependência enviando automaticamente pings regulares para dependências.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>Aplicações Web JavaScript

**Filtragem usando ITelemettryInitializer**

1. Crie uma função de chamada de chamada inicializador de telemetria. A função `ITelemetryItem` de callback assume como parâmetro, que é o evento que está a ser processado. A `false` devolução desta chamada resulta no item de telemetria a filtrar.  

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
        return false;
     }
  
     return true;
   };
   ```

2. Adicione o seu backback de infetante de telemetria:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Adicionar/modificar propriedades: ITelemettryInitializer


Utilize iniciais de telemetria para enriquecer a telemetria com informações adicionais e/ou para sobrepor as propriedades de telemetria definidas pelos módulos de telemetria padrão.

Por exemplo, os Insights de Aplicação para pacote web recolhem telemetria sobre pedidos HTTP. Por padrão, assinala como falhou qualquer pedido com um código de resposta >= 400. Mas se quiser tratar 400 como um sucesso, pode fornecer um inicializador de telemetria que define a propriedade Sucesso.

Se fornecer um infetante de telemetria, é chamado sempre que qualquer um dos métodos track*() são chamados. Isto `Track()` inclui métodos chamados pelos módulos padrão de telemetria. Por convenção, estes módulos não estabelecem qualquer propriedade que já tenha sido definida por um inicializador. Os iniciais de telemetria são chamados antes de chamar processadores de telemetria. Assim, quaisquer enriquecimentos feitos por iniciais são visíveis para processadores.

**Defina o seu inicializador**

*C #*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**ASP.NET aplicações: Carregue o seu inicializador**

Em ApplicationInsights.config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*Em alternativa,* pode instantaneamente o inicializador em código, por exemplo, em Global.aspx.cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[Veja mais sobre esta amostra.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**ASP.NET aplicações Core/ Worker Service: Carregue o seu inicializador**

> [!NOTE]
> Adicionar inicializador `ApplicationInsights.config` utilizando `TelemetryConfiguration.Active` ou utilizar não é válido para ASP.NET aplicações Core ou se estiver a utilizar o Microsoft.ApplicationInsights.WorkerService SDK.

Para aplicações escritas com [base ASP.NET](asp-net-core.md#adding-telemetryinitializers) `TelemetryInitializer` Core ou [WorkerService,](worker-service.md#adding-telemetryinitializers)a adição de uma nova é feita adicionando-a ao recipiente de Injeção de Dependência, como mostrado abaixo. Isto é `Startup.ConfigureServices` feito em método.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="java-telemetry-initializers"></a>Inicializadores de telemetria java

[Documentação Java SDK](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

Em seguida, registe o inicializador personalizado no ficheiro applicationinsights.xml.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
    <Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>Inicializadores de telemetria JavaScript
*JavaScript*

Insira um inicializador de telemetria imediatamente após o código de inicialização que obteve do portal:

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

Para um resumo das propriedades não personalizadas disponíveis no telemetriaItem, consulte Application [Insights Export Data Model](../../azure-monitor/app/export-data-model.md).

Pode adicionar quantos iniciantes quiser, e são chamados na ordem em que são adicionados.

### <a name="opencensus-python-telemetry-processors"></a>Processadores de telemetria OpenCensus Python

Os processadores de telemetria no OpenCensus Python são simplesmente funções de callback chamadas para processar a telemetria antes de serem exportadas. A função de chamada deve aceitar um tipo de dados do [envelope](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) como parâmetro. Para filtrar a telemetria de ser exportada, certifique-se de que a função de retorno de retorno de retorno é `False`. Pode ver o esquema para os tipos de dados do Monitor Azure nos envelopes [aqui](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py).

> [!NOTE]
> Pode modificar `cloud_RoleName` o atributo alterando o `ai.cloud.role` atributo no `tags` campo.

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name.py'
```

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
Pode adicionar quantos processadores quiser, e são chamados na ordem em que são adicionados. Se um processador lançar uma exceção, não impacta os seguintes processadores.

### <a name="example-telemetryinitializers"></a>Exemplo TelemettryInitializers

#### <a name="add-custom-property"></a>Adicionar propriedade personalizada

O inseridor de amostra seguem adiciona uma propriedade personalizada a todas as telemetrias rastreadas.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-cloud-role-name"></a>Adicione o nome do papel da nuvem

O inseridor de amostra seguem define o nome do papel da nuvem em todas as telemetrias rastreadas.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if(string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemettryProcessador e ITelemettryInitializer

Qual é a diferença entre processadores de telemetria e iniciais de telemetria?

* Existem algumas sobreposições no que pode fazer com elas: ambas podem ser usadas para adicionar ou modificar propriedades de telemetria, embora seja aconselhável utilizar iniciantes para esse fim.
* Os TelemettryInitializers funcionam sempre antes dos TelemettryProcessors.
* Os Iniciais de Telemetria podem ser chamados mais de uma vez. Por convenção, não estabelecem qualquer propriedade que já tenha sido definida.
* Os TelemettryProcessors permitem-lhe substituir ou descartar completamente um item de telemetria.
* Todos os TelemettryInitializers registados são garantidos para serem chamados para cada item de telemetria. Para processadores de Telemetria, a SDK garante chamar o primeiro processador de telemetria. Se os restantes processadores são chamados ou não, é decidido pelos processadores de telemetria anteriores.
* Utilize TelemettryInitializers para enriquecer a telemetria com propriedades adicionais, ou sobrepor-se a uma existente. Utilize o TelemettryProcessor para filtrar a telemetria.

## <a name="troubleshooting-applicationinsightsconfig"></a>Resolução de problemas ApplicationInsights.config

* Confirme se o nome de tipo e o nome de montagem totalmente qualificados estão corretos.
* Confirme que o ficheiro applicationinsights.config está no seu diretório de saída e contém quaisquer alterações recentes.

## <a name="reference-docs"></a>Doutorados de referência

* [Visão geral da API](../../azure-monitor/app/api-custom-events-metrics.md)
* [referência ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Código SDK

* [SDK de ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [SDK JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>Passos seguintes
* [Pesquisar eventos e registos](../../azure-monitor/app/diagnostic-search.md)
* [Amostragem](../../azure-monitor/app/sampling.md)
* [Resolução de problemas](../../azure-monitor/app/troubleshoot-faq.md)
