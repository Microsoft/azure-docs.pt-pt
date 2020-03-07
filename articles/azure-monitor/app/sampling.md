---
title: Amostragem de telemetria em Insights de Aplicação Azure  Microsoft Docs
description: Como manter o volume de telemetria sob controlo.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: fc9db23f7733f97ca207e834d4543fbdb1b9db5c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362310"
---
# <a name="sampling-in-application-insights"></a>Amostragem no Application Insights

A amostragem é uma característica no [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). É a forma recomendada de reduzir o tráfego de telemetria, os custos de dados e os custos de armazenamento, preservando ao mesmo tempo uma análise estatisticamente correta dos dados da aplicação. A amostragem também ajuda a evitar que os Insights de Aplicação estrangulem a sua telemetria. O filtro de amostragem seleciona itens relacionados, para que possa navegar entre itens quando estiver a fazer investigações de diagnóstico.

Quando as contagens métricas são apresentadas no portal, são renormalizadas para ter em conta a amostragem. Fazê-lo minimiza qualquer efeito nas estatísticas.

## <a name="brief-summary"></a>Breve resumo

* Existem três tipos diferentes de amostragem: amostragem adaptável, amostragem de taxa fixa e amostragem de ingestão.
* A amostragem adaptativa é ativada por padrão em todas as versões mais recentes dos Insights de Aplicação ASP.NET e ASP.NET Core Software Development Kits (SDKs). Também é utilizado pelas [Funções Azure.](https://docs.microsoft.com/azure/azure-functions/functions-overview)
* A amostragem de taxa fixa está disponível em versões recentes dos SDKs de Insights de Aplicação para ASP.NET, ASP.NET Core, Java e Python.
* A amostragem de ingestão funciona no ponto final do serviço Application Insights. Só se aplica quando não existe qualquer outra amostragem. Se o SDK provar a sua telemetria, a amostragem de ingestão é desativada.
* Para aplicações web, se você registar eventos personalizados e precisar de garantir que um conjunto de eventos é retido ou descartado em conjunto, os eventos devem ter o mesmo valor `OperationId`.
* Se escrever consultas de Análise, deve ter em conta a [amostragem.](../../azure-monitor/log-query/aggregations.md) Em particular, em vez de simplesmente contar registos, deve utilizar `summarize sum(itemCount)`.
* Alguns tipos de telemetria, incluindo métricas de desempenho e métricas personalizadas, são sempre mantidos independentemente de a amostragem estar ativada ou não.

O quadro seguinte resume os tipos de amostragem disponíveis para cada SDK e tipo de aplicação:

| Insights de aplicação SDK | Amostragem adaptável suportada | Amostragem de taxa fixa suportada | Amostragem de ingestão suportada |
|-|-|-|-|
| ASP.NET | [Sim (por defeito)](#configuring-adaptive-sampling-for-aspnet-applications) | [Sim](#configuring-fixed-rate-sampling-for-aspnet-applications) | Só se nenhuma outra amostragem estiver em vigor |
| Núcleo de ASP.NET | [Sim (por defeito)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Sim](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Só se nenhuma outra amostragem estiver em vigor |
| Funções do Azure | [Sim (por defeito)](#configuring-adaptive-sampling-for-azure-functions) | Não | Só se nenhuma outra amostragem estiver em vigor |
| Java | Não | [Sim](#configuring-fixed-rate-sampling-for-java-applications) | Só se nenhuma outra amostragem estiver em vigor |
| Python | Não | [Sim](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Só se nenhuma outra amostragem estiver em vigor |
| Todos os outros | Não | Não | [Sim](#ingestion-sampling) |

> [!NOTE]
> A informação na maior parte desta página aplica-se às versões atuais dos SDKs de Insights de Aplicação. Para obter informações sobre versões mais antigas dos SDKs, [consulte a secção abaixo](#older-sdk-versions).

## <a name="types-of-sampling"></a>Tipos de amostragem

Existem três métodos diferentes de amostragem:

* **A amostragem adaptável ajusta** automaticamente o volume de telemetria enviado do SDK na sua aplicação ASP.NET/ASP.NET Core e das Funções Azure. Esta é a amostragem predefinida quando utiliza o ASP.NET ou ASP.NET Core SDK. A amostragem adaptável está atualmente disponível apenas para telemetria do lado do servidor ASP.NET, e para funções Azure.

* **A amostragem de taxa fixa** reduz o volume de telemetria enviado tanto do seu ASP.NET como do ASP.NET servidor Core ou Java e dos navegadores dos seus utilizadores. Estabeleceu a taxa. O cliente e o servidor sincronizarão a sua amostragem para que, na Pesquisa, possa navegar entre as visualizações e pedidos de página relacionados.

* **A amostragem de ingestão** ocorre no ponto final do serviço Application Insights. Descarta alguma da telemetria que chega da sua aplicação, a uma taxa de amostragem que definiu. Não reduz o tráfego de telemetria enviado da sua app, mas ajuda-o a manter-se dentro da sua quota mensal. A principal vantagem da amostragem de ingestão é que pode definir a taxa de amostragem sem reimplantar a sua aplicação. A amostragem de ingestão funciona uniformemente para todos os servidores e clientes, mas não se aplica quando outros tipos de amostragem estão em funcionamento.

> [!IMPORTANT]
> Se estiverem em funcionamento métodos de amostragem adaptativos ou de taxa fixa, a amostragem de ingestão é desativada.

## <a name="adaptive-sampling"></a>Amostragem adaptável

A amostragem adaptável afeta o volume de telemetria enviado da sua aplicação de servidor web para o ponto final do serviço Application Insights.

> [!TIP]
> A amostragem adaptativa é ativada por predefinição quando utiliza o ASP.NET SDK ou o ASP.NET Core SDK, e também é ativada por predefinição para funções Azure.

O volume é ajustado automaticamente para manter dentro de uma taxa máxima de tráfego especificada, e é controlado através da definição `MaxTelemetryItemsPerSecond`. Se a aplicação produzir uma baixa quantidade de telemetria, como quando se depura ou devido a uma baixa utilização, os itens não serão deixados cair pelo processador de amostragem enquanto o volume estiver abaixo `MaxTelemetryItemsPerSecond`. À medida que o volume de telemetria aumenta, a taxa de amostragem é ajustada de modo a atingir o volume-alvo. O ajuste é recalculado em intervalos regulares, e baseia-se numa média móvel da taxa de transmissão de saída.

Para atingir o volume-alvo, algumas das telemetrias geradas são descartadas. Mas, como outros tipos de amostragem, o algoritmo retém itens de telemetria relacionados. Por exemplo, quando estiver a inspecionar a telemetria em Busca, poderá encontrar o pedido relacionado com uma determinada exceção.

As contagens métricas, tais como a taxa de pedido e a taxa de exceção são ajustadas para compensar a taxa de amostragem, de modo que apresentem valores aproximadamente corretos no Explorador Métrico.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Configuração da amostragem adaptável para aplicações ASP.NET

> [!NOTE]
> Esta secção aplica-se a aplicações ASP.NET, não a ASP.NET aplicações Core. [Saiba mais tarde configurar amostras adapttivas para aplicações ASP.NET Core mais tarde neste documento.](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)

Em [`ApplicationInsights.config`, ](../../azure-monitor/app/configuration-with-applicationinsights-config.md)pode ajustar vários parâmetros no nó `AdaptiveSamplingTelemetryProcessor`. Os números apresentados são os valores predefinidos:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    A taxa-alvo que o algoritmo adaptativo visa **em cada anfitrião**do servidor . Se a sua aplicação web for executado em muitos anfitriões, reduza este valor de modo a permanecer dentro da sua taxa de tráfego-alvo no portal Application Insights.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    O intervalo em que a taxa atual de telemetria é reavaliada. A avaliação é feita como uma média móvel. Talvez queira encurtar este intervalo se a sua telemetria for suscetível de explosões repentinas.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Quando a amostragem de valor percentual muda, quando nos é permitido baixar novamente a percentagem de amostragem para capturar menos dados?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Quando a amostragem de valor percentual muda, quando nos é permitido aumentar a percentagem de amostragem novamente para capturar mais dados?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Como a percentagem de amostragem varia, qual é o valor mínimo que podemos definir?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Como a percentagem de amostragem varia, qual é o valor máximo que podemos definir?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    No cálculo da média móvel, isto especifica o peso que deve ser atribuído ao valor mais recente. Use um valor igual ou inferior a 1. Valores menores tornam o algoritmo menos reativo a mudanças repentinas.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    A quantidade de telemetria para provar quando a aplicação acaba de começar. Não reduza este valor enquanto está a depurar.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Uma lista de tipos deslimitada sem cólon que não quer estar sujeita a amostragem. Os tipos reconhecidos são: `Dependency`, `Event`, `Exception`, `PageView`, `Request`, `Trace`. É transmitida toda a telemetria dos tipos especificados; os tipos que não estão especificados serão amostrados.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Uma lista de tipos deslimitadas do cólon que pretende sujeitar à amostragem. Os tipos reconhecidos são: `Dependency`, `Event`, `Exception`, `PageView`, `Request`, `Trace`. Os tipos especificados serão amostrados; toda a telemetria dos outros tipos será sempre transmitida.

**Para desligar** a amostragem adaptativa, retire o nó `AdaptiveSamplingTelemetryProcessor` do nó do `ApplicationInsights.config`.

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativa: Configure amostragem adaptável em código

Em vez de definir o parâmetro de amostragem no ficheiro `.config`, pode definir programáticamente estes valores.

1. Retire todos os nódosos `AdaptiveSamplingTelemetryProcessor` do ficheiro `.config`.
2. Utilize o seguinte corte para configurar a amostragem adaptável:

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

    ( Conheça os processadores de[telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Também pode ajustar a taxa de amostragem para cada tipo de telemetria individualmente, ou pode mesmo excluir certos tipos de serem amostrados:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Configuração da amostragem adaptável para aplicações ASP.NET Core

Não existe `ApplicationInsights.config` para ASP.NET aplicações Core, por isso toda a configuração é feita por código.
A amostragem adaptativa é ativada por padrão para todas as aplicações ASP.NET Core. Pode desativar ou personalizar o comportamento de amostragem.

#### <a name="turning-off-adaptive-sampling"></a>Desligando a amostragem adaptativa

A função de amostragem predefinida pode ser desativada ao adicionar o serviço Application Insights, no método `ConfigureServices`, utilizando `ApplicationInsightsServiceOptions` dentro do ficheiro `Startup.cs`:

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

Utilize métodos de extensão de `TelemetryProcessorChainBuilder` como mostrado abaixo para personalizar o comportamento de amostragem.

> [!IMPORTANT]
> Se utilizar este método para configurar a amostragem, certifique-se de que define a propriedade `aiOptions.EnableAdaptiveSampling` para `false` quando ligar para `AddApplicationInsightsTelemetry()`.

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

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Configuração de amostragem adaptável para funções azure

Siga as instruções [desta página](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) para configurar a amostragem adaptável para aplicações em execução em Funções Azure.

## <a name="fixed-rate-sampling"></a>Amostragem a taxa fixa

A amostragem de taxa fixa reduz o tráfego enviado do seu servidor web e navegadores web. Ao contrário da amostragem adaptativa, reduz a telemetria a uma taxa fixa decidida por si. A amostragem de taxa fixa está disponível para aplicações ASP.NET, ASP.NET Core, Java e Python.

Tal como outras técnicas de amostragem, isto também retém itens relacionados. Também sincroniza a amostragem do cliente e do servidor para que os itens relacionados sejam retidos - por exemplo, quando se olha para uma visualização de página em Search, pode encontrar os seus pedidos de servidor relacionados. 

No Metrics Explorer, as taxas como as contagens de pedido e de exceção são multiplicadas por um fator de compensação pela taxa de amostragem, de modo a que estejam aproximadamente corretas.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Configuração da amostragem de taxa fixa para aplicações ASP.NET

1. **Desativar a amostragem adaptativa**: Em [`ApplicationInsights.config`, ](../../azure-monitor/app/configuration-with-applicationinsights-config.md)retire ou comente o nó `AdaptiveSamplingTelemetryProcessor`.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Ativar o módulo de amostragem de taxa fixa.** Adicione este corte a [`ApplicationInsights.config`: ](../../azure-monitor/app/configuration-with-applicationinsights-config.md)
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Alternativamente, em vez de colocar o parâmetro de amostragem no ficheiro `ApplicationInsights.config`, pode definir programáticamente estes valores:

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

    ( Conheça os processadores de[telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Configurar a amostragem de taxa fixa para aplicações ASP.NET Core

1. **Desativar a amostragem adaptativa**: Podem ser efetuadas alterações no método `ConfigureServices`, utilizando `ApplicationInsightsServiceOptions`:

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

2. **Ativar o módulo de amostragem de taxa fixa.** Alterações podem ser feitas no método `Configure`, como indicado no corte abaixo:

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

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Configuração da amostragem de taxa fixa para aplicações java

Por padrão, não é ativada qualquer amostragem no Java SDK. Atualmente, apenas suporta a amostragem de taxa fixa. A amostragem adaptável não é suportada no Java SDK.

1. Descarregue e configure a sua aplicação web com as mais recentes Informações de [Aplicação Java SDK](../../azure-monitor/app/java-get-started.md).

2. **Ativar o módulo de amostragem de taxa fixa** adicionando o seguinte corte ao ficheiro `ApplicationInsights.xml`:

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

3. Pode incluir ou excluir tipos específicos de telemetria da amostragem utilizando as seguintes etiquetas no interior da `FixedRateSamplingTelemetryProcessor`da etiqueta `Processor`:
   
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
> Para a percentagem de amostragem, escolha uma percentagem próxima de 100/N onde N é um inteiro.  Atualmente a amostragem não suporta outros valores.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Configuração de amostragem de taxa fixa para aplicações OpenCensus Python

Instrumente a sua aplicação com os mais recentes [exportadores OpenCensus Azure Monitor](../../azure-monitor/app/opencensus-python.md).

> [!NOTE]
> A amostragem a taxa fixa não está disponível para o exportador de métricas. Isto significa que as métricas personalizadas são os únicos tipos de telemetria onde a amostragem não pode ser configurada. O exportador de métricas enviará toda a telemetria que rastreia.

#### <a name="fixed-rate-sampling-for-tracing"></a>Amostragem de taxa fixa para rastreio ####
Pode especificar um `sampler` como parte da configuração do `Tracer`. Se não for fornecido um amostrador explícito, o `ProbabilitySampler` será utilizado por defeito. O `ProbabilitySampler` utilizaria uma taxa de 1/10000 por padrão, o que significa que um em cada 10000 pedidos será enviado para A Visão Insights. Se quiser especificar uma taxa de amostragem, veja abaixo.

Para especificar a taxa de amostragem, certifique-se de que o seu `Tracer` especifica um amostrador com uma taxa de amostragem entre 0,0 e 1,0 inclusive. Uma taxa de amostragem de 1.0 representa 100%, o que significa que todos os seus pedidos serão enviados como telemetria para Application Insights.

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

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Configurar amostras de taxa fixa para páginas web com JavaScript

As páginas web baseadas em JavaScript podem ser configuradas para utilizar os Insights da Aplicação. A telemetria é enviada a partir da aplicação do cliente que funciona dentro do navegador do utilizador, e as páginas podem ser hospedadas a partir de qualquer servidor.

Quando [configurar as suas páginas web baseadas em JavaScript para Insights de Aplicação,](javascript.md)modifique o corte JavaScript que obtém do portal Deinsights de Aplicações.

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

Para a percentagem de amostragem, escolha uma percentagem próxima de 100/N onde N é um inteiro. Atualmente a amostragem não suporta outros valores.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Coordenar a amostragem do lado do servidor e do lado do cliente

O JavaScript SDK do lado do cliente participa na amostragem de taxa fixa em conjunto com o SDK do lado do servidor. As páginas instrumentadas apenas enviarão telemetria do lado do cliente do mesmo utilizador para o qual o SDK do lado do servidor tomou a sua decisão de incluir na amostragem. Esta lógica foi concebida para manter a integridade das sessões de utilizador através de aplicações do lado do cliente e do servidor. Como resultado, a partir de qualquer item de telemetria em Insights de Aplicação pode encontrar todos os outros itens de telemetria para este utilizador ou sessão e em Pesquisa, pode navegar entre as vistas e pedidos de página relacionados.

Se o seu cliente e a telemetria do lado do servidor não mostrarem amostras coordenadas:

* Verifique se permitiu a amostragem tanto no servidor como no cliente.
* Verifique se definiu a mesma percentagem de amostragem tanto no cliente como no servidor.
* Certifique-se de que a versão SDK é de 2.0 ou superior.

## <a name="ingestion-sampling"></a>Amostragem de ingestão

A amostragem de ingestão funciona no ponto em que a telemetria do seu servidor web, navegadores e dispositivos atinge o ponto final do serviço Application Insights. Embora não reduza o tráfego de telemetria enviado da sua aplicação, reduz o valor processado e retido (e cobrado) pela Application Insights.

Utilize este tipo de amostragem se a sua aplicação ultrapassar frequentemente a sua quota mensal e não tiver a opção de utilizar nenhum dos tipos de amostragem baseados em SDK. 

Delineie a taxa de amostragem na página de utilização e custos estimados:

![A partir da lâmina de visão geral da aplicação, clique em Definições, Quota, Amostras, em seguida, selecione uma taxa de amostragem e clique em Atualizar.](./media/sampling/data-sampling.png)

Como outros tipos de amostragem, o algoritmo retém itens de telemetria relacionados. Por exemplo, quando estiver a inspecionar a telemetria em Busca, poderá encontrar o pedido relacionado com uma determinada exceção. As contagens métricas, tais como a taxa de pedido e a taxa de exceção, são corretamente mantidas.

Os pontos de dados que são descartados por amostragem não estão disponíveis em qualquer recurso de Insights de Aplicação, como a [Exportação Contínua.](../../azure-monitor/app/export-telemetry.md)

A amostragem de ingestão não funciona enquanto a amostragem adaptável ou de taxa fixa está em funcionamento. A amostragem adaptativa é ativada por padrão quando o SDK ASP.NET ou o ASP.NET Core SDK está a ser utilizado, ou quando os Insights de Aplicação são ativados no Serviço de [Aplicações Azure](azure-web-apps.md) ou utilizando o Status Monitor. Quando a telemetria é recebida pelo ponto final do serviço Application Insights, examina a telemetria e se a taxa de amostragem for reportada como inferior a 100% (o que indica que a telemetria está a ser amostrada) então a taxa de amostragem de ingestão que definiu é ignorada.

> [!WARNING]
> O valor mostrado no azulejo do portal indica o valor que definiu para a amostragem de ingestão. Não representa a taxa real de amostragem se algum tipo de amostragem sdK (amostragem adaptável ou de taxa fixa) estiver em funcionamento.

## <a name="when-to-use-sampling"></a>Quando utilizar amostras

Em geral, para a maioria das aplicações de tamanho pequeno e médio não precisa de amostragem. As informações de diagnóstico mais úteis e as estatísticas mais precisas são obtidas através da recolha de dados sobre todas as suas atividades de utilizador. 

As principais vantagens da amostragem são:

* O serviço Deinsights de Aplicação cai ("aceleradores") pontos de dados quando a sua aplicação envia uma taxa muito elevada de telemetria num curto intervalo de tempo. A amostragem reduz a probabilidade de a sua aplicação ver o estrangulamento ocorrer.
* Para manter dentro da [quota](pricing.md) de pontos de dados para o seu nível de preços. 
* Para reduzir o tráfego de rede da recolha de telemetria. 

### <a name="which-type-of-sampling-should-i-use"></a>Que tipo de amostragem devo usar?

**Utilize amostras de ingestão se:**

* Usa-se frequentemente a sua quota mensal de telemetria.
* Está a receber demasiada telemetria dos navegadores web dos seus utilizadores.
* Está a usar uma versão do SDK que não suporta a amostragem - por exemplo, ASP.NET versões mais cedo do que 2.

**Utilize amostras de amostragem a taxa fixa se:**

* Você quer uma amostragem sincronizada entre cliente e servidor para que, quando estiver a investigar eventos em [Search,](../../azure-monitor/app/diagnostic-search.md)possa navegar entre eventos relacionados no cliente e servidor, como visualizações de página e pedidos HTTP.
* Está confiante na percentagem de amostragem adequada para a sua aplicação. Deve ser alto o suficiente para obter métricas precisas, mas abaixo da taxa que excede a sua quota de preços e os limites de estrangulamento.

**Utilize amostras adapttivas:**

Se as condições de utilização das outras formas de amostragem não se aplicarem, recomendamos uma amostragem adaptável. Esta definição é ativada por padrão no ASP.NET/ASP.NET Core SDK. Não reduzirá o tráfego até que uma determinada taxa mínima seja atingida, pelo que os locais de baixa utilização provavelmente não serão amostrados.

## <a name="knowing-whether-sampling-is-in-operation"></a>Saber se a amostragem está em funcionamento

Para descobrir a taxa de amostragem real, independentemente de onde tenha sido aplicada, utilize uma [consulta de Analytics](../../azure-monitor/app/analytics.md) como esta:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Se vir que `RetainedPercentage` para qualquer tipo é inferior a 100, então esse tipo de telemetria está a ser amostrado.

> [!IMPORTANT]
> Aplicação Insights não experimenta sessão, métricas (incluindo métricas personalizadas) ou tipos de telemetria de contra-desempenho em qualquer uma das técnicas de amostragem. Estes tipos são sempre excluídos da amostragem, uma vez que uma redução da precisão pode ser altamente indesejável para estes tipos de telemetria.

## <a name="how-sampling-works"></a>Como funciona a amostragem

O algoritmo de amostragem decide quais itens de telemetria a cair, e quais os que devem guardar. Isto é verdade se a amostragem é feita pelo SDK ou pelo serviço Devisão de Aplicações. A decisão de amostragem baseia-se em várias regras que visam preservar intactos todos os pontos de dados interrelacionados, mantendo uma experiência de diagnóstico em Insights de Aplicação que seja exequível e fiável mesmo com um conjunto de dados reduzido. Por exemplo, se a sua aplicação tiver um pedido falhado incluído numa amostra, os itens adicionais de telemetria (como exceção e vestígios registados para este pedido) serão retidos. A amostragem mantém ou deixa-as todas juntas. Como resultado, quando você olha para os detalhes do pedido em Insights de Aplicação, você pode sempre ver o pedido juntamente com seus itens de telemetria associados.

A decisão de amostragem baseia-se na identificação de funcionamento do pedido, o que significa que todos os artigos de telemetria pertencentes a uma determinada operação são preservados ou abandonados. Para os itens de telemetria que não possuam um conjunto de ID de operação (como itens de telemetria relatados a partir de fios assíncronos sem contexto HTTP) a amostragem simplesmente captura uma percentagem de itens de telemetria de cada tipo.

Ao apresentar-lhe telemetria, o serviço Application Insights ajusta as métricas pela mesma percentagem de amostragem que foi utilizada no momento da recolha, para compensar os pontos de dados em falta. Assim, quando se olha para a telemetria em Insights de Aplicação, os utilizadores estão a ver aproximações estatisticamente corretas que estão muito próximas dos números reais.

A precisão da aproximação depende em grande parte da percentagem de amostragem configurada. Além disso, a precisão aumenta para aplicações que lidam com um grande volume de pedidos geralmente semelhantes de muitos utilizadores. Por outro lado, para aplicações que não funcionam com uma carga significativa, a amostragem não é necessária, uma vez que estas aplicações podem normalmente enviar toda a sua telemetria enquanto se mantêm dentro da quota, sem causar perda de dados por estrangulamento. 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

*Qual é o comportamento de amostragem padrão nos ASP.NET e ASP.NET SDKs core?*

* Se estiver a utilizar uma das versões mais recentes do SDK acima, a Amostragem Adaptive é ativada por padrão com cinco itens de telemetria por segundo.
  Existem dois `AdaptiveSamplingTelemetryProcessor` nós adicionados por padrão, e um inclui o tipo `Event` em amostragem, enquanto o outro exclui o tipo `Event` da amostragem. Esta configuração significa que o SDK tentará limitar os itens de telemetria a cinco artigos de telemetria de tipos `Event`, e cinco itens de telemetria de todos os outros tipos combinados, garantindo assim que `Events` são amostrados separadamente de outros tipos de telemetria. Os eventos são normalmente utilizados para telemetria de negócios, e muito provavelmente não devem ser afetados por volumes de telemetria de diagnóstico.
  
  O seguinte mostra o ficheiro `ApplicationInsights.config` predefinido gerado. Em ASP.NET Core, o mesmo comportamento padrão é ativado em código. Utilize os [exemplos na secção anterior desta página](#configuring-adaptive-sampling-for-aspnet-core-applications) para alterar este comportamento predefinido.

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

*A telemetria pode ser experimentada mais de uma vez?*

* Não. AmostragemOsProcessadores de Telemetria ignoram itens de considerações de amostragem se o artigo já estiver amostrado. O mesmo acontece com a amostragem de ingestão, que não aplica amostragem aos itens já amostrados no próprio SDK.

*Porque é que provar um simples "recolher X por cento de cada tipo de telemetria"?*

* Embora esta abordagem de amostragem proporcionasse um elevado nível de precisão nas aproximações métricas, quebraria a capacidade de correlacionar os dados de diagnóstico por utilizador, sessão e pedido, o que é fundamental para os diagnósticos. Por isso, a amostragem funciona melhor com políticas como "recolher todos os itens de telemetria para X por cento dos utilizadores de aplicações", ou "recolher toda a telemetria para X por cento dos pedidos de aplicações". Para os itens de telemetria não associados aos pedidos (como processamento assíncrono de fundo), o recuo é "recolher X por cento de todos os itens para cada tipo de telemetria". 

*A percentagem de amostragem pode mudar ao longo do tempo?*

* Sim, a amostragem adaptável altera gradualmente a percentagem de amostragem, com base no volume atualmente observado da telemetria.

*Se eu usar amostragem de taxa fixa, como sei qual a percentagem de amostragem que vai funcionar melhor para a minha aplicação?*

* Uma maneira é começar com a amostragem adaptativa, descobrir em que taxa se instala (ver a pergunta acima), e depois mudar para amostragem de taxa fixa usando essa taxa. 
  
    Caso contrário, tem que adivinhar. Analise o seu uso atual de telemetria em Application Insights, observe qualquer estrangulamento que esteja a ocorrer, e calcule o volume da telemetria recolhida. Estas três inputs, juntamente com o seu nível de preços selecionado, sugerem quanto você pode querer reduzir o volume da telemetria recolhida. No entanto, um aumento do número de utilizadores ou alguma outra mudança no volume de telemetria pode invalidar a sua estimativa.

*O que acontece se eu configurar a percentagem de amostragem para ser muito baixa?*

* Percentagens de amostragem excessivamente baixas causam amostragem demasiado agressiva e reduzem a precisão das aproximações quando o Application Insights tenta compensar a visualização dos dados para a redução do volume de dados. Também a sua experiência de diagnóstico pode ser negativamente impactada, uma vez que alguns dos pedidos pouco frequentemente falhados ou lentos podem ser amostrados.

*O que acontece se eu configurar a percentagem de amostragem para ser muito alta?*

* Configurar uma percentagem de amostragem demasiado elevada (não suficientemente agressiva) resulta numa redução insuficiente do volume da telemetria recolhida. Ainda pode experimentar a perda de dados de telemetria relacionada com a aceleração, e o custo de utilização de Insights de Aplicação pode ser maior do que o planeado devido a encargos excessivos.

*Em que plataformas posso usar amostras?*

* A amostragem de ingestão pode ocorrer automaticamente para qualquer telemetria acima de um determinado volume, se o SDK não estiver a realizar amostras. Esta configuração funcionaria, por exemplo, se estiver a utilizar uma versão mais antiga do SDK ASP.NET ou Java SDK.
* Se estiver a utilizar os ASP.NET atuais ou ASP.NET SDKs Core (hospedados no Azure ou no seu próprio servidor), obtém uma amostragem adaptativa por padrão, mas pode mudar para a taxa fixa como acima descrito. Com amostragem de taxa fixa, o Navegador SDK sincroniza automaticamente para eventos relacionados com amostras. 
* Se estiver a utilizar o Atual Java SDK, pode configurar `ApplicationInsights.xml` para ligar a amostragem de taxa fixa. A amostragem é desligada por defeito. Com amostragem de taxa fixa, o Navegador SDK e o servidor sincronizam automaticamente para experimentar eventos relacionados.

*Há certos eventos raros que sempre quis ver. Como posso fazê-los passar pelo módulo de amostragem?*

* A melhor maneira de o conseguir é escrever um [TelemettryInitializer](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)personalizado, que define o `SamplingPercentage` a 100 no item de telemetria que pretende reter, como mostrado abaixo. Como os iniciais são garantidos para serem executados antes dos processadores de telemetria (incluindo amostragem), isto garante que todas as técnicas de amostragem ignorarão este item de quaisquer considerações de amostragem. Os iniciantes de telemetria personalizados estão disponíveis no ASP.NET SDK, no ASP.NET Core SDK, no JavaScript SDK e no Java SDK. Por exemplo, pode configurar um inicializador de telemetria utilizando o SDK ASP.NET:

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

A amostragem adaptativa está disponível para o SDK de Insights de aplicação para ASP.NET v2.0.0-beta3 e posteriormente, Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1 e posteriormente, e está ativado por padrão.

A amostragem de taxa fixa é uma característica do SDK nas versões ASP.NET a partir de 2.0.0 e java sdk versão 2.0.1 e em diante.

Antes do v2.5.0-beta2 do ASP.NET SDK, e v2.2.0-beta3 de ASP.NET Core SDK, a decisão de amostragem baseou-se no hash do ID do utilizador para aplicações que definem "utilizador" (isto é, aplicações web mais típicas). Para os tipos de aplicações que não definiram os utilizadores (como serviços web), a decisão de amostragem baseou-se na identificação de operação do pedido. Versões recentes dos ASP.NET e ASP.NET Os SDKs core utilizam o ID de operação para a decisão de amostragem.

## <a name="next-steps"></a>Passos Seguintes

* [A filtragem](../../azure-monitor/app/api-filtering-sampling.md) pode fornecer um controlo mais rigoroso do que o seu SDK envia.
* Leia o artigo da Rede de Desenvolvedores [Otimize Telemetria com Insights de Aplicação](https://msdn.microsoft.com/magazine/mt808502.aspx).
