---
title: Amostragem de telemetria em Azure Application Insights / Microsoft Docs
description: Como controlar o volume de telemetria.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: 151bc87bd5674a61b8652adfa70634318c405240
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839610"
---
# <a name="sampling-in-application-insights"></a>Amostragem no Application Insights

A amostragem é uma característica no [Azure Application Insights](./app-insights-overview.md). É a forma recomendada de reduzir o tráfego de telemetria, os custos de dados e os custos de armazenamento, preservando simultaneamente uma análise estatisticamente correta dos dados da aplicação. A amostragem também ajuda a evitar que os Insights de Aplicação a estrangulem a sua telemetria. O filtro de amostragem seleciona itens relacionados, para que possa navegar entre itens quando estiver a fazer investigações de diagnóstico.

Quando as contagens métricas são apresentadas no portal, são renormalizadas para ter em conta a amostragem. Fazê-lo minimiza qualquer efeito nas estatísticas.

## <a name="brief-summary"></a>Breve resumo

* Existem três tipos diferentes de amostragem: amostragem adaptativa, amostragem de taxa fixa e amostragem de ingestão.
* A amostragem adaptativa é ativada por padrão em todas as versões mais recentes dos ASP.NET de Insights de Aplicação e ASP.NET Core Software Development Kits (SDKs). Também é utilizado pelas [Funções Azure.](../../azure-functions/functions-overview.md)
* A amostragem de taxa fixa está disponível nas versões recentes dos SDKs application insights para ASP.NET, ASP.NET Core, Java (tanto o agente como o SDK) e Python.
* A amostragem de ingestão funciona no ponto final do serviço Application Insights. Só se aplica quando não existe outra amostragem. Se o SDK provar a sua telemetria, a amostragem de ingestão é desativada.
* Para aplicações web, se registar eventos personalizados e precisar de garantir que um conjunto de eventos é mantido ou descartado em conjunto, os eventos devem ter o mesmo `OperationId` valor.
* Se escrever consultas de Analytics, deve [ter em conta a amostragem.](../log-query/aggregations.md) Em particular, em vez de simplesmente contar registos, deve usar `summarize sum(itemCount)` .
* Alguns tipos de telemetria, incluindo métricas de desempenho e métricas personalizadas, são sempre mantidos independentemente de a amostragem estar ativada ou não.

O quadro que se segue resume os tipos de amostragem disponíveis para cada SDK e tipo de aplicação:

| Insights de Aplicação SDK | Amostragem adaptativa suportada | Amostragem de taxa fixa suportada | Amostragem de ingestão suportada |
|-|-|-|-|
| ASP.NET | [Sim (on on by default)](#configuring-adaptive-sampling-for-aspnet-applications) | [Sim](#configuring-fixed-rate-sampling-for-aspnet-applications) | Só se nenhuma outra amostragem estiver em vigor |
| Núcleo de ASP.NET | [Sim (on on by default)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Sim](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Só se nenhuma outra amostragem estiver em vigor |
| Funções do Azure | [Sim (on on by default)](#configuring-adaptive-sampling-for-azure-functions) | Não | Só se nenhuma outra amostragem estiver em vigor |
| Java | Não | [Sim](#configuring-fixed-rate-sampling-for-java-applications) | Só se nenhuma outra amostragem estiver em vigor |
| Node.JS | Não | [Sim](./nodejs.md#sampling) | Só se nenhuma outra amostragem estiver em vigor
| Python | Não | [Sim](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Só se nenhuma outra amostragem estiver em vigor |
| Todos os outros | Não | Não | [Sim](#ingestion-sampling) |

> [!NOTE]
> A informação sobre a maior parte desta página aplica-se às versões atuais dos SDKs application insights. Para obter informações sobre versões mais antigas dos SDKs, [consulte a secção abaixo](#older-sdk-versions).

## <a name="types-of-sampling"></a>Tipos de amostragem

Existem três métodos de amostragem diferentes:

* **A amostragem adaptativa** ajusta automaticamente o volume de telemetria enviada a partir do SDK na sua aplicação Core ASP.NET/ASP.NET e a partir de Funções Azure. Esta é a amostra geódia padrão quando utiliza o ASP.NET ou ASP.NET Core SDK. Atualmente, a amostragem adaptativa só está disponível para ASP.NET telemetria do lado do servidor e para funções Azure.

* **A amostragem de taxa fixa** reduz o volume de telemetria enviada tanto do seu ASP.NET como ASP.NET servidor Core ou Java e dos navegadores dos seus utilizadores. Definiu a taxa. O cliente e o servidor sincronizarão a sua amostragem para que, em Search, possa navegar entre visualizações e pedidos de página relacionados.

* **A amostragem de ingestão** acontece no ponto final do serviço Application Insights. Descarta alguma da telemetria que chega da sua aplicação, a uma taxa de amostragem que definiu. Não reduz o tráfego de telemetria enviado da sua app, mas ajuda-o a manter-se dentro da sua quota mensal. A principal vantagem da amostragem de ingestão é que pode definir a taxa de amostragem sem recolocar a sua app. A amostragem de ingestão funciona uniformemente para todos os servidores e clientes, mas não se aplica quando qualquer outro tipo de amostragem está em funcionamento.

> [!IMPORTANT]
> Se estiverem em funcionamento métodos de amostragem adaptativos ou fixos, a amostragem de ingestão é desativada.

## <a name="adaptive-sampling"></a>Amostragem adaptativa

A amostragem adaptativa afeta o volume de telemetria enviada da sua aplicação de servidor web para o ponto final do serviço Application Insights.

> [!TIP]
> A amostragem adaptativa é ativada por padrão quando utiliza o ASP.NET SDK ou o ASP.NET Core SDK, e também é ativada por padrão para Funções Azure.

O volume é ajustado automaticamente para manter dentro de uma taxa máxima de tráfego especificada, e é controlado através da definição `MaxTelemetryItemsPerSecond` . Se a aplicação produzir uma baixa quantidade de telemetria, como quando depurado ou devido a uma utilização baixa, os itens não serão deixados pelo processador de amostragem enquanto o volume estiver abaixo `MaxTelemetryItemsPerSecond` . À medida que o volume de telemetria aumenta, a taxa de amostragem é ajustada de modo a atingir o volume-alvo. O ajuste é recalculado a intervalos regulares e baseia-se numa média móvel da taxa de transmissão de saída.

Para atingir o volume-alvo, alguma da telemetria gerada é descartada. Mas, tal como outros tipos de amostragem, o algoritmo retém itens de telemetria relacionados. Por exemplo, quando estiver a inspecionar a telemetria em Busca, poderá encontrar o pedido relacionado com uma exceção específica.

As contagens métricas, tais como taxa de pedido e taxa de exceção são ajustadas para compensar a taxa de amostragem, de modo que eles mostram valores aproximadamente corretos no Metric Explorer.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Configuração da amostragem adaptativa para aplicações ASP.NET

> [!NOTE]
> Esta secção aplica-se a aplicações ASP.NET, não a aplicações core ASP.NET. [Saiba como configurar amostras adaptativas para aplicações ASP.NET Core mais tarde neste documento.](#configuring-adaptive-sampling-for-aspnet-core-applications)

In [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) , pode ajustar vários parâmetros no `AdaptiveSamplingTelemetryProcessor` nó. Os números apresentados são os valores predefinidos:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    A taxa-alvo de [operações lógicas](./correlation.md#data-model-for-telemetry-correlation) que o algoritmo adaptativo pretende recolher **em cada anfitrião do servidor**. Se a sua aplicação web for executado em muitos anfitriões, reduza este valor de modo a permanecer dentro da sua taxa de tráfego alvo no portal Application Insights.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    O intervalo em que a taxa atual de telemetria é reavaliada. A avaliação é realizada como uma média móvel. É melhor encurtar este intervalo se a sua telemetria for suscetível de explosões repentinas.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Quando a amostragem do valor da percentagem muda, quando é que nos é permitido baixar novamente a percentagem de amostragem para capturar menos dados?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Quando a amostragem do valor da percentagem muda, em que momento nos é permitido aumentar novamente a percentagem de amostragem para capturar mais dados?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Como a percentagem de amostragem varia, qual é o valor mínimo que podemos definir?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Como a percentagem de amostragem varia, qual é o valor máximo que podemos definir?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    No cálculo da média móvel, isto especifica o peso que deve ser atribuído ao valor mais recente. Utilize um valor igual ou inferior a 1. Valores menores tornam o algoritmo menos reativo a mudanças repentinas.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    A quantidade de telemetria para amostrar quando a aplicação acaba de começar. Não reduza este valor enquanto estiver a depurar.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Uma lista de tipos delimitados semi-cólon que não pretende ser sujeito a amostragem. Os tipos reconhecidos são: `Dependency` , , , , , `Event` `Exception` `PageView` `Request` `Trace` . Toda a telemetria dos tipos especificados é transmitida; os tipos que não são especificados serão amostrados.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Uma lista de tipos delimitados semi-cólon que pretende submeter a amostragem. Os tipos reconhecidos são: `Dependency` , , , , , `Event` `Exception` `PageView` `Request` `Trace` . Os tipos especificados serão amostrados; toda a telemetria dos outros tipos será sempre transmitida.

**Para desligar a** amostragem adaptativa, retire o `AdaptiveSamplingTelemetryProcessor` nó(s) de `ApplicationInsights.config` .

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativa: Configurar amostragem adaptativa em código

Em vez de definir o parâmetro de amostragem no `.config` ficheiro, pode definir programaticamente estes valores.

1. Retire todos os `AdaptiveSamplingTelemetryProcessor` nós do `.config` ficheiro.
2. Utilize o seguinte corte para configurar a amostragem adaptativa:

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    (Saiba[mais sobre processadores de telemetria.)](./api-filtering-sampling.md#filtering)

Também pode ajustar a taxa de amostragem de cada tipo de telemetria individualmente, ou pode mesmo excluir certos tipos de amostragem:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Configurar amostras adaptativas para aplicações ASP.NET Core

Não há `ApplicationInsights.config` para aplicações core ASP.NET, por isso toda a configuração é feita via código.
A amostragem adaptativa é ativada por padrão para todas as aplicações core ASP.NET. Pode desativar ou personalizar o comportamento de amostragem.

#### <a name="turning-off-adaptive-sampling"></a>Desligar a amostragem adaptativa

A função de amostragem predefinido pode ser desativada ao mesmo tempo que adiciona o serviço Application Insights, no `ConfigureServices` método, utilizando `ApplicationInsightsServiceOptions` dentro do `Startup.cs` ficheiro:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

O código acima irá desativar a amostragem adaptativa. Siga os passos abaixo para adicionar amostragem com mais opções de personalização.

#### <a name="configure-sampling-settings"></a>Configurar as definições de amostragem

Utilize métodos de extensão de `TelemetryProcessorChainBuilder` como mostrado abaixo para personalizar o comportamento da amostragem.

> [!IMPORTANT]
> Se utilizar este método para configurar a amostragem, certifique-se de definir a `aiOptions.EnableAdaptiveSampling` propriedade para quando ligar `false` `AddApplicationInsightsTelemetry()` .

```csharp
using Microsoft.ApplicationInsights.Extensibility

public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Configurar amostras adaptativas para funções Azure

Siga as instruções [desta página](../../azure-functions/functions-monitoring.md#configure-sampling) para configurar amostras adaptativas para aplicações em execução em Funções Azure.

## <a name="fixed-rate-sampling"></a>Amostragem de taxa fixa

A amostragem de taxa fixa reduz o tráfego enviado do seu servidor web e navegadores web. Ao contrário da amostragem adaptativa, reduz a telemetria a uma taxa fixa decidida por si. A amostragem de taxa fixa está disponível para aplicações ASP.NET, ASP.NET Core, Java e Python.

À semelhança de outras técnicas de amostragem, esta também retém itens relacionados. Também sincroniza a amostragem do cliente e do servidor para que os itens relacionados sejam retidos - por exemplo, quando olha para uma vista de página no Search, pode encontrar os seus pedidos de servidor relacionados. 

No Metrics Explorer, as taxas como pedidos e contagens de exceções são multiplicadas por um fator para compensar a taxa de amostragem, de modo a que estejam aproximadamente corretas.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Configuração da amostragem de taxa fixa para aplicações ASP.NET

1. **Desativar a amostragem adaptativa**: Em [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) , remover ou comentar o `AdaptiveSamplingTelemetryProcessor` nó.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Ativar o módulo de amostragem de taxa fixa.** Adicione este corte [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) a:
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Em alternativa, em vez de definir o parâmetro de amostragem no `ApplicationInsights.config` ficheiro, pode definir programaticamente estes valores:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    (Saiba[mais sobre processadores de telemetria.)](./api-filtering-sampling.md#filtering)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Configuração da amostragem de taxa fixa para aplicações ASP.NET Core

1. **Desativar a amostragem adaptativa**: Podem ser feitas alterações no `ConfigureServices` método, `ApplicationInsightsServiceOptions` utilizando:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **Ativar o módulo de amostragem de taxa fixa.** As alterações podem ser efetuadas no `Configure` método tal como indicado no corte abaixo:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = configuration.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Configuração da amostragem de taxa fixa para aplicações java

Por predefinição, não está ativada qualquer amostragem no agente Java e na SDK. Atualmente suporta apenas a amostragem de taxa fixa. A amostragem adaptativa não é suportada em Java.

#### <a name="configuring-java-agent"></a>Agente Java configurado

1. Baixar [aplicaçõesinsights-agent-3.0.0-PREVIEW.5.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar)

1. Para ativar a amostragem adicione o seguinte ao seu `ApplicationInsights.json` ficheiro:

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10 //this is just an example that shows you how to enable only only 10% of transaction 
        }
      }
    }
  }
}
```

#### <a name="configuring-java-sdk"></a>Configurar Java SDK

1. Faça o download e configuure a sua aplicação web com os mais recentes [Application Insights Java SDK](./java-get-started.md).

2. **Ativar o módulo de amostragem de taxa fixa** adicionando o seguinte corte para `ApplicationInsights.xml` arquivar:

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. Pode incluir ou excluir tipos específicos de telemetria da amostragem utilizando as seguintes etiquetas dentro `Processor` da `FixedRateSamplingTelemetryProcessor` etiqueta:
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

Os tipos de telemetria que podem ser incluídos ou excluídos da amostragem são: `Dependency` , , , , , e `Event` `Exception` `PageView` `Request` `Trace` .

> [!NOTE]
> Para a percentagem de amostragem, escolha uma percentagem próxima de 100/N onde N é um inteiro.  Atualmente, a amostragem não suporta outros valores.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Configuração da amostragem de taxa fixa para aplicações OpenCensus Python

Instrumento a sua aplicação com os mais recentes [exportadores do OpenCensus Azure Monitor](./opencensus-python.md).

> [!NOTE]
> A amostragem a taxa fixa não está disponível para o exportador de métricas. Isto significa que as métricas personalizadas são os únicos tipos de telemetria onde a amostragem NÃO pode ser configurada. O exportador de métricas enviará toda a telemetria que rastreia.

#### <a name="fixed-rate-sampling-for-tracing"></a>Amostragem de taxa fixa para rastreio ####
Pode especificar um `sampler` como parte da configuração do `Tracer`. Se não for fornecido um amostrador explícito, o `ProbabilitySampler` será utilizado por predefinição. Usaria `ProbabilitySampler` uma taxa de 1/10000 por defeito, o que significa que um em cada 10000 pedidos será enviado para a Application Insights. Se quiser especificar uma taxa de amostragem, veja abaixo.

Para especificar a taxa de amostragem, certifique-se de que `Tracer` especifica um amostrador com uma taxa de amostragem entre 0,0 e 1.0 inclusive. Uma taxa de amostragem de 1,0 representa 100%, o que significa que todos os seus pedidos serão enviados como telemetria para a Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Amostragem de taxa fixa para troncos ####
Pode configurar a amostragem de taxa fixa através `AzureLogHandler` da modificação do `logging_sampling_rate` argumento opcional. Se não for apresentado qualquer argumento, será utilizada uma taxa de amostragem de 1.0. Uma taxa de amostragem de 1,0 representa 100%, o que significa que todos os seus pedidos serão enviados como telemetria para a Application Insights.

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Configurar amostragem de taxa fixa para páginas web com JavaScript

As páginas web baseadas em JavaScript podem ser configuradas para usar Insights de Aplicação. A telemetria é enviada a partir da aplicação do cliente que está a decorrer dentro do navegador do utilizador, e as páginas podem ser hospedadas a partir de qualquer servidor.

Quando [configurar as suas páginas web baseadas em JavaScript para Insights de Aplicações,](javascript.md)modifique o snippet JavaScript que obtém do portal Application Insights.

> [!TIP]
> Em ASP.NET aplicações com JavaScript incluído, o snippet normalmente `_Layout.cshtml` entra.

Insira uma linha como `samplingPercentage: 10,` antes da tecla de instrumentação:

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

Para a percentagem de amostragem, escolha uma percentagem próxima de 100/N onde N é um inteiro. Atualmente, a amostragem não suporta outros valores.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Coordenação da amostragem do lado do servidor e do lado do cliente

O JavaScript SDK do lado do cliente participa em amostragem de taxa fixa em conjunto com o SDK do lado do servidor. As páginas instrumentadas apenas enviarão telemetria do lado do cliente do mesmo utilizador para o qual o SDK do lado do servidor tomou a sua decisão de incluir na amostragem. Esta lógica é projetada para manter a integridade das sessões de utilizadores em todas as aplicações do lado do cliente e do servidor. Como resultado, a partir de qualquer item de telemetria em Informações de Aplicação pode encontrar todos os outros itens de telemetria para este utilizador ou sessão e em Search, pode navegar entre visualizações e pedidos de página relacionados.

Se o seu cliente e a telemetria do lado do servidor não mostrarem amostras coordenadas:

* Verifique se ativou a amostragem tanto no servidor como no cliente.
* Verifique se define a mesma percentagem de amostragem tanto no cliente como no servidor.
* Certifique-se de que a versão SDK é 2.0 ou superior.

## <a name="ingestion-sampling"></a>Amostragem de ingestão

A amostragem de ingestão funciona no ponto em que a telemetria do seu servidor web, navegadores e dispositivos atinge o ponto final do serviço Application Insights. Apesar de não reduzir o tráfego de telemetria enviado da sua app, reduz a quantidade processada e retida (e cobrada) pela Application Insights.

Utilize este tipo de amostragem se a sua aplicação ultrapassar frequentemente a sua quota mensal e não tiver a opção de utilizar nenhum dos tipos de amostragem baseados em SDK. 

Definir a taxa de amostragem na página de utilização e custos estimados:

![A partir da lâmina de visão geral da aplicação, clique em Definições, Quota, Amostras e, em seguida, selecione uma taxa de amostragem e clique em Update.](./media/sampling/data-sampling.png)

Como outros tipos de amostragem, o algoritmo retém itens de telemetria relacionados. Por exemplo, quando estiver a inspecionar a telemetria em Busca, poderá encontrar o pedido relacionado com uma exceção específica. As contagens métricas, tais como taxa de pedido e taxa de exceção, são corretamente mantidas.

Os pontos de dados que são descartados por amostragem não estão disponíveis em nenhuma funcionalidade de Insights de Aplicação, como [a Exportação Contínua.](./export-telemetry.md)

A amostragem de ingestão não funciona enquanto a amostragem adaptativa ou de taxa fixa está em funcionamento. A amostragem adaptativa é ativada por padrão quando o ASP.NET SDK ou o ASP.NET Core SDK está a ser utilizado, ou quando os Insights de Aplicação são ativados no [Serviço de Aplicações Azure ](azure-web-apps.md) ou utilizando o Status Monitor. Quando a telemetria é recebida pelo ponto final do serviço Application Insights, examina a telemetria e se a taxa de amostragem for inferior a 100% (o que indica que a telemetria está a ser amostrada), então a taxa de amostragem de ingestão que definiu é ignorada.

> [!WARNING]
> O valor mostrado no azulejo do portal indica o valor que definiu para a amostragem de ingestão. Não representa a taxa de amostragem real se estiver em funcionamento qualquer tipo de amostragem SDK (amostragem adaptativa ou de taxa fixa).

## <a name="when-to-use-sampling"></a>Quando utilizar a amostragem

Em geral, para a maioria das aplicações de tamanho pequeno e médio não precisa de amostragem. As informações de diagnóstico mais úteis e as estatísticas mais precisas são obtidas recolhendo dados sobre todas as suas atividades de utilizador. 

As principais vantagens da amostragem são:

* O serviço Application Insights deixa cair pontos de dados ("aceleradores") quando a sua aplicação envia uma taxa muito elevada de telemetria num curto espaço de tempo. A amostragem reduz a probabilidade de a sua aplicação ver estrangulamento.
* Para manter dentro da [quota](pricing.md) de pontos de dados para o seu nível de preços. 
* Reduzir o tráfego de rede da recolha de telemetria. 

### <a name="which-type-of-sampling-should-i-use"></a>Que tipo de amostragem devo usar?

**Utilize amostras de ingerição se:**

* Usa-se frequentemente a sua quota mensal de telemetria.
* Está a receber demasiada telemetria dos navegadores web dos seus utilizadores.
* Está a usar uma versão do SDK que não suporta amostras - por exemplo, ASP.NET versões antes de 2.

**Utilizar amostragem de taxa fixa se:**

* Pretende uma amostra sincronizada entre o cliente e o servidor para que, quando estiver a investigar eventos em [Search,](./diagnostic-search.md)possa navegar entre eventos relacionados no cliente e servidor, tais como visualizações de páginas e pedidos HTTP.
* Está confiante na percentagem de amostragem adequada para a sua aplicação. Deve ser alto o suficiente para obter métricas precisas, mas abaixo da taxa que excede a sua quota de preços e os limites de estrangulamento.

**Utilização de amostragem adaptativa:**

Se não se aplicarem as condições de utilização das outras formas de amostragem, recomendamos uma amostragem adaptativa. Esta definição é ativada por padrão no ASP.NET/ASP.NET Core SDK. Não reduzirá o tráfego até que uma determinada taxa mínima seja atingida, pelo que os locais de baixa utilização provavelmente não serão amostrados.

## <a name="knowing-whether-sampling-is-in-operation"></a>Saber se a amostragem está em funcionamento

Para descobrir a taxa de amostragem real, independentemente do local onde tenha sido aplicada, utilize uma [consulta analítica](../log-query/log-query-overview.md) como esta:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Se vir que `RetainedPercentage` para qualquer tipo é inferior a 100, então este tipo de telemetria está a ser amostrado.

> [!IMPORTANT]
> Application Insights não experimenta sessão, métricas (incluindo métricas personalizadas) ou tipos de telemetria de contador de desempenho em qualquer uma das técnicas de amostragem. Estes tipos são sempre excluídos da amostragem, uma vez que uma redução da precisão pode ser altamente indesejável para estes tipos de telemetria.

## <a name="how-sampling-works"></a>Como funciona a amostragem

O algoritmo de amostragem decide quais itens de telemetria a largar, e quais os que devem guardar. Isto é verdade se a amostragem é feita pelo SDK ou no serviço Application Insights. A decisão de amostragem baseia-se em várias regras que visam preservar intactos todos os pontos de dados interrelacionados, mantendo uma experiência de diagnóstico em Application Insights que seja exequível e fiável mesmo com um conjunto de dados reduzido. Por exemplo, se a sua aplicação tiver um pedido falhado incluído numa amostra, os itens de telemetria adicionais (como exceção e vestígios registados para este pedido) serão mantidos. A amostragem mantém ou deixa-as juntas todas. Como resultado, quando olha para os detalhes do pedido no Application Insights, pode sempre ver o pedido juntamente com os seus itens de telemetria associados.

A decisão de amostragem baseia-se na operação ID do pedido, o que significa que todos os artigos de telemetria pertencentes a uma determinada operação são preservados ou abandonados. Para os itens de telemetria que não possuam um conjunto de ID de operação (como artigos de telemetria relatados a partir de fios assíncronos sem contexto HTTP) a amostragem captura simplesmente uma percentagem de itens de telemetria de cada tipo.

Ao apresentar a telemetria de volta a si, o serviço Application Insights ajusta as métricas pela mesma percentagem de amostragem que foi utilizada no momento da recolha, para compensar os pontos de dados em falta. Assim, quando se olha para a telemetria no Application Insights, os utilizadores estão a ver aproximações estatisticamente corretas que estão muito próximas dos números reais.

A precisão da aproximação depende em grande parte da percentagem de amostragem configurada. Além disso, a precisão aumenta para aplicações que lidam com um grande volume de pedidos geralmente similares de muitos utilizadores. Por outro lado, para aplicações que não funcionam com uma carga significativa, a amostragem não é necessária, uma vez que estas aplicações podem normalmente enviar toda a sua telemetria enquanto permanecem dentro da quota, sem causar perda de dados por estrangulamento. 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

*Qual é o comportamento de amostragem padrão nos ASP.NET e ASP.NET SDKs core?*

* Se estiver a utilizar uma das versões mais recentes do SDK acima, a Amostragem Adaptativa é ativada por padrão com cinco itens de telemetria por segundo.
  Existem dois `AdaptiveSamplingTelemetryProcessor` nós adicionados por padrão, e um inclui o `Event` tipo de amostragem, enquanto o outro exclui o `Event` tipo de amostragem. Esta configuração significa que o SDK tentará limitar os artigos de telemetria a cinco itens de telemetria de `Event` tipos e cinco itens de telemetria de todos os outros tipos combinados, garantindo assim que `Events` são amostrados separadamente de outros tipos de telemetria. Os eventos são normalmente utilizados para telemetria de negócios, e provavelmente não devem ser afetados por volumes de telemetria de diagnóstico.
  
  O seguinte mostra o ficheiro predefinido `ApplicationInsights.config` gerado. Em ASP.NET Core, o mesmo comportamento padrão é ativado em código. Utilize os [exemplos na secção anterior desta página](#configuring-adaptive-sampling-for-aspnet-core-applications) para alterar este comportamento padrão.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*A telemetria pode ser amostrada mais de uma vez?*

* N.º Amostragem Os processos de amostragem ignoram os itens de consideração de amostragem se o item já estiver amostrado. O mesmo acontece com a amostragem de ingestão, que não aplicará amostras aos itens já recolhidos no próprio SDK.

*Por que provar um simples "recolher X% de cada tipo de telemetria"?*

* Embora esta abordagem de amostragem proporcione um elevado nível de precisão nas aproximações métricas, quebraria a capacidade de correlacionar dados de diagnóstico por utilizador, sessão e pedido, o que é fundamental para os diagnósticos. Por isso, a amostragem funciona melhor com políticas como "recolher todos os itens de telemetria para X por cento dos utilizadores de aplicações", ou "recolher toda a telemetria para X por cento dos pedidos de aplicações". Para os itens de telemetria não associados aos pedidos (como o processamento assíncrodo de fundo), o recuo é "recolher X% de todos os itens para cada tipo de telemetria". 

*A percentagem de amostragem pode mudar ao longo do tempo?*

* Sim, a amostragem adaptativa altera gradualmente a percentagem de amostragem, com base no volume atualmente observado da telemetria.

*Se eu usar amostragem de taxa fixa, como sei qual a percentagem de amostragem que funcionará melhor para a minha app?*

* Uma maneira é começar com a amostragem adaptativa, descobrir em que taxa se fixa (ver a questão acima) e, em seguida, mudar para a amostragem de taxa fixa usando essa taxa. 
  
    Caso contrário, tem que adivinhar. Analise a sua utilização atual de telemetria em Application Insights, observe qualquer estrangulamento que esteja a ocorrer e calcule o volume da telemetria recolhida. Estas três entradas, juntamente com o seu nível de preços selecionado, sugerem o quanto pode querer reduzir o volume da telemetria recolhida. No entanto, um aumento do número de utilizadores ou de alguma outra mudança no volume de telemetria pode invalidar a sua estimativa.

*O que acontece se eu configurar a percentagem de amostragem para ser muito baixa?*

* Percentagens de amostragem excessivamente baixas causam uma amostragem demasiado agressiva e reduzem a precisão das aproximações quando a Application Insights tenta compensar a visualização dos dados para a redução do volume de dados. Também a sua experiência de diagnóstico pode ser negativamente impactada, uma vez que alguns dos pedidos de falha ou lentidão raramente podem ser recolhidos.

*O que acontece se eu configurar a percentagem de amostragem para ser muito alta?*

* Configurar uma percentagem de amostragem demasiado elevada (não suficientemente agressiva) resulta numa redução insuficiente do volume da telemetria recolhida. Pode ainda experimentar a perda de dados de telemetria relacionada com o estrangulamento, e o custo da utilização de Insights de Aplicação pode ser superior ao que planeou devido a taxas de excesso de velocidade.

*Em que plataformas posso usar amostragem?*

* A amostragem de ingestão pode ocorrer automaticamente para qualquer telemetria acima de um determinado volume, se o SDK não estiver a efetuar a amostragem. Esta configuração funcionaria, por exemplo, se estiver a utilizar uma versão mais antiga do ASP.NET SDK ou Java SDK.
* Se estiver a utilizar os ASP.NET atuais ou ASP.NET Core SDKs (hospedados no Azure ou no seu próprio servidor), obtém uma amostragem adaptativa por padrão, mas pode mudar para taxa fixa como descrito acima. Com a amostragem de taxa fixa, o browser SDK sincroniza-se automaticamente com eventos relacionados com a amostra. 
* Se estiver a utilizar o agente Java atual, pode configurar `ApplicationInsights.json` (para Java SDK, `ApplicationInsights.xml` configurar) para ligar a amostragem de taxa fixa. A amostragem é desligada por defeito. Com a amostragem de taxa fixa, o browser SDK e o servidor sincronizam automaticamente para eventos relacionados com a amostra.

*Há certos eventos raros que sempre quero ver. Como posso passá-los pelo módulo de amostragem?*

* A melhor maneira de o conseguir é escrever um [TelemetriaInitializador](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)personalizado, que define o `SamplingPercentage` 100 no item de telemetria que pretende manter retido, como mostrado abaixo. Dado que os inicializadores são garantidos a serem executados antes dos processadores de telemetria (incluindo a amostragem), isto garante que todas as técnicas de amostragem ignorarão este item de quaisquer considerações de amostragem. Os iniciais de telemetria personalizados estão disponíveis no ASP.NET SDK, no ASP.NET Core SDK, no JavaScript SDK e no Java SDK. Por exemplo, pode configurar um inicializador de telemetria utilizando o ASP.NET SDK:

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>Versões SDK mais antigas

A amostragem adaptativa está disponível para o SDK application Insights para ASP.NET v2.0.0-beta3 e, mais tarde, Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1 e posteriormente, e é ativada por padrão.

A amostragem de taxa fixa é uma característica do SDK em ASP.NET versões a partir de 2.0.0 e java SDK versão 2.0.1 e em diante.

Antes de v2.5.0-beta2 do ASP.NET SDK, e v2.2.0-beta3 de ASP.NET Core SDK, a decisão de amostragem baseou-se no haxixe do ID do utilizador para aplicações que definem "utilizador" (isto é, aplicações web mais típicas). Para os tipos de aplicações que não definiram os utilizadores (como os serviços web) a decisão de amostragem baseou-se na operação ID do pedido. Versões recentes do ASP.NET e ASP.NET Os SDKs core utilizam o ID de operação para a decisão de amostragem.

## <a name="next-steps"></a>Passos seguintes

* [A filtragem](./api-filtering-sampling.md) pode fornecer um controlo mais rigoroso do que o seu SDK envia.
* Leia o artigo da Rede de [Desenvolvedores Otimize a Telemetria com Insights de Aplicação](/archive/msdn-magazine/2017/may/devops-optimize-telemetry-with-application-insights).

