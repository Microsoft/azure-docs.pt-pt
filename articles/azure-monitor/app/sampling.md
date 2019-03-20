---
title: Amostragem de telemetria no Application Insights do Azure | Documentos da Microsoft
description: Como manter o volume de telemetria sob controle.
services: application-insights
documentationcenter: windows
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.reviewer: vitalyg
ms.author: cithomas
ms.openlocfilehash: 83c286be6429376d4d0b4009b18c5f751a4b158f
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226696"
---
# <a name="sampling-in-application-insights"></a>Amostragem no Application Insights

A amostragem é uma funcionalidade do [do Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). É a forma recomendada para reduzir o tráfego de telemetria e de armazenamento, preservando uma análise estatística correta dos dados de aplicação. O filtro seleciona itens relacionados, para que pode navegar entre os itens quando estão a fazer investigações de diagnóstico.
Quando as contagens de métricas são apresentadas no portal, eles são renormalized levar em amostragem de conta. Se o fizer, minimiza qualquer efeito sobre as estatísticas.

Amostragem reduz os custos de tráfego e os dados e ajuda a evitar a limitação.

## <a name="in-brief"></a>Em Resumo:

* Amostragem mantém 1 na *n* regista e descarta o resto. Por exemplo, ele poderá manter uma em cinco eventos, uma taxa de amostragem de 20%. 
* Amostragem adaptável está ativada por predefinição na versão mais recente do ASP.NET e Kits de desenvolvimento de Software (SDKs) do ASP.NET Core.
* Também pode definir manualmente de amostragem. Isso pode ser configurado no portal sobre o *utilização e a página de custos estimados*, no SDK do ASP.NET no ficheiro applicationinsights. config, no SDK de núcleo do ASP.NET por meio do código ou no SDK do Java no applicationinsights. XML ficheiro.
* Se iniciar eventos personalizados e a necessidade de garantir que um conjunto de eventos é mantido ou descartado em conjunto, os eventos têm de ter o mesmo valor de OperationId.
* O divisor de amostragem *n* é comunicada em cada registo na propriedade `itemCount`, que na pesquisa aparece sob o nome amigável "contagem de pedidos" ou "contagem de eventos". `itemCount==1`Quando amostragem não está em operação.
* Se escrever consultas de análise, deve [levar em conta amostragem](../../azure-monitor/log-query/aggregations.md). Em particular, em vez de simplesmente contagem de registos, deve usar `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Tipos de amostragem

Existem três métodos alternativos de amostragem:

* **Amostragem adaptável** se ajusta automaticamente o volume de telemetria enviada do SDK na sua aplicação ASP.NET/ASP.NET Core. Esta é a amostragem predefinida do SDK de Web de ASP.NET v 2.0.0-Beta3 ou posterior e 2.2.0-beta1 e posteriores do SDK de Microsoft.ApplicationInsights.AspNetCore v.  Amostragem adaptável está atualmente disponível apenas para a telemetria de lado do servidor do ASP.NET.

* **Amostragem de taxa fixa** reduz o volume de telemetria enviada a partir de ambos os seu servidor ASP.NET ou ASP.NET Core ou Java e de browsers dos seus utilizadores. Definir a taxa. O cliente e o servidor irão sincronizar os seus amostragem, de modo que, a pesquisa em, pode navegar entre as vistas de página relacionado e pedidos.

* **Amostragem de ingestão** funciona no portal do Azure. Ele descartará alguns da telemetria que chega pela sua aplicação, a uma taxa de amostragem que definir. Ele não reduz o tráfego de telemetria enviado pela sua aplicação, mas ajuda a manter-se dentro da sua quota mensal. A principal vantagem de amostragem de ingestão é que pode definir a frequência de amostragem sem Reimplementar a sua aplicação. Amostragem de ingestão funciona de maneira uniforme para todos os servidores e clientes.

Se forem Adaptive ou fixo amostragem de taxa numa operação, a amostragem de ingestão está desativada.


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>Amostragem adaptável em seus aplicativos de Web do núcleo ASP.NET/ASP.NET

Amostragem adaptável está disponível para o Application Insights SDK para 2.0.0-Beta3 v do ASP.NET e posterior, o SDK de Microsoft.ApplicationInsights.AspNetCore v 2.2.0-beta1 e versões posteriores e está ativada por predefinição.

Amostragem adaptável afeta o volume de telemetria enviada pela sua aplicação de servidor web para o ponto de final de serviço do Application Insights. O volume é ajustado automaticamente de manter uma taxa máxima especificada de tráfego e é controlado através da definição `MaxTelemetryItemsPerSecond`. Se o aplicativo produz uma quantidade reduzida de telemetria, como ao depurar ou devido à baixa utilização, itens não obter objeto de amostragem, desde que o volume é abaixo `MaxTelemetryItemsPerSecond`. Como o volume de telemetria aumenta, a taxa de amostragem é ajustada de modo a alcançar o volume de destino.

Para obter o volume de destino, alguns da telemetria gerada é descartado. Mas, como outros tipos de amostragem, o algoritmo mantém os itens de telemetria relacionados. Por exemplo, quando está analisando a telemetria na pesquisa, poderá encontrar o pedido relacionados com uma exceção em particular.

Contagens de métrica, tais como a taxa de pedidos e taxa de exceções são ajustados para compensar a taxa de amostragem, para que mostrem aproximadamente os valores corretos no Explorador de métricas.

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Configuração de amostragem adaptável para aplicativos ASP.NET

[Saiba](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) sobre a configuração de amostragem adaptável para os aplicativos de principais para ASP.NET. 

Na [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), pode ajustar vários parâmetros nos `AdaptiveSamplingTelemetryProcessor` nó. Os números mostrados são os valores predefinidos:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    A taxa de destino que o algoritmo do estímulo Objetiva **em cada anfitrião do servidor**. Se a sua aplicação web é executado em vários anfitriões, reduza este valor de modo a permanecer no seu ritmo de destino de tráfego no portal do Application Insights.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    O intervalo a que a taxa actual de telemetria é reavaliada. Avaliação é executada como uma média móvel. Pode querer diminuir este intervalo, se a telemetria é considerada como picos repentinos.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Quando as alterações de valor de percentagem de amostragem, como em breve afinal são nós permitidos para reduzir a percentagem de amostragem novamente para capturar dados com menos.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Quando as alterações de valor de percentagem de amostragem, como brevemente após são nós permitidos para aumentar a percentagem de amostragem novamente para capturar mais dados.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Como a percentagem de amostragem varia, o que é o valor mínimo que têm permissão para definir.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Como a percentagem de amostragem varia, o que é o que têm permissão para definir o valor máximo.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    O cálculo da média móvel, o peso atribuído para o valor mais recente. Utilize um valor igual ou inferior a 1. Valores menores efetuar o algoritmo de alterações de menos reativas para repentino.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    O valor atribuído quando acabou de iniciar a aplicação. Não reduza o valor enquanto estiver a depurar.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Uma lista delimitada ponto e vírgula de tipos que não pretende ser objeto de amostragem. Tipos reconhecidos são: Dependência, eventos, a exceção, a visualização de página, pedido, de rastreio. Todas as instâncias dos tipos especificados são transmitidas; os tipos que não forem especificados são objeto de amostragem.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Uma lista delimitada ponto e vírgula de tipos que deseja ser objeto de amostragem. Tipos reconhecidos são: Dependência, eventos, a exceção, a visualização de página, pedido, de rastreio. Tipos especificados são objeto de amostragem; todas as instâncias dos outros tipos de sempre serão transmitidas.


**Para optar por desativar** adaptável de amostragem, remover o nó de AdaptiveSamplingTelemetryProcessor (s) a partir da configuração do Application insights.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativa: Configurar amostragem adaptável no código

Em vez de definir o parâmetro de amostragem no arquivo. config, pode definir programaticamente estes valores.

1. Remova todos os `AdaptiveSamplingTelemetryProcessor` nó (s) do arquivo. config.
2. Utilize o fragmento seguinte para configurar a amostragem adaptável.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Saiba mais sobre os processadores de telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Pode também ajustar a frequência de amostragem para cada tipo de telemetria individualmente ou pode até mesmo impedir determinados tipos de ser divididos em amostras de todo. 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Configuração de amostragem adaptável para aplicativos do ASP.NET Core.

Não existe nenhum `ApplicationInsights.Config` para aplicativos do ASP.NET Core, portanto, cada configuração é feita por meio do código.
Amostragem adaptável está ativada por predefinição para todos os aplicativos do ASP.NET Core. Pode desativar ou personalizar o comportamento de amostragem.

### <a name="turning-off-adaptive-sampling"></a>Desativar a amostragem adaptável

A funcionalidade de amostragem padrão pode ser desabilitada ao adicionar o serviço do Application Insights, no método ```ConfigureServices```, utilizando ```ApplicationInsightsServiceOptions```:

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

O código acima irá desativar a funcionalidade de amostragem. Siga os passos abaixo para adicionar a amostragem com mais opções de personalização.

### <a name="configure-sampling-settings"></a>Configurar as definições de amostragem

Utilizar métodos de extensão de ```TelemetryProcessorChainBuilder``` conforme mostrado abaixo para personalizar o comportamento de amostragem.

> [!IMPORTANT]
> Se usar esse método para configurar a amostragem, certifique-se de usar aiOptions.EnableAdaptiveSampling = false; definições com AddApplicationInsightsTelemetry().

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

    var builder = configuration .TelemetryProcessorChainBuilder;
    // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
    // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    builder.Build();

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    // ...
}

```

**Se utilizar o método acima para configurar a amostragem, certifique-se de usar ```aiOptions.EnableAdaptiveSampling = false;``` definições com AddApplicationInsightsTelemetry().**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-and-java-websites"></a>Amostragem de taxa fixa para Web sites do ASP.NET, ASP.NET Core e Java

Taxa fixa amostragem reduz o tráfego enviado a partir do servidor web e navegadores da web. Ao contrário de amostragem adaptável, reduz a telemetria a um preço fixo decidido por. Ele também sincroniza o cliente e amostragem de servidor para que os itens relacionados são retidos - por exemplo, quando examinar uma vista de página na pesquisa, pode encontrar a solicitação relacionada.

Como outras técnicas de amostragem, isso também mantém os itens relacionados. Para cada solicitação HTTP eventos, o pedido e seus eventos relacionados são eliminados ou transmitidos em conjunto.

No Explorador de métricas, tarifas, tais como contagens de pedido e exceção são multiplicadas por um fator para compensar a taxa de amostragem, para que fiquem aproximadamente corretos.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Configurar taxa fixa amostragem no ASP.NET

1. **Desativar a amostragem adaptável**: Na [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), remover ou comentar o `AdaptiveSamplingTelemetryProcessor` nó.

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **Ative o módulo de amostragem de taxa fixa.** Adicione este fragmento de código para [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternativa: Ativar a amostragem de taxa fixa no código de servidor
    
    Em vez de definir o parâmetro de amostragem no arquivo. config, pode definir programaticamente estes valores. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```
([Saiba mais sobre os processadores de telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>Configurar taxa fixa amostragem no ASP.NET Core

1. **Desativar a amostragem adaptável**:  Podem ser feitas alterações no método ```ConfigureServices```, utilizando ```ApplicationInsightsServiceOptions```:

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

2. **Ative o módulo de amostragem de taxa fixa.** Podem ser feitas alterações no método ```Configure``` takto no trecho de código:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.TelemetryProcessorChainBuilder;
        // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
        // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }

    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>Configurar a amostragem de taxa fixa em JAVA ###

1. Transferir e configurar a sua aplicação web com a versão mais recente [SDK de java do application insights](../../azure-monitor/app/java-get-started.md)

2. **Ativar o módulo de amostragem de taxa fixa** adicionando o fragmento seguinte ao ficheiro Applicationinsights XML.

    ```XML
        <TelemetryProcessors>
            <BuiltInProcessors>
                <Processor type = "FixedRateSamplingTelemetryProcessor">
                    <!-- Set a percentage close to 100/N where N is an integer. -->
                    <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                    <Add name = "SamplingPercentage" value = "50" />
                </Processor>
            </BuiltInProcessors>
        <TelemetryProcessors/>
    ```

3. Pode incluir ou excluir determinados tipos de telemetria de amostragem usando as seguintes etiquetas dentro da marca de processador "FixedRateSamplingTelemetryProcessor"
    ```XML
        <ExcludedTypes>
            <ExcludedType>Request</ExcludedType>
        </ExcludedTypes>

        <IncludedTypes>
            <IncludedType>Exception</IncludedType>
        </IncludedTypes>
    ```

Os tipos de telemetria que podem ser incluídos ou excluídos da amostragem são: Dependência, eventos, exceções, visualização de página, pedido e rastreio.

> [!NOTE]
> Para a percentagem de amostragem, escolha uma percentagem que esteja próxima 100/N, onde N é um número inteiro.  Amostragem atualmente não suporta outros valores.
> 
> 

<a name="other-web-pages"></a>



## <a name="ingestion-sampling"></a>Amostragem de ingestão

Essa forma de amostragem opera no ponto onde a telemetria do seu servidor web, navegadores e dispositivos atinge o ponto de extremidade de serviço do Application Insights. Embora ele não reduz o tráfego de telemetria enviado pela sua aplicação, a reduzir a quantidade processados e mantidos (e é cobrada) pelo Application Insights.

Utilize este tipo de amostragem, se a sua aplicação, muitas vezes, são enviados pela sua quota mensal e não tem a opção de utilizar qualquer um dos tipos com base no SDK de amostragem. 

Defina a frequência de amostragem na utilização e a página de custos estimados:

![No painel de descrição geral da aplicação, clique em definições, Quota, amostras, em seguida, em seguida, selecione uma frequência de amostragem e clique em Atualizar.](./media/sampling/04.png)

Como outros tipos de amostragem, o algoritmo mantém os itens de telemetria relacionados. Por exemplo, quando está analisando a telemetria na pesquisa, poderá encontrar o pedido relacionados com uma exceção em particular. Contagens de métrica, tais como a taxa de pedidos e taxa de exceções são mantidos corretamente.

Pontos de dados que são rejeitados pelo amostragem como não estão disponíveis em qualquer recurso do Application Insights [exportação contínua](../../azure-monitor/app/export-telemetry.md).

Amostragem de ingestão não funciona enquanto amostragem adaptável ou tarifa fixa baseada no SDK está em operação. Amostragem adaptável está ativada por predefinição, quando ASP.NET/ASP.NET Core SDK está ativado no Visual Studio ou ativado nas extensões de aplicação Web do Azure ou com o Monitor de estado e amostragem de ingestão está desativada. Se a taxa de amostragem, o SDK for inferior a 100% (ou seja itens estão a ser amostrados), em seguida, a taxa de amostragem de ingestão que definir é ignorada.

> [!WARNING]
> O valor mostrado no mosaico indica o valor que definiu para a amostragem de ingestão. Ele não representar a frequência de amostragem real se amostragem do SDK está em operação.
>
>
## <a name="sampling-for-web-pages-with-javascript"></a>Amostragem para páginas da web com JavaScript
Pode configurar páginas da web para a amostragem de taxa fixa a partir de qualquer servidor. 

Quando [configurar as páginas da web do Application Insights](../../azure-monitor/app/javascript.md), modificar o fragmento do JavaScript que a partir do portal do Application Insights. (Em aplicativos do ASP.NET, o fragmento normalmente vai no layout. cshtml.)  Insira uma linha, como `samplingPercentage: 10,` antes da chave de instrumentação:

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

Para a percentagem de amostragem, escolha uma percentagem que esteja próxima 100/N, onde N é um número inteiro.  Amostragem atualmente não suporta outros valores.

Se também ativar a amostragem de taxa fixa no servidor, os clientes e o servidor irão sincronizar, de modo que, a pesquisa em, pode navegar entre as vistas de página relacionado e pedidos.

## <a name="when-to-use-sampling"></a>Quando utilizar amostragem?

Amostragem adaptável está automaticamente habilitada no .NET e .NET Core SDKs mais recentes. Independentemente de qual versão do SDK do que utilizar, pode ativar a amostragem de ingestão permitir que o Application Insights para os dados recolhidos de exemplo.

Por predefinição, nenhuma amostragem está ativada no SDK de Java. Atualmente, apenas suporta fixo de taxa de amostragem. Amostragem adaptável não é suportada no SDK de Java.

Em geral, para a maioria dos aplicativos de tamanho de pequeno e médio não precisa amostragem. As informações de diagnóstico mais úteis e estatísticas mais precisas são obtidas através da recolha de dados em todas as suas atividades de utilizador. 

São as principais vantagens da amostragem:

* Intervalo de tempo do Application Insights serviço cai ("limitadores") pontos de dados quando a aplicação envia uma taxa muito elevada de telemetria em Resumo. 
* Para manter o [quota](../../azure-monitor/app/pricing.md) de pontos de dados para o escalão de preço. 
* Para reduzir o tráfego de rede da coleção de telemetria. 

### <a name="which-type-of-sampling-should-i-use"></a>Que tipo de amostragem devo utilizar?

**Utilize a amostragem de ingestão se:**

* Muitas vezes passam por meio da sua quota mensal de telemetria.
* Está a utilizar uma versão do SDK que não suporta a amostragem - por exemplo, ASP.NET versões anteriores ao 2.
* Estamos a preparar muita telemetria a partir de browsers dos seus utilizadores.

**Utilize a tarifa fixa de amostragem se:**

* Está a utilizar o Application Insights SDK para a versão 2.0.0 do ASP.NET web services ou posterior ou o SDK de Java v2.0.1 ou posterior, e
* Pretende que a amostragem sincronizados entre cliente e servidor, para que, quando está investigando eventos na [pesquisa](../../azure-monitor/app/diagnostic-search.md), pode navegar entre os eventos relacionados no cliente e servidor, como vistas de página e solicitações http.
* Tiver a certeza de que a percentagem de amostragem apropriado para a sua aplicação. Deve ser alto o suficiente para obter uma métrica precisa, mas abaixo a taxa que excede a quota de preços e os limites de limitação. 

**Utilize a amostragem adaptável:**

Se não aplicar as condições para utilizar as outras formas de amostragem, recomendamos que amostragem adaptável. Esta definição está ativada por predefinição no servidor básico de ASP.NET/ASP.NET SDK. Não reduzir o tráfego até que uma taxa mínima é atingida, por conseguinte, sites de baixa utilização não serão afetados.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Como posso saber se a amostragem está em operação?

Para detetar a frequência de amostragem real, independentemente de onde ele foi aplicado, use um [consulta do Analytics](../../azure-monitor/app/analytics.md) como este:

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Se RetainedPercentage para qualquer tipo for inferior a 100, em seguida, esse item está a ser objeto de amostragem.

**O Application Insights não de exemplo sessão, métricas e tipos de telemetria em qualquer descritos acima de técnicas de amostragem de contadores de desempenho. Esses tipos são sempre excluídos da amostragem como redução na precisão pode ser altamente desejável para estes tipos de telemetria**

## <a name="how-does-sampling-work"></a>Como funciona a amostragem?

Recurso de amostragem de taxa fixa do SDK em versões do ASP.NET do 2.0.0 e o SDK de Java versão 2.0.1 e e posteriores. Amostragem adaptável é uma funcionalidade do SDK em versões do ASP.NET do 2.0.0 e posteriores. Amostragem de ingestão é uma funcionalidade do serviço do Application Insights e pode ser numa operação, se o SDK não está a efetuar amostragem.

O algoritmo de amostragem decide quais itens de telemetria ao largar e quais podem manter (seja no SDK ou no serviço do Application Insights). A decisão de amostragem baseia-se a várias regras que têm como objetivo para preservar a todos os pontos de dados inter-relacionados intactos, manter uma experiência de diagnóstico no Application Insights, que é fiável, mesmo com um conjunto de dados reduzido e acionáveis. Por exemplo, se de um pedido falhado a sua aplicação enviar itens de telemetria adicional (como exceções e rastreios com sessão iniciados neste pedido), amostragem não divide este pedido e outra telemetria. Ele mantém ou remove-las todas as ferramentas. Como resultado, quando examinar os detalhes de pedido no Application Insights, pode sempre ver o pedido, juntamente com seus itens de telemetria associados. 

A decisão de amostragem baseia-se o ID da operação de solicitação, o que significa que todos os itens de telemetria que pertencem a uma determinada operação é preservado ou removida. Para os itens de telemetria que não têm operação amostragem de conjunto (para itens de telemetria de exemplo reportada a partir de threads assíncronas com nenhum contexto de http) do ID simplesmente captura uma percentagem de itens de telemetria de cada tipo. Antes de 2.5.0-beta2 do SDK do .NET e 2.2.0-beta3 do ASP.NET Core SDK, a decisão de amostragem tiveram como base o hash do ID de utilizador para aplicações que definir "user" (ou seja, aplicativos de web mais comuns). Para os tipos de aplicativos que não definem utilizadores (tais como serviços da web) a decisão de amostragem foi com base no ID da operação do pedido.

Ao apresentar a telemetria é com, o serviço Application Insights ajusta as métricas pela percentagem de amostragem mesmo que foi utilizada no momento da coleção, para compensar os pontos de dados em falta. Por conseguinte, ao ver a telemetria no Application Insights, os utilizadores estão a ver estatística corretas aproximações estão muito perto os números de real.

A precisão da aproximação depende em grande parte a percentagem de amostragem configurado. Além disso, a precisão aumenta para aplicações que processam um grande volume de pedidos em geral semelhantes de muitos usuários. Por outro lado, para aplicações que não funcionam com uma carga significativa, amostragem não é necessária como esses aplicativos geralmente podem enviar toda a sua telemetria, permanecendo dentro da quota, sem causar perda de dados de limitação. 

> [!WARNING]
> O Application Insights não de exemplo tipos de telemetria de métricas e sessões. Redução na precisão pode ser altamente desejável para estes tipos de telemetria.
> 

### <a name="adaptive-sampling"></a>Amostragem adaptável

Amostragem adaptável adiciona um componente que monitoriza a taxa actual de transmissão do SDK e ajusta a percentagem de amostragem para experimentar para se manterem dentro a velocidade máxima de destino. O ajuste é recalculado em intervalos regulares e se baseia numa média móvel da taxa de transmissão de saída.

## <a name="sampling-and-the-javascript-sdk"></a>Amostragem e o JavaScript SDK

O lado do cliente (JavaScript) SDK participa de amostragem de taxa fixa em conjunto com o SDK do lado do servidor. As páginas instrumentadas só irão enviar telemetria do lado do cliente dos mesmos utilizadores para o qual o servidor feitas a sua decisão de "exemplo em". Essa lógica foi concebida para manter a integridade da sessão de utilizador nos lados cliente e servidor. Como resultado, a partir de qualquer item de telemetria específico no Application Insights pode encontrar todos os outros itens de telemetria para este utilizador ou a sessão. 

*Meu cliente e a telemetria do lado do servidor não mostram exemplos coordenados como descrito acima.*

* Certifique-se de que ativou a amostragem de taxa fixa no servidor e cliente.
* Certifique-se de que a versão do SDK 2.0 ou superior.
* Verifique que defina a percentagem de amostragem mesmo no cliente e servidor.

## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

*O que é o comportamento de amostragem padrão em ASP.NET e ASP.NET Core SDK?*

* Se estiver a utilizar uma das versões mais recentes do SDK acima, a amostragem adaptável está ativada por predefinição, com cinco itens de telemetria por segundo.
  Existem 2 AdaptiveSamplingTelemetryProcessors adicionados por padrão, e uma inclui o tipo de evento em amostragem para o outro exclui o tipo de evento de amostragem. Esta configuração significa que o SDK tentará limitar os itens de telemetria para cinco itens de telemetria de tipos de eventos e cinco itens de telemetria de todos os outros tipos de combinado, assegurando que eventos servem como amostra em separado dos outros tipos de telemetria. Eventos são normalmente utilizados para a telemetria de negócios e provavelmente não devem ser afetados por volumes de telemetria de diagnóstico.
  
  O seguinte mostra o ficheiro de applicationinsights. config predefinido gerado. Conforme descrito, existem dois nós separados do AdaptiveSamplingTelemetryProcessor adicionado, uma excluir tipos de eventos e outro incluí-lo. ASP.NET Core, mesmo comportamento de padrão exata está ativado no código. Utilize os exemplos na seção anterior do documento para alterar este comportamento predefinido.

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

*Telemetria pode ser convertida mais de uma vez?*

* Não. SamplingTelemetryProcessors ignorar itens de considerações de amostragem, se o item já é objeto de amostragem. O mesmo vale para a amostragem de ingestão como bem, que não se aplicam amostragem para esses itens já amostragem no SDK do próprio. "

*Por que não é de amostragem um simples "recolher X por cento de cada tipo de telemetria"?*

* Embora essa abordagem de amostragem forneceria com um elevado nível de precisão na métrica aproximações, iria quebrar a capacidade para correlacionar dados de diagnóstico por usuário, sessão e pedido, o que é essencial para obter um diagnóstico. Por conseguinte, a amostragem de funciona melhor com "recolher todos os telemetria itens para X por cento dos utilizadores da aplicação" ou "recolher toda a telemetria para X por cento dos pedidos de aplicação" lógica. Para os itens de telemetria não associados a pedidos (por exemplo, o processamento assíncrono em segundo plano), a desvantagem é "recolher X por cento de todos os itens para cada tipo de telemetria." 

*Pode alterar a percentagem de amostragem ao longo do tempo?*

* Sim, amostragem adaptável altera gradualmente a percentagem de amostragem, com base no volume de telemetria enfrentado atualmente.

*Se eu utilizar amostragem de taxa fixa, como posso saber qual amostragem percentagem funcionará melhor para a minha aplicação?*

* Uma forma é começar com amostragem, Descubra o que classificá-la liquida (consulte a pergunta acima) e, em seguida, mude para a taxa fixa de amostragem com essa taxa. 
  
    Caso contrário, é preciso adivinhar. Analisar a utilização atual de telemetria no Application Insights, observar qualquer limitação que está a ocorrer e estimar o volume de telemetria recolhido. Estes três entradas, juntamente com o escalão de preço selecionado, sugerem quanto poderá reduzir o volume de telemetria recolhido. No entanto, um aumento no número de utilizadores ou alguns outro shift do volume de telemetria pode invalidar sua estimativa.

*O que acontece se eu configurar a percentagem de amostragem demasiado baixa?*

* Percentagem de amostragem excessivamente baixa (demasiado agressivas amostragem) reduz a precisão das aproximações, quando tenta do Application Insights compensar a visualização dos dados para a redução de volume de dados. Além disso, experiência de diagnóstico pode ser afetada negativamente, como alguns dos pedidos com pouca frequência falhar ou lentos podem ser objeto de amostragem horizontalmente.

*O que acontece se eu configurar a percentagem de amostragem muito alta?*

* Configurar a percentagem de amostragem demasiado elevada (não agressiva suficiente) resulta numa redução de insuficiente no volume de telemetria recolhido. Ainda pode ocorrer perda de dados de telemetria relacionados com a limitação e o custo de utilização do Application Insights pode ser maior do que planeada devido a custos de utilização excedida.

*Em que plataformas posso utilizar amostragem?*

* Amostragem de ingestão pode ocorrer automaticamente para qualquer telemetria acima de um determinado volume, se o SDK não está a efetuar amostragem. Esta configuração funciona, por exemplo, se estiver a utilizar uma versão mais antiga do SDK do ASP.NET ou versão anterior do Java SDK(1.0.10 or before).
* Se estiver a utilizar versões do ASP.NET SDK 2.0.0 e superior ou ASP.NET CORE SDK versão 2.2.0 e superior (alojada no Azure ou no seu próprio servidor), obterá adaptável de amostragem por predefinição, mas pode mudar para a taxa fixa, tal como descrito acima. Com a amostragem de taxa fixa, o navegador SDK sincroniza automaticamente para a amostragem de eventos relacionados. 
* Se estiver a utilizar o SDK de Java versão 2.0.1 ou superior, pode configurar o applicationinsights. XML para ativar a amostragem de taxa fixa. Amostragem está desativada por predefinição. Com a amostragem de taxa fixa, o navegador SDK sincroniza automaticamente para a amostragem de eventos relacionados.

*Existem determinados eventos raros, que quero ver sempre. Como posso obtê-los após o módulo de amostragem?*

* Inicialize uma instância separada do TelemetryClient com um novo TelemetryConfiguration (não a predefinição ativa). Usá-lo para enviar seus eventos raros.

## <a name="next-steps"></a>Passos Seguintes

* [Filtragem](../../azure-monitor/app/api-filtering-sampling.md) pode fornecer mais rigoroso controle de que o SDK envia.