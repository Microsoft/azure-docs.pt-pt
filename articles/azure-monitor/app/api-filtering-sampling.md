---
title: Filtragem e pré-processamento no SDK application Insights Microsoft Docs
description: Escreva processadores de telemetria e iniciais de telemetria para o SDK filtrar ou adicionar propriedades aos dados antes de a telemetria ser enviada para o portal Application Insights.
ms.topic: conceptual
ms.date: 11/23/2016
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: b3ccc3516d5b31f4c119c2d5a2bd11a63dbdc611
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91758035"
---
# <a name="filter-and-preprocess-telemetry-in-the-application-insights-sdk"></a>Filtrar e pré-processar telemetria no SDK Application Insights

Pode escrever e configurar plug-ins para o Application Insights SDK para personalizar como a telemetria pode ser enriquecida e processada antes de ser enviada para o serviço Application Insights.

* [A amostragem](sampling.md) reduz o volume de telemetria sem afetar as suas estatísticas. Mantém os pontos de dados relacionados para que possa navegar entre eles quando diagnosticar um problema. No portal, multiplicam-se as contagens totais para compensar a amostragem.
* A filtragem com processadores de telemetria permite filtrar a telemetria no SDK antes de ser enviada para o servidor. Por exemplo, poderia reduzir o volume de telemetria excluindo pedidos de robôs. A filtragem é uma abordagem mais básica para reduzir o tráfego do que a amostragem. Permite-lhe mais controlo sobre o que é transmitido, mas afeta as suas estatísticas. Por exemplo, pode filtrar todos os pedidos bem sucedidos.
* [Os inicializadores de telemetria adicionam ou modificam propriedades](#add-properties) a qualquer telemetria enviada da sua aplicação, que inclui telemetria a partir dos módulos padrão. Por exemplo, pode adicionar valores calculados ou números de versão para filtrar os dados no portal.
* [A API SDK](./api-custom-events-metrics.md) é usada para enviar eventos e métricas personalizados.

Antes de começar:

* Instale o SDK apropriado para a sua aplicação: [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [Non HTTP/Worker for.NET Core](worker-service.md), ou [JavaScript](javascript.md).

<a name="filtering"></a>

## <a name="filtering"></a>Filtragem

Esta técnica dá-lhe controlo direto sobre o que está incluído ou excluído do fluxo de telemetria. A filtragem pode ser usada para deixar cair itens de telemetria de serem enviados para o Application Insights. Pode utilizar a filtragem em conjunto com a amostragem, ou separadamente.

Para filtrar a telemetria, escreve-se um processador de telemetria e regista-o com `TelemetryConfiguration` . Toda a telemetria passa pelo seu processador. Pode optar por largue-o do fluxo ou dá-lo ao próximo processador da cadeia. A telemetria dos módulos padrão, como o colecionador de pedidos HTTP e o colecionador de dependência, e a telemetria que rastreou a si mesmo estão incluídas. Por exemplo, pode filtrar a telemetria sobre pedidos de robôs ou chamadas de dependência bem sucedidas.

> [!WARNING]
> Filtrar a telemetria enviada do SDK utilizando processadores pode distorcer as estatísticas que vê no portal e dificultar o acompanhamento de itens relacionados.
>
> Em vez disso, considere usar [a amostragem.](./sampling.md)
>
>

### <a name="create-a-telemetry-processor-c"></a>Criar um processador de telemetria (C#)

1. Para criar um filtro, implemente `ITelemetryProcessor` .

    Os processadores de telemetria constroem uma cadeia de processamento. Quando se instantaneamente um processador de telemetria, é-lhe dada uma referência ao próximo processador da cadeia. Quando um ponto de dados de telemetria é passado para o método do processo, ele faz o seu trabalho e, em seguida, chama (ou não chama) o próximo processador de telemetria na cadeia.

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

aplicativos **apps** ASP.NET

Insira este corte em ApplicationInsights.config:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

Pode passar valores de cadeia a partir do ficheiro .config, fornecendo propriedades nomeadas em público na sua classe.

> [!WARNING]
> Preste a conta com o nome do tipo e quaisquer nomes de propriedade no ficheiro .config com a classe e os nomes de propriedade no código. Se o ficheiro .config fizer referências a um tipo ou propriedade inexistente, o SDK pode não enviar qualquer telemetria.
>

Em alternativa, pode inicializar o filtro em código. Numa classe de inicialização adequada, por exemplo, o AppStart in `Global.asax.cs` , insira o seu processador na cadeia:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

Os clientes de telemetria criados após este ponto utilizarão os seus processadores.

ASP.NET **aplicações de serviço Core/Worker**

> [!NOTE]
> Adicionar um processador utilizando `ApplicationInsights.config` ou não é válido para `TelemetryConfiguration.Active` aplicações core ASP.NET ou se estiver a utilizar o Microsoft.ApplicationInsights.WorkerService SDK.

Para aplicações escritas utilizando [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) ou [WorkerService](worker-service.md#adding-telemetry-processors), adicionar um novo processador de telemetria é feito utilizando o `AddApplicationInsightsTelemetryProcessor` método de extensão em , como `IServiceCollection` mostrado. Este método é chamado no `ConfigureServices` método da sua `Startup.cs` classe.

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

Filtrar bots e testes web. Embora o Metrics Explorer lhe dê a opção de filtrar fontes sintéticas, esta opção reduz o tráfego e o tamanho da ingestão filtrando-as na própria SDK.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Autenticação falhada

Filtre os pedidos com uma resposta "401".

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

Se quiser diagnosticar apenas chamadas lentas, filtre as mais rápidas.

> [!NOTE]
> Esta filtragem distorcerá as estatísticas que vê no portal.
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

### <a name="javascript-web-applications"></a>Aplicações web JavaScript

**Filtrar utilizando o ITelemetryInitializer**

1. Crie uma função de retorno inicializador de telemetria. A função de retorno toma `ITelemetryItem` como parâmetro, que é o evento que está sendo processado. O retorno `false` desta chamada resulta em filtragem do artigo de telemetria.

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
         return false;
     }
  
     return true;
   };
   ```

2. Adicione a sua chamada inicializadora de telemetria:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Adicionar/modificar propriedades: ITelemetryInitializer

Utilize inicializadores de telemetria para enriquecer a telemetria com informações adicionais ou para sobrepor as propriedades da telemetria definidas pelos módulos de telemetria padrão.

Por exemplo, o Application Insights para um pacote web recolhe telemetria sobre pedidos HTTP. Por predefinição, sinaliza como falhou qualquer pedido com um código de resposta >=400. Mas se você quiser tratar 400 como um sucesso, você pode fornecer um inicializador de telemetria que define a propriedade de sucesso.

Se fornecer um inicializador de telemetria, é chamado sempre que qualquer um dos métodos track*() são chamados. Isto inclui `Track()` métodos chamados pelos módulos padrão de telemetria. Por convenção, estes módulos não definem qualquer propriedade que já tenha sido definida por um inicializador. Os inicializadores de telemetria são chamados antes de chamar processadores de telemetria. Assim, quaisquer enriquecimentos feitos por inicializadores são visíveis para os processadores.

**Defina o seu inicializador**

*C#*

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

aplicativos **ASP.NET: Carregue o seu inicializador**

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

Em alternativa, pode instantaneamente o inicializador em código, por exemplo, em Global.aspx.cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

Veja mais [nesta amostra.](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

ASP.NET **aplicações de serviço Core/Worker: Carregue o seu inicializador**

> [!NOTE]
> Adicionar um inicializador utilizando `ApplicationInsights.config` ou não é válido para `TelemetryConfiguration.Active` aplicações core ASP.NET ou se estiver a utilizar o Microsoft.ApplicationInsights.WorkerService SDK.

Para aplicações escritas utilizando [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) ou [WorkerService](worker-service.md#adding-telemetryinitializers), a adição de um novo inicializador de telemetria é feita adicionando-a ao recipiente de Injeção de Dependência, como mostrado. Isto é feito no `Startup.ConfigureServices` método.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
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

    appInsights.addTelemetryInitializer(function (envelope) {
        var telemetryItem = envelope.data.baseData;

        // To check the telemetry items type - for example PageView:
        if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
            // this statement removes url from all page view documents
            telemetryItem.url = "URL CENSORED";
        }

        // To set custom properties:
        telemetryItem.properties = telemetryItem.properties || {};
        telemetryItem.properties["globalProperty"] = "boo";
        
        // To set cloud role name / instance
        envelope.tags["ai.cloud.role"] = "your role name";
        envelope.tags["ai.cloud.roleInstance"] = "your role instance";
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

Para obter um resumo das propriedades não-clientes disponíveis no item da telemetria, consulte o [Modelo de Dados de Exportação de Insights de Aplicação](./export-data-model.md).

Pode adicionar quantos inicializadores quiser. São chamados pela ordem que lhes é adicionada.

### <a name="opencensus-python-telemetry-processors"></a>Processadores de telemetria OpenCensus Python

Os processadores de telemetria no OpenCensus Python são simplesmente funções de retorno chamados para processar telemetria antes de serem exportados. A função de retorno deve aceitar um tipo de dados [de envelope](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) como parâmetro. Para filtrar a telemetria de ser exportada, certifique-se de que a função de retorno de chamada retorna `False` . Pode ver o esquema dos tipos de dados do Azure Monitor nos envelopes [do GitHub.](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py)

> [!NOTE]
> Pode modificar `cloud_RoleName` alterando o `ai.cloud.role` atributo no `tags` campo.

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name'
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
Pode adicionar quantos processadores quiser. São chamados pela ordem que lhes é adicionada. Se um processador lançar uma exceção, não impacta os seguintes processadores.

### <a name="example-telemetryinitializers"></a>Exemplo TelemetriaInitializadores

#### <a name="add-a-custom-property"></a>Adicionar uma propriedade personalizada

O seguinte inicializador de amostra adiciona uma propriedade personalizada a cada telemetria rastreada.

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

#### <a name="add-a-cloud-role-name"></a>Adicione um nome de papel em nuvem

O inicializador de amostras a seguir define o nome do papel da nuvem em cada telemetria rastreada.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

#### <a name="add-information-from-httpcontext"></a>Adicionar informações do HttpContext

O seguinte inicializador da amostra lê [`HttpContext`](/aspnet/core/fundamentals/http-context?view=aspnetcore-3.1) dados e anexa-os a um `RequestTelemetry` exemplo. O `IHttpContextAccessor` é fornecido automaticamente através da injeção de dependência do construtor.

```csharp
public class HttpContextRequestTelemetryInitializer : ITelemetryInitializer
{
    private readonly IHttpContextAccessor httpContextAccessor;

    public HttpContextRequestTelemetryInitializer(IHttpContextAccessor httpContextAccessor)
    {
        this.httpContextAccessor =
            httpContextAccessor ??
            throw new ArgumentNullException(nameof(httpContextAccessor));
    }

    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        if (requestTelemetry == null) return;

        var claims = this.httpContextAccessor.HttpContext.User.Claims;
        Claim oidClaim = claims.FirstOrDefault(claim => claim.Type == "oid");
        requestTelemetry.Properties.Add("UserOid", oidClaim?.Value);
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor e ITelemetryInitializer

Qual é a diferença entre processadores de telemetria e inicializadores de telemetria?

* Há algumas sobreposições no que se pode fazer com eles. Ambos podem ser usados para adicionar ou modificar propriedades de telemetria, embora recomendemos que utilize inicializadores para o efeito.
* Os inicializadores de telemetria funcionam sempre antes dos processadores de telemetria.
* Os inicializadores de telemetria podem ser chamados mais de uma vez. Por convenção, não estabelecem nenhuma propriedade que já estivesse definida.
* Os processadores de telemetria permitem substituir ou descartar completamente um item de telemetria.
* Todos os inicializadores de telemetria registados são garantidos para cada item de telemetria. Para processadores de telemetria, a SDK garante chamar o primeiro processador de telemetria. Se os restantes processadores são ou não chamados é decidido pelos processadores de telemetria anteriores.
* Utilize inicializadores de telemetria para enriquecer a telemetria com propriedades adicionais ou sobrepor-se a uma existente. Utilize um processador de telemetria para filtrar a telemetria.

## <a name="troubleshoot-applicationinsightsconfig"></a>Resolução de problemas ApplicationInsights.config

* Confirme se o nome e o nome de montagem totalmente qualificados estão corretos.
* Confirme que o ficheiro applicationinsights.config está no seu diretório de saída e contém quaisquer alterações recentes.

## <a name="reference-docs"></a>Documentos de referência

* [Descrição geral da API](./api-custom-events-metrics.md)
* [referência ASP.NET](/previous-versions/azure/dn817570(v=azure.100))

## <a name="sdk-code"></a>Código do SDK 

* [SDK de ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [SDK JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>Passos seguintes
* [Eventos de pesquisa e registos](./diagnostic-search.md)
* [amostragem](./sampling.md)
* [Resolução de problemas](../faq.md)

