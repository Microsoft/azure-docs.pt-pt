---
title: Amostragem de telemetria no Aplicativo Azure insights | Microsoft Docs
description: Como manter o volume de telemetria sob controle.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: 9fda3bb0188a2030572ee686ff5a942aca61ea36
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989982"
---
# <a name="sampling-in-application-insights"></a>Amostragem no Application Insights

A amostragem é um recurso do [aplicativo Azure insights](../../azure-monitor/app/app-insights-overview.md). É a maneira recomendada para reduzir o tráfego de telemetria, os custos de dados e os custos de armazenamento, preservando uma análise estatisticamente correta dos dados do aplicativo. A amostragem também ajuda a evitar Application Insights limitação da telemetria. O filtro de amostragem seleciona itens relacionados, para que você possa navegar entre os itens quando estiver fazendo investigações de diagnóstico.

Quando as contagens de métricas são apresentadas no portal, elas são renormalizadas para levar em consideração a amostragem. Isso minimiza qualquer efeito nas estatísticas.

## <a name="brief-summary"></a>Breve resumo

* Há três tipos diferentes de amostragem: amostragem adaptável, amostragem de taxa fixa e amostragem de ingestão.
* A amostragem adaptável é habilitada por padrão em todas as versões mais recentes do Application Insights ASP.NET e ASP.NET Core SDKs (Software Development Kits). Ele também é usado pelo [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).
* A amostragem de taxa fixa está disponível em versões recentes dos SDKs de Application Insights para ASP.NET, ASP.NET Core, Java e Python.
* A amostragem de ingestão funciona no ponto de extremidade do serviço de Application Insights. Ele só se aplica quando nenhuma outra amostragem está em vigor. Se o SDK amostras de sua telemetria, a amostragem de ingestão será desabilitada.
* Para aplicativos Web, se você registrar eventos personalizados e precisar garantir que um conjunto de eventos seja mantido ou descartado em conjunto, os eventos deverão ter o mesmo valor `OperationId`.
* Se você escrever consultas do Analytics, deverá [considerar a amostragem](../../azure-monitor/log-query/aggregations.md). Em particular, em vez de simplesmente contar registros, você deve usar `summarize sum(itemCount)`.
* Alguns tipos de telemetria, incluindo métricas de desempenho e métricas personalizadas, são sempre mantidos, independentemente de a amostragem estar habilitada ou não.

A tabela a seguir resume os tipos de amostragem disponíveis para cada SDK e tipo de aplicativo:

| SDK do Application Insights | Amostragem adaptável com suporte | Amostragem de taxa fixa com suporte | Amostragem de ingestão com suporte |
|-|-|-|-|
| ASP.NET | [Sim (ativado por padrão)](#configuring-adaptive-sampling-for-aspnet-applications) | [Sim](#configuring-fixed-rate-sampling-for-aspnet-applications) | Somente se nenhuma outra amostragem estiver em vigor |
| ASP.NET Core | [Sim (ativado por padrão)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Sim](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Somente se nenhuma outra amostragem estiver em vigor |
| Funções do Azure | [Sim (ativado por padrão)](#configuring-adaptive-sampling-for-azure-functions) | Não | Somente se nenhuma outra amostragem estiver em vigor |
| Java | Não | [Sim](#configuring-fixed-rate-sampling-for-java-applications) | Somente se nenhuma outra amostragem estiver em vigor |
| Python | Não | [Sim](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Somente se nenhuma outra amostragem estiver em vigor |
| Todos os outros | Não | Não | [Sim](#ingestion-sampling) |

> [!NOTE]
> As informações sobre a maior parte desta página se aplicam às versões atuais dos SDKs do Application Insights. Para obter informações sobre versões mais antigas dos SDKs, [consulte a seção abaixo](#older-sdk-versions).

## <a name="types-of-sampling"></a>Tipos de amostragem

Há três métodos diferentes de amostragem:

* A **amostragem adaptável** ajusta automaticamente o volume de telemetria enviado do SDK em seu aplicativo ASP.net/ASP.NET Core e de Azure functions. Essa é a amostragem padrão quando você usa o SDK do ASP.NET ou do ASP.NET Core. A amostragem adaptável está disponível no momento apenas para telemetria ASP.NET do lado do servidor e para Azure Functions.

* A **amostragem de taxa fixa** reduz o volume de telemetria enviado do seu ASP.NET ou ASP.NET Core ou do servidor Java e dos navegadores dos seus usuários. Você define a taxa. O cliente e o servidor sincronizarão sua amostragem para que, na pesquisa, você possa navegar entre exibições e solicitações de página relacionadas.

* A **amostragem de ingestão** ocorre no ponto de extremidade do serviço de Application insights. Ele descarta parte da telemetria que chega de seu aplicativo, em uma taxa de amostragem que você define. Ele não reduz o tráfego de telemetria enviado do seu aplicativo, mas ajuda você a manter em sua cota mensal. A principal vantagem da amostragem de ingestão é que você pode definir a taxa de amostragem sem reimplantar seu aplicativo. A amostragem de ingestão funciona uniformemente para todos os servidores e clientes, mas não se aplica quando outros tipos de amostragem estão em operação.

> [!IMPORTANT]
> Se os métodos de amostragem de taxa adaptável ou fixa estiverem em operação, a amostragem de ingestão será desabilitada.

## <a name="adaptive-sampling"></a>Amostragem adaptável

A amostragem adaptável afeta o volume de telemetria enviado do aplicativo do servidor Web para o ponto de extremidade do serviço de Application Insights.

> [!TIP]
> A amostragem adaptável é habilitada por padrão quando você usa o SDK do ASP.NET ou o SDK do ASP.NET Core e também é habilitada por padrão para Azure Functions.

O volume é ajustado automaticamente para manter dentro de uma taxa máxima de tráfego especificada e é controlado por meio da configuração `MaxTelemetryItemsPerSecond`. Se o aplicativo produzir uma pequena quantidade de telemetria, como durante a depuração ou devido ao baixo uso, os itens não serão descartados pelo processador de amostragem, contanto que o volume esteja abaixo de `MaxTelemetryItemsPerSecond`. À medida que aumenta o volume de telemetria, a taxa de amostragem é ajustada para alcançar o volume de destino. O ajuste é recalculado em intervalos regulares e é baseado em uma média móvel da taxa de transmissão de saída.

Para obter o volume de destino, parte da telemetria gerada é descartada. Mas, como outros tipos de amostragem, o algoritmo retém itens de telemetria relacionados. Por exemplo, quando você estiver inspecionando a telemetria na pesquisa, poderá encontrar a solicitação relacionada a uma exceção específica.

Contagens de métricas, como taxa de solicitação e taxa de exceção, são ajustadas para compensar a taxa de amostragem, para que elas mostrem aproximadamente os valores corretos no Gerenciador de métricas.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Configurando a amostragem adaptável para aplicativos ASP.NET

> [!NOTE]
> Esta seção se aplica a aplicativos ASP.NET, não a ASP.NET Core aplicativos. [Saiba mais sobre como configurar a amostragem adaptável para aplicativos ASP.NET Core mais adiante neste documento.](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)

No [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md), você pode ajustar vários parâmetros no nó `AdaptiveSamplingTelemetryProcessor`. As figuras mostradas são os valores padrão:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    A taxa de destino para a qual o algoritmo adaptável se destina **em cada host do servidor**. Se seu aplicativo Web for executado em vários hosts, reduza esse valor para permanecer dentro de sua taxa de destino de tráfego no portal de Application Insights.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    O intervalo no qual a taxa atual de telemetria é reavaliada. A avaliação é executada como uma média móvel. Talvez você queira encurtar esse intervalo se sua telemetria for responsável por picos repentinos.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Quando o valor da porcentagem de amostragem for alterado, o quanto logo depois será permitido reduzir o percentual de amostragem novamente para capturar menos dados?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Quando o valor da porcentagem de amostragem for alterado, o quanto logo depois será permitido aumentar o percentual de amostragem novamente para capturar mais dados?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    À medida que a porcentagem de amostragem varia, qual é o valor mínimo que temos permissão para definir?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    À medida que a porcentagem de amostragem varia, qual é o valor máximo que temos permissão para definir?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    No cálculo da média móvel, isso especifica o peso que deve ser atribuído ao valor mais recente. Use um valor igual ou menor que 1. Valores menores tornam o algoritmo menos reativo para alterações repentinas.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    A quantidade de telemetria a ser amostrada quando o aplicativo acabou de ser iniciado. Não reduza esse valor enquanto estiver depurando.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Uma lista delimitada por ponto e vírgula de tipos que você não deseja que estejam sujeitos à amostragem. Os tipos reconhecidos são: `Dependency`, `Event`, `Exception`, `PageView`, `Request`, `Trace`. Toda a telemetria dos tipos especificados é transmitida; os tipos que não forem especificados serão amostrados.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Uma lista delimitada por ponto e vírgula dos tipos que você deseja que sejam sujeitos à amostragem. Os tipos reconhecidos são: `Dependency`, `Event`, `Exception`, `PageView`, `Request`, `Trace`. Os tipos especificados serão amostrados; toda a telemetria dos outros tipos sempre será transmitida.

**Para desativar** a amostragem adaptável, remova o `AdaptiveSamplingTelemetryProcessor` nó (s) da `ApplicationInsights.config`.

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativa: configurar a amostragem adaptável no código

Em vez de definir o parâmetro de amostragem no arquivo de `.config`, você pode definir esses valores programaticamente.

1. Remova todos os nós de `AdaptiveSamplingTelemetryProcessor` do arquivo de `.config`.
2. Use o trecho a seguir para configurar a amostragem adaptável:

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

    ([Saiba mais sobre processadores de telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Você também pode ajustar a taxa de amostragem para cada tipo de telemetria individualmente ou pode até mesmo excluir determinados tipos de amostra:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Configurando a amostragem adaptável para aplicativos ASP.NET Core

Não há `ApplicationInsights.config` para ASP.NET Core aplicativos, portanto, toda a configuração é feita por meio de código.
A amostragem adaptável é habilitada por padrão para todos os aplicativos de ASP.NET Core. Você pode desabilitar ou personalizar o comportamento de amostragem.

#### <a name="turning-off-adaptive-sampling"></a>Desativando a amostragem adaptável

O recurso de amostragem padrão pode ser desabilitado ao adicionar Application Insights serviço, no método `ConfigureServices`, usando `ApplicationInsightsServiceOptions` dentro do arquivo `Startup.cs`:

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

O código acima desabilitará a amostragem adaptável. Siga as etapas abaixo para adicionar amostragem com mais opções de personalização.

#### <a name="configure-sampling-settings"></a>Definir configurações de amostragem

Use métodos de extensão de `TelemetryProcessorChainBuilder` conforme mostrado abaixo para personalizar o comportamento de amostragem.

> [!IMPORTANT]
> Se você usar esse método para configurar a amostragem, certifique-se de definir a propriedade `aiOptions.EnableAdaptiveSampling` como `false` ao chamar `AddApplicationInsightsTelemetry()`.

```csharp
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

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Configurando a amostragem adaptável para Azure Functions

Siga as instruções [desta página](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) para configurar a amostragem adaptável para aplicativos em execução no Azure functions.

## <a name="fixed-rate-sampling"></a>Amostragem de taxa fixa

A amostragem de taxa fixa reduz o tráfego enviado do seu servidor Web e navegadores da Web. Ao contrário da amostragem adaptável, ela reduz a telemetria em uma taxa fixa decidida por você. A amostragem de taxa fixa está disponível para aplicativos ASP.NET, ASP.NET Core, Java e Python.

Assim como outras técnicas de amostragem, isso também retém itens relacionados. Ele também sincroniza a amostragem do cliente e do servidor para que os itens relacionados sejam retidos-por exemplo, ao examinar um modo de exibição de página na pesquisa, você pode encontrar suas solicitações de servidor relacionadas. 

Em Metrics Explorer, as taxas como contagens de solicitação e exceção são multiplicadas por um fator para compensar a taxa de amostragem, de forma que elas estejam aproximadamente corretas.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Configurando a amostragem de taxa fixa para aplicativos ASP.NET

1. **Desabilitar amostragem adaptável**: em [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md), remova ou comente o nó `AdaptiveSamplingTelemetryProcessor`.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Habilite o módulo amostragem de taxa fixa.** Adicione este trecho a [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md):
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Como alternativa, em vez de definir o parâmetro de amostragem no arquivo de `ApplicationInsights.config`, você pode definir esses valores de forma programática:

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

    ([Saiba mais sobre processadores de telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Configurando a amostragem de taxa fixa para aplicativos ASP.NET Core

1. **Desabilitar amostragem adaptável**: as alterações podem ser feitas no método `ConfigureServices`, usando `ApplicationInsightsServiceOptions`:

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

2. **Habilite o módulo amostragem de taxa fixa.** As alterações podem ser feitas no método `Configure`, conforme mostrado no trecho de código abaixo:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Configurando a amostragem de taxa fixa para aplicativos Java

Por padrão, nenhuma amostragem está habilitada no SDK do Java. Atualmente, ele só dá suporte à amostragem de taxa fixa. A amostragem adaptável não tem suporte no SDK do Java.

1. Baixe e configure seu aplicativo Web com o [SDK Application insights Java](../../azure-monitor/app/java-get-started.md)mais recente.

2. **Habilite o módulo amostragem de taxa fixa** adicionando o seguinte trecho ao arquivo `ApplicationInsights.xml`:

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

3. Você pode incluir ou excluir tipos específicos de telemetria de amostragem usando as seguintes marcas no `FixedRateSamplingTelemetryProcessor`da marca de `Processor`:
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

Os tipos de telemetria que podem ser incluídos ou excluídos da amostragem são: `Dependency`, `Event`, `Exception`, `PageView`, `Request`e `Trace`.

> [!NOTE]
> Para o percentual de amostragem, escolha uma porcentagem que esteja próxima de 100/N, em que N é um inteiro.  Atualmente, a amostragem não dá suporte a outros valores.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Configurando a amostragem de taxa fixa para aplicativos OpenCensus Python

Instrumente seu aplicativo com os mais recentes [OpenCensus Azure monitor exportadores](../../azure-monitor/app/opencensus-python.md).

> [!NOTE]
> A amostragem a taxa fixa não está disponível para o exportador de métricas. Isto significa que as métricas personalizadas são os únicos tipos de telemetria onde a amostragem não pode ser configurada. O exportador de métricas enviará toda a telemetria que rastreia.

#### <a name="fixed-rate-sampling-for-tracing"></a>Amostragem de taxa fixa para rastreio ####
Pode especificar um `sampler` como parte da configuração do `Tracer`. Se nenhum amostra explícito for fornecido, o `ProbabilitySampler` será usado por padrão. O `ProbabilitySampler` usaria uma taxa de 1/10000 por padrão, o que significa que uma das solicitações de 10000 será enviada para Application Insights. Se quiser especificar uma taxa de amostragem, veja abaixo.

Para especificar a taxa de amostragem, verifique se o `Tracer` especifica um amostra com uma taxa de amostragem entre 0,0 e 1,0, inclusive. Uma taxa de amostragem de 1.0 representa 100%, o que significa que todos os seus pedidos serão enviados como telemetria para Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Amostragem de taxa fixa para registos ####
Pode configurar amostras de taxa fixa para `AzureLogHandler` modificando o `logging_sampling_rate` argumento opcional. Se não for fornecido qualquer argumento, será utilizada uma taxa de amostragem de 1.0. Uma taxa de amostragem de 1.0 representa 100%, o que significa que todos os seus pedidos serão enviados como telemetria para Application Insights.

```python
exporter = metrics_exporter.new_metrics_exporter(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Configurando a amostragem de taxa fixa para páginas da Web com JavaScript

As páginas da Web baseadas em JavaScript podem ser configuradas para usar Application Insights. A telemetria é enviada do aplicativo cliente em execução no navegador do usuário, e as páginas podem ser hospedadas de qualquer servidor.

Ao [configurar suas páginas da Web baseadas em JavaScript para Application insights](javascript.md), modifique o trecho de código JavaScript que você obtém do portal de Application insights.

> [!TIP]
> Em ASP.NET aplicações com JavaScript incluídas, o corte normalmente vai em `_Layout.cshtml`.

Insira uma linha como `samplingPercentage: 10,` antes da chave de instrumentação:

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

Para o percentual de amostragem, escolha uma porcentagem que esteja próxima de 100/N, em que N é um inteiro. Atualmente, a amostragem não dá suporte a outros valores.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Coordenando a amostragem do lado do servidor e do cliente

O SDK do JavaScript do lado do cliente participa da amostragem de taxa fixa em conjunto com o SDK do lado do servidor. As páginas instrumentadas só enviarão telemetria do lado do cliente do mesmo usuário para o qual o SDK do lado do servidor fez sua decisão de incluir na amostragem. Essa lógica foi projetada para manter a integridade das sessões de usuário em aplicativos do lado do cliente e do servidor. Como resultado, de qualquer item de telemetria específico no Application Insights você pode encontrar todos os outros itens de telemetria para esse usuário ou sessão e, na pesquisa, você pode navegar entre exibições de página e solicitações relacionadas.

Se o cliente e a telemetria do lado do servidor não mostrarem exemplos coordenados:

* Verifique se você habilitou a amostragem no servidor e no cliente.
* Verifique se você definiu o mesmo percentual de amostragem no cliente e no servidor.
* Certifique-se de que a versão do SDK seja 2,0 ou superior.

## <a name="ingestion-sampling"></a>Amostragem de ingestão

A amostragem de ingestão opera no ponto em que a telemetria do seu servidor Web, navegadores e dispositivos atinge o ponto de extremidade de serviço Application Insights. Embora não reduza o tráfego de telemetria enviado do seu aplicativo, ele reduz a quantidade processada e retida (e cobrada) por Application Insights.

Use esse tipo de amostragem se seu aplicativo costuma passar por sua cota mensal e você não tiver a opção de usar qualquer um dos tipos de amostragem baseados em SDK. 

Defina a taxa de amostragem na página uso e custos estimados:

![Na folha visão geral do aplicativo, clique em configurações, cota, exemplos, selecione uma taxa de amostragem e clique em atualizar.](./media/sampling/data-sampling.png)

Como outros tipos de amostragem, o algoritmo retém os itens de telemetria relacionados. Por exemplo, quando você estiver inspecionando a telemetria na pesquisa, poderá encontrar a solicitação relacionada a uma exceção específica. Contagens de métricas, como taxa de solicitação e taxa de exceção, são retidas corretamente.

Os pontos de dados descartados por amostragem não estão disponíveis em nenhum recurso Application Insights, como a [exportação contínua](../../azure-monitor/app/export-telemetry.md).

A amostragem de ingestão não funciona enquanto a amostragem adaptável ou de taxa fixa está em operação. A amostragem adaptável é habilitada por padrão quando o SDK do ASP.NET ou o SDK do ASP.NET Core está sendo usado, ou quando Application Insights está habilitado no [serviço Azure app](azure-web-apps.md) ou usando status monitor. Quando a telemetria é recebida pelo ponto final do serviço Application Insights, examina a telemetria e se a taxa de amostragem for reportada como inferior a 100% (o que indica que a telemetria está a ser amostrada) então a taxa de amostragem de ingestão que definiu é ignorada.

> [!WARNING]
> O valor mostrado no bloco do portal indica o valor que você definiu para amostragem de ingestão. Ele não representa a taxa de amostragem real se qualquer tipo de amostragem do SDK (amostragem adaptável ou de taxa fixa) estiver em operação.

## <a name="when-to-use-sampling"></a>Quando usar a amostragem

Em geral, para a maioria dos aplicativos de pequeno e médio porte, você não precisa de amostragem. As informações de diagnóstico mais úteis e as estatísticas mais precisas são obtidas pela coleta de dados em todas as suas atividades de usuário. 

As principais vantagens da amostragem são:

* Application Insights serviço descarta ("limitadores") pontos de dados quando seu aplicativo envia uma taxa muito alta de telemetria em um intervalo de tempo curto. A amostragem reduz a probabilidade de que seu aplicativo Veja a limitação.
* Para manter dentro da [cota](pricing.md) de pontos de dados para seu tipo de preço. 
* Para reduzir o tráfego de rede da coleção de telemetria. 

### <a name="which-type-of-sampling-should-i-use"></a>Que tipo de amostragem devo usar?

**Use a amostragem de ingestão se:**

* Você geralmente usa sua cota mensal de telemetria.
* Você está recebendo muita telemetria dos navegadores da Web dos seus usuários.
* Você está usando uma versão do SDK que não dá suporte à amostragem, por exemplo, versões do ASP.NET anteriores a 2.

**Use a amostragem de taxa fixa se:**

* Você deseja obter a amostragem sincronizada entre o cliente e o servidor para que, quando estiver investigando eventos na [pesquisa](../../azure-monitor/app/diagnostic-search.md), você possa navegar entre eventos relacionados no cliente e no servidor, como exibições de página e solicitações HTTP.
* Você está confiante do percentual de amostragem apropriado para seu aplicativo. Ele deve ser alto o suficiente para obter métricas precisas, mas abaixo da taxa que excede a cota de preços e os limites de limitação.

**Use a amostragem adaptável:**

Se as condições para usar as outras formas de amostragem não se aplicarem, recomendamos a amostragem adaptável. Essa configuração é habilitada por padrão no SDK do ASP.NET/ASP.NET Core. Ele não reduzirá o tráfego até que uma determinada taxa mínima seja atingida, portanto, os sites de baixo uso provavelmente não serão amostrados.

## <a name="knowing-whether-sampling-is-in-operation"></a>Sabendo se a amostragem está em operação

Para descobrir a taxa de amostragem real, independentemente de onde ela foi aplicada, use uma [consulta de análise](../../azure-monitor/app/analytics.md) como esta:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Se você vir que `RetainedPercentage` para qualquer tipo é menor que 100, esse tipo de telemetria está sendo amostrado.

> [!IMPORTANT]
> Application Insights não faz amostragem de sessão, métricas (incluindo métricas personalizadas) ou tipos de telemetria do contador de desempenho em qualquer uma das técnicas de amostragem. Esses tipos são sempre excluídos da amostragem, pois uma redução na precisão pode ser altamente indesejável para esses tipos de telemetria.

## <a name="how-sampling-works"></a>Como funciona a amostragem

O algoritmo de amostragem decide quais itens de telemetria serão descartados e quais serão mantidos. Isso é verdadeiro se a amostragem é feita pelo SDK ou no serviço de Application Insights. A decisão de amostragem se baseia em várias regras que visam preservar todos os pontos de dados inter-relacionados intactos, mantendo uma experiência de diagnóstico em Application Insights que seja acionável e confiável mesmo com um conjunto de dados reduzido. Por exemplo, se seu aplicativo tiver uma solicitação com falha incluída em um exemplo, os itens de telemetria adicionais (como exceção e rastreamentos registrados para essa solicitação) serão mantidos. A amostragem mantém ou descarta todas elas juntas. Como resultado, ao examinar os detalhes da solicitação em Application Insights, você sempre poderá ver a solicitação junto com seus itens de telemetria associados.

A decisão de amostragem se baseia na ID da operação da solicitação, o que significa que todos os itens de telemetria pertencentes a uma determinada operação são preservados ou removidos. Para os itens de telemetria que não têm uma ID de operação definida (como itens de telemetria relatados de threads assíncronos sem nenhum contexto HTTP), a amostragem simplesmente captura uma porcentagem de itens de telemetria de cada tipo.

Ao apresentar a telemetria de volta para você, o serviço de Application Insights ajusta as métricas pelo mesmo percentual de amostragem que foi usado no momento da coleta, para compensar os pontos de dados ausentes. Portanto, ao examinar a telemetria em Application Insights, os usuários estão vendo as aproximações estatisticamente corretas que são muito próximas dos números reais.

A precisão da aproximação depende em grande parte da porcentagem de amostragem configurada. Além disso, a precisão aumenta para aplicativos que lidam com um grande volume de solicitações geralmente semelhantes de muitos usuários. Por outro lado, para aplicativos que não funcionam com uma carga significativa, a amostragem não é necessária, pois esses aplicativos normalmente podem enviar todas as suas telemetrias enquanto ficam dentro da cota, sem causar a perda de dados da limitação. 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

*Qual é o comportamento de amostragem padrão nos SDKs ASP.NET e ASP.NET Core?*

* Se você estiver usando uma das versões mais recentes do SDK acima, a amostragem adaptável será habilitada por padrão com cinco itens de telemetria por segundo.
  Há dois nós de `AdaptiveSamplingTelemetryProcessor` adicionados por padrão e um inclui o tipo de `Event` em amostragem, enquanto o outro exclui o tipo de `Event` da amostragem. Essa configuração significa que o SDK tentará limitar os itens de telemetria a cinco itens de telemetria de tipos de `Event` e cinco itens de telemetria de todos os outros tipos combinados, garantindo assim que `Events` sejam amostrados separadamente de outros tipos de telemetria. Normalmente, os eventos são usados para telemetria de negócios e provavelmente não devem ser afetados por volumes de telemetria de diagnóstico.
  
  O seguinte mostra o arquivo de `ApplicationInsights.config` padrão gerado. No ASP.NET Core, o mesmo comportamento padrão é habilitado no código. Use os [exemplos na seção anterior desta página](#configuring-adaptive-sampling-for-aspnet-core-applications) para alterar esse comportamento padrão.

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

* Não. SamplingTelemetryProcessors ignorar itens de considerações de amostragem se o item já estiver sendo amostrado. O mesmo também é verdadeiro para a amostragem de ingestão, o que não aplicará amostragem a esses itens já amostrados no próprio SDK.

*Por que a amostragem não é simples "coletar X por cento de cada tipo de telemetria"?*

* Embora essa abordagem de amostragem forneça um alto nível de precisão em aproximaçãos de métricas, ela quebraria a capacidade de correlacionar os dados de diagnóstico por usuário, sessão e solicitação, que é essencial para o diagnóstico. Portanto, a amostragem funciona melhor com políticas como "coletar todos os itens de telemetria para X% dos usuários do aplicativo" ou "coletar toda a telemetria para X% das solicitações do aplicativo". Para os itens de telemetria não associados às solicitações (como processamento assíncrono em segundo plano), o fallback é para "coletar X por cento de todos os itens de cada tipo de telemetria". 

*A porcentagem de amostragem pode mudar ao longo do tempo?*

* Sim, a amostragem adaptável altera gradualmente o percentual de amostragem, com base no volume observado no momento da telemetria.

*Se eu usar a amostragem de taxa fixa, como saber qual porcentagem de amostragem funcionará melhor para o meu aplicativo?*

* Uma maneira é começar com a amostragem adaptável, descobrir em que taxa ela se liquida (consulte a pergunta acima) e, em seguida, alternar para a amostragem de taxa fixa usando essa taxa. 
  
    Caso contrário, você precisará adivinhar. Analise seu uso de telemetria atual em Application Insights, observe qualquer limitação que esteja ocorrendo e estime o volume da telemetria coletada. Essas três entradas, junto com seu tipo de preço selecionado, sugerem quanto você talvez queira reduzir o volume da telemetria coletada. No entanto, um aumento no número de seus usuários ou alguma outra mudança no volume de telemetria pode invalidar sua estimativa.

*O que acontecerá se eu configurar o percentual de amostragem como muito baixo?*

* Porcentagens de amostragem excessivamente baixas causam uma amostragem muito agressiva e reduzem a precisão das aproximações quando Application Insights tenta compensar a visualização dos dados para a redução do volume de dados. Além disso, sua experiência de diagnóstico pode ser afetada negativamente, pois algumas das solicitações com falha ou lentidão podem ser amostradas.

*O que acontecerá se eu configurar o percentual de amostragem como muito alto?*

* Configurar um percentual de amostragem muito alto (não tão agressivo) resulta em uma redução insuficiente no volume da telemetria coletada. Você ainda pode enfrentar a perda de dados de telemetria relacionada à limitação, e o custo de usar Application Insights pode ser maior do que o planejado devido a encargos excedentes.

*Em quais plataformas posso usar a amostragem?*

* A amostragem de ingestão pode ocorrer automaticamente para qualquer telemetria acima de um determinado volume, se o SDK não estiver executando a amostragem. Essa configuração funcionaria, por exemplo, se você estiver usando uma versão mais antiga do SDK do ASP.NET ou do SDK do Java.
* Se você estiver usando os SDKs atuais do ASP.NET ou do ASP.NET Core (hospedados no Azure ou em seu próprio servidor), obterá amostragem adaptável por padrão, mas você pode alternar para a taxa fixa, conforme descrito acima. Com a amostragem de taxa fixa, o SDK do navegador sincroniza automaticamente com os eventos relacionados de exemplo. 
* Se você estiver usando o SDK do Java atual, poderá configurar `ApplicationInsights.xml` para ativar a amostragem de taxa fixa. A amostragem é desativada por padrão. Com a amostragem de taxa fixa, o SDK do navegador e o servidor são sincronizados automaticamente com os eventos relacionados de exemplo.

*Há certos eventos raros que sempre quero ver. Como posso obtê-los após o módulo de amostragem?*

* A melhor maneira de conseguir isso é escrever um [personalizada telemetryinitializer](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)personalizado, que define o `SamplingPercentage` como 100 no item de telemetria que você deseja reter, conforme mostrado abaixo. À medida que inicializadores são garantidos para serem executados antes dos processadores de telemetria (incluindo amostragem), isso garante que todas as técnicas de amostragem ignorem esse item de quaisquer considerações de amostragem. Os iniciantes de telemetria personalizados estão disponíveis no ASP.NET SDK, no ASP.NET Core SDK, no JavaScript SDK e no Java SDK. Por exemplo, pode configurar um inicializador de telemetria utilizando o SDK ASP.NET:

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

## <a name="older-sdk-versions"></a>Versões mais antigas do SDK

A amostragem adaptável está disponível para o SDK do Application Insights para ASP.NET v 2.0.0-Beta3 e posterior, Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0-beta1 e posterior e está habilitada por padrão.

A amostragem de taxa fixa é um recurso do SDK em versões do ASP.NET do 2.0.0 e do SDK do Java versão 2.0.1 e em diante.

Antes do v 2.5.0-beta2 do SDK do ASP.NET e v 2.2.0-Beta3 do SDK ASP.NET Core, a decisão de amostragem foi baseada no hash da ID de usuário para aplicativos que definem "user" (ou seja, a maioria dos aplicativos Web típicos). Para os tipos de aplicativos que não definiram os usuários (como serviços Web), a decisão de amostragem foi baseada na ID da operação da solicitação. As versões recentes dos SDKs ASP.NET e ASP.NET Core usam a ID da operação para a decisão de amostragem.

## <a name="next-steps"></a>Passos seguintes

* A [filtragem](../../azure-monitor/app/api-filtering-sampling.md) pode fornecer um controle mais estrito do que o seu SDK envia.
* Leia o artigo rede do desenvolvedor [otimizar a telemetria com Application insights](https://msdn.microsoft.com/magazine/mt808502.aspx).
