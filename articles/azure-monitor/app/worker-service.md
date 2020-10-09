---
title: Insights de Aplicação para aplicações de Serviço de Trabalhadores (aplicações não-HTTP)
description: Monitorização de .NET Core/.NET Aplicações não-HTTP com Insights de Aplicação do Monitor Azure.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.openlocfilehash: 8156541a5b04a5db5f2ce683fd0e514c81e8b53e
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91840409"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Pedidos de informação para aplicações de serviços de trabalhadores (aplicações não-HTTP)

[Application Insights SDK for Worker Service](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) é um novo SDK que é mais adequado para cargas de trabalho não-HTTP como mensagens, tarefas de fundo, aplicações de consola, etc. Estes tipos de aplicações não têm a noção de um pedido HTTP de entrada como uma aplicação web core ASP.NET/ASP.NET tradicional, e, portanto, usar pacotes de Insights de Aplicação para [ASP.NET](asp-net.md) ou ASP.NET aplicações [Core](asp-net-core.md) não são suportadas.

O novo SDK não faz qualquer coleção de telemetria por si só. Em vez disso, traz outros colecionadores de automóveis bem conhecidos da Application Insights como [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [PerfCounterCollector,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) [ApplicationInsightsLoggingProvider,](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) etc. Este SDK expõe métodos de extensão `IServiceCollection` para permitir e configurar a recolha de telemetria.

## <a name="supported-scenarios"></a>Cenários suportados

O [Application Insights SDK for Worker Service](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) é o mais adequado para aplicações não-HTTP, independentemente de onde ou como funcionam. Se a sua aplicação estiver em execução e tiver conectividade de rede com a Azure, a telemetria pode ser recolhida. A monitorização de Insights de Aplicação é suportada em todos os lugares .NET Core é suportado. Este pacote pode ser utilizado no recém-introduzido [Serviço de Trabalhadores .NET Core 3.0,](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances) [tarefas de fundo em Asp.Net Core 2.1/2.2,](/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&preserve-view=true)Apps de consola (.NET Core/ .NET Framework), etc.

## <a name="prerequisites"></a>Pré-requisitos

Uma chave de instrumentação válida de Insights de Aplicação. Esta chave é necessária para enviar qualquer telemetria para Insights de Aplicação. Se precisar de criar um novo recurso Application Insights para obter uma chave de instrumentação, consulte [Criar um recurso Application Insights](./create-new-resource.md).

## <a name="using-application-insights-sdk-for-worker-services"></a>Utilização de Insights de Aplicação SDK para serviços de trabalhadores

1. Instale o pacote [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) para a aplicação.
   O seguinte corte mostra as alterações que precisam de ser adicionadas ao ficheiro do seu `.csproj` projeto.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. Ligue `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` o método de extensão, `IServiceCollection` fornecendo a chave de instrumentação. Este método deve ser chamado no início da aplicação. A localização exata depende do tipo de aplicação.

1. Recupere uma `ILogger` instância ou instância do recipiente de injeção de dependência `TelemetryClient` (DI) ligando `serviceProvider.GetRequiredService<TelemetryClient>();` ou usando a injeção de construtor. Este passo irá desencadear a criação de módulos de recolha de automóveis e de recolha de `TelemetryConfiguration` automóveis.

Instruções específicas para cada tipo de aplicação são descritas nas seguintes secções.

## <a name="net-core-30-worker-service-application"></a>.NET Core 3.0 pedido de serviço de trabalhador

O exemplo completo é partilhado [aqui](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)

1. Baixar e instalar [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Crie um novo projeto de Serviço dos Trabalhadores, utilizando o novo modelo de projeto ou linha de comando do Visual Studio `dotnet new worker`
3. Instale o pacote [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) para a aplicação.

4. Adicione `services.AddApplicationInsightsTelemetryWorkerService();` ao método da sua `CreateHostBuilder()` `Program.cs` classe, como neste exemplo:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Modifique o seu `Worker.cs` exemplo por exemplo abaixo.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. Configurar a chave de instrumentação.

    Embora possa fornecer a chave de instrumentação como argumento para `AddApplicationInsightsTelemetryWorkerService` , recomendamos que especifique a chave de instrumentação na configuração. A amostra de código que se segue mostra como especificar uma chave de instrumentação em `appsettings.json` . Certifique-se de que `appsettings.json` é copiado para a pasta raiz da aplicação durante a publicação.

```json
    {
        "ApplicationInsights":
            {
            "InstrumentationKey": "putinstrumentationkeyhere"
            },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

Em alternativa, especifique a chave de instrumentação em qualquer uma das seguintes variáveis ambientais.
`APPINSIGHTS_INSTRUMENTATIONKEY` ou `ApplicationInsights:InstrumentationKey`

Por exemplo: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
OU `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Tipicamente, `APPINSIGHTS_INSTRUMENTATIONKEY` especifica a chave de instrumentação para aplicações implementadas para Web Apps como Web Jobs.

> [!NOTE]
> Uma chave de instrumentação especificada em código vence a variável `APPINSIGHTS_INSTRUMENTATIONKEY` ambiental, que vence outras opções.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET Tarefas de fundo do núcleo com serviços hospedados

[Este](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio&view=aspnetcore-2.2&preserve-view=true) documento descreve como criar tarefas de fundo na aplica ASP.NET ção Core 2.1/2.2.

O exemplo completo é partilhado [aqui](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)

1. Instale o pacote [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) para a aplicação.
2. Adicione `services.AddApplicationInsightsTelemetryWorkerService();` ao `ConfigureServices()` método, como neste exemplo:

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

Segue-se o código para `TimedHostedService` onde reside a lógica de tarefa de fundo.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = httpClient.GetAsync("https://bing.com").GetAwaiter().GetResult();
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                _telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. Configurar a chave de instrumentação.
   Utilize o mesmo `appsettings.json` a partir do exemplo do Serviço de Trabalhador .NET Core 3.0 acima.

## <a name="net-corenet-framework-console-application"></a>.NET Core/.NET Aplicação da consola-quadro

Como mencionado no início deste artigo, o novo pacote pode ser usado para ativar a Telemetria Application Insights a partir de uma aplicação regular de consola. Este pacote tem como [`NetStandard2.0`](/dotnet/standard/net-standard) alvos , e assim pode ser utilizado para aplicações de consolas em .NET Core 2.0 ou superior, e .NET Framework 4.7.2 ou superior.

O exemplo completo é partilhado [aqui](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)

1. Instale o pacote [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) para a aplicação.

2. Modifique Program.cs como exemplo abaixo.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

Esta aplicação de consola também utiliza o mesmo padrão `TelemetryConfiguration` – e pode ser personalizada da mesma forma que os exemplos na secção anterior.

## <a name="run-your-application"></a>Executar a aplicação

Executar a sua candidatura. O exemplo dos trabalhadores de todos os acima faz uma chamada http a cada segundo para bing.com, e também emite poucos registos usando `ILogger` . Estas linhas estão embrulhadas no `StartOperation` interior da `TelemetryClient` chamada, que é usada para criar uma operação (neste exemplo `RequestTelemetry` chamado "operação"). Os Insights de Aplicação recolherão estes registos ILogger (aviso ou acima por defeito) e dependências, e serão correlacionados com a `RequestTelemetry` relação pai-filho. A correlação também funciona entre o processo/fronteira da rede. Por exemplo, se a chamada foi feita para outro componente monitorizado, então também será correlacionado com este progenitor.

Esta operação personalizada `RequestTelemetry` pode ser considerada como o equivalente a um pedido web de entrada numa aplicação web típica. Embora não seja necessário utilizar uma Operação, encaixa-se melhor com o [modelo de dados de correlação application Insights](./correlation.md) - com a `RequestTelemetry` atuação como a operação principal, e cada telemetria gerada dentro da iteração do trabalhador sendo tratada como logicamente pertencente à mesma operação. Esta abordagem também garante que toda a telemetria gerada (automática e manual) terá a mesma `operation_id` . Como a amostragem é baseada `operation_id` em , o algoritmo de amostragem mantém ou deixa cair toda a telemetria de uma única iteração.

As seguintes listas listam a telemetria completa recolhida automaticamente pela Application Insights.

### <a name="live-metrics"></a>Live Metrics

[As Métricas Vivas](./live-stream.md) podem ser utilizadas para verificar rapidamente se a monitorização do Application Insights está configurada corretamente. Embora possa demorar alguns minutos até que a telemetria comece a aparecer no portal e analítico, as Métricas Ao Vivo mostrariam o uso do CPU do processo de execução em quase tempo real. Também pode mostrar outras telemetrias como Pedidos, Dependências, Vestígios, etc.

### <a name="ilogger-logs"></a>Troncos ILogger

Os registos emitidos através `ILogger` de severidade `Warning` ou maior são capturados automaticamente. Siga [os docs ILogger](ilogger.md#control-logging-level) para personalizar quais os níveis de registo capturados pela Application Insights.

### <a name="dependencies"></a>Dependências

A recolha de dependência é ativada por padrão. [Este](asp-net-dependencies.md#automatically-tracked-dependencies) artigo explica as dependências que são recolhidas automaticamente, e também contém passos para fazer o rastreio manual.

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` é ativado por padrão, e irá recolher um conjunto padrão de contadores a partir de aplicações .NET Core 3.0. O tutorial [do EventCounter](eventcounters.md) lista o conjunto predefinido de contadores recolhidos. Também tem instruções para personalizar a lista.

### <a name="manually-tracking-additional-telemetry"></a>Rastreio manual de telemetria adicional

Embora o SDK recolha automaticamente a telemetria como explicado acima, na maioria dos casos o utilizador terá de enviar telemetria adicional para o serviço Application Insights. A forma recomendada de rastrear a telemetria adicional é obtendo um exemplo `TelemetryClient` de injeção de dependência e, em seguida, chamando um dos `TrackXXX()` métodos de [API](api-custom-events-metrics.md) suportados. Outro caso de uso típico é [o rastreio personalizado das operações.](custom-operations-tracking.md) Esta abordagem é demonstrada nos exemplos do Trabalhador acima referidos.

## <a name="configure-the-application-insights-sdk"></a>Configure a Aplicação Insights SDK

O padrão `TelemetryConfiguration` utilizado pelo serviço de trabalho SDK é semelhante à configuração automática utilizada numa aplicação core ASP.NET ou ASP.NET, menos os TelemetriaInitializadores utilizados para enriquecer a telemetria a partir de `HttpContext` .

Pode personalizar o SDK Application Insights para o Serviço de Trabalhadores para alterar a configuração padrão. Os utilizadores do Application Insights ASP.NET Core SDK podem estar familiarizados com a alteração da configuração utilizando ASP.NET injeção de [dependência](/aspnet/core/fundamentals/dependency-injection)incorporada do Core . O WorkerService SDK também se baseia em princípios semelhantes. Faça quase todas as alterações de configuração na `ConfigureServices()` secção, invocando métodos `IServiceCollection` apropriados, conforme descrito abaixo.

> [!NOTE]
> Ao utilizar este SDK, alterar a configuração modificando `TelemetryConfiguration.Active` não é suportado, e as alterações não serão refletidas.

### <a name="using-applicationinsightsserviceoptions"></a>Utilização de AplicaçõesInsightsServiceOptions

Pode modificar algumas configurações comuns passando `ApplicationInsightsServiceOptions` para , como neste `AddApplicationInsightsTelemetryWorkerService` exemplo:

```csharp
using Microsoft.ApplicationInsights.WorkerService;

public void ConfigureServices(IServiceCollection services)
{
    var aiOptions = new ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
}
```

Note que `ApplicationInsightsServiceOptions` neste SDK está no espaço de `Microsoft.ApplicationInsights.WorkerService` nomes em oposição `Microsoft.ApplicationInsights.AspNetCore.Extensions` ao ASP.NET Core SDK.

Configurações comumente usadas em `ApplicationInsightsServiceOptions`

|Definições | Descrição | Predefinição
|---------------|-------|-------
|EnableQuickPulseMetricStream | Ativar/Desativar a funcionalidade LiveMetrics | true
|EnableAdaptiveSampling | Permitir/Desativar a amostragem adaptativa | true
|EnableHeartbeat | Ativar/Desativar a funcionalidade Heartbeats, que periodicamente (padrão de 15 min) envia uma métrica personalizada chamada 'HeartBeatState' com informações sobre o tempo de execução como .NET Version, Informações do Ambiente Azure, se aplicável, etc. | true
|AddautoCollectedMetricExtractor | Ativar/Desativar o extrator autoCollectosemétrico, que é um Processor de Telemetria que envia métricas pré-agregadas sobre Pedidos/Dependências antes da recolha de amostragem. | true
|EnableDiagnosticsTelemetryModule | Ativar/Desativar `DiagnosticsTelemetryModule` . Desativar isto fará com que as seguintes definições sejam ignoradas; `EnableHeartbeat`, `EnableAzureInstanceMetadataTelemetryModule`, `EnableAppServicesHeartbeatTelemetryModule` | true

Consulte as [definições `ApplicationInsightsServiceOptions` configuráveis](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) para a lista mais atualizada.

### <a name="sampling"></a>Amostragem

O Application Insights SDK for Worker Service suporta uma amostragem de taxa fixa e adaptativa. A amostragem adaptativa é ativada por defeito. A amostragem pode ser desativada utilizando `EnableAdaptiveSampling` a opção em [ApplicationInsightsServiceOptions](#using-applicationinsightsserviceoptions)

Para configurar configurações de amostragem adicionais, pode utilizar-se o seguinte exemplo.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WorkerService;

public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new ApplicationInsightsServiceOptions();
    
    // Disable adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetryWorkerService(aiOptions);

    // Add Adaptive Sampling with custom settings.
    // the following adds adaptive sampling with 15 items per sec.
    services.Configure<TelemetryConfiguration>((telemetryConfig) =>
        {
            var builder = telemetryConfig.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond: 15);
            builder.Build();
        });
    //...
}
```

Mais informações podem ser encontradas no documento [de amostragem.](#sampling)

### <a name="adding-telemetryinitializers"></a>Adicionar TelemetriaInitializers

Utilize [inicializadores de telemetria](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) quando quiser definir propriedades que são enviadas com toda a telemetria.

Adicione qualquer novidade `TelemetryInitializer` ao recipiente e a `DependencyInjection` SDK irá adicioná-las automaticamente ao `TelemetryConfiguration` .

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Remoção de TelemetriaInitializadores

Os inicializadores de telemetria estão presentes por padrão. Para remover os inicializadores de telemetria todos ou específicos, utilize o seguinte código de amostra *depois de* ligar `AddApplicationInsightsTelemetryWorkerService()` .

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>Adicionar processadores de telemetria

Pode adicionar processadores de telemetria personalizados `TelemetryConfiguration` utilizando o método de extensão em `AddApplicationInsightsTelemetryProcessor` `IServiceCollection` . Utiliza processadores de telemetria em [cenários avançados de filtragem](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer) para permitir um controlo mais direto sobre o que está incluído ou excluído da telemetria que envia para o serviço Application Insights. Use o seguinte exemplo.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configurar ou remover telemetrias padrão

O Application Insights utiliza módulos de telemetria para recolher automaticamente telemetria sobre cargas de trabalho específicas sem necessitar de rastreio manual.

Os seguintes módulos de recolha automática são ativados por predefinição. Estes módulos são responsáveis pela recolha automática de telemetria. Pode desativá-los ou configurá-los para alterar o seu comportamento predefinido.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule` - (Existe atualmente um problema que envolve este módulo de telemetria. Para uma solução temporária consulte [a edição 1689 do GitHub .)](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1689
)
* `AzureInstanceMetadataTelemetryModule`

Para configurar qualquer predefinição, `TelemetryModule` utilize o método de extensão em , como mostra o exemplo `ConfigureTelemetryModule<T>` `IServiceCollection` seguinte.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>Canai de telemetria configurado

O canal predefinido é `ServerTelemetryChannel` . Pode sobrepor-se como mostra o exemplo a seguir.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>Desative a telemetria dinamicamente

Se pretender desativar a telemetria de forma condicional e dinâmica, poderá resolver `TelemetryConfiguration` o caso com ASP.NET recipiente de injeção de dependência do Núcleo em qualquer lugar do seu código e definir `DisableTelemetry` a sua bandeira.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Como posso rastrear a telemetria que não é recolhida automaticamente?

Obtenha uma instância de usar a injeção de `TelemetryClient` construtor e ligue para o método `TrackXXX()` necessário. Não recomendamos a criação de novos `TelemetryClient` casos. Uma instância singleton `TelemetryClient` de já está registada no `DependencyInjection` contentor, que partilha com o resto da `TelemetryConfiguration` telemetria. A criação de um novo `TelemetryClient` caso só é recomendada se precisar de uma configuração separada do resto da telemetria.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Posso utilizar o Visual Studio IDE para embarcar insights de aplicação para um projeto de Serviço de Trabalhadores?

O visual Studio IDE está atualmente suportado apenas para aplicações core ASP.NET/ASP.NET. Este documento será atualizado quando as naves do Visual Studio apoiarem para aplicações de serviço de trabalhadores a bordo.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Posso ativar a monitorização de Insights de Aplicação utilizando ferramentas como o Status Monitor?

Não. [Atualmente, o Monitor](./monitor-performance-live-website-now.md) de Estado e [o Monitor de Estado v2](./status-monitor-v2-overview.md) suportam ASP.NET apenas 4.x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se eu executar a minha candidatura no Linux, todas as funcionalidades são suportadas?

Sim. Suporte de funcionalidades para este SDK é o mesmo em todas as plataformas, com as seguintes exceções:

* Os contadores de desempenho são suportados apenas no Windows, com exceção do CpU/Memória do Processo mostrado em Métricas Vivas.
* Apesar de `ServerTelemetryChannel` estar ativado por padrão, se a aplicação estiver em execução no Linux ou no MacOS, o canal não cria automaticamente uma pasta de armazenamento local para manter a telemetria temporariamente se houver problemas de rede. Devido a esta limitação, a telemetria perde-se quando existem problemas temporários de rede ou servidor. Para contornar esta questão, configuure uma pasta local para o canal:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>Exemplos de aplicações

[Aplicação de consola core .NET](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Utilize esta amostra se estiver a utilizar uma aplicação de consola escrita em .NET Core (2.0 ou superior) ou .NET Framework (4.7.2 ou superior)

[ASP .NET Core tarefas de fundo com Os Serviços Hospedados](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Utilize esta amostra se estiver em Asp.Net Core 2.1/2.2 e criando tarefas de fundo de acordo com a orientação oficial [aqui](/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&preserve-view=true)

[.NET Core 3.0 Serviço de Trabalhadores](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Utilize esta amostra se tiver uma aplicação de Serviço de Trabalhador .NET Core 3.0 de acordo com a orientação oficial [aqui](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio&view=aspnetcore-3.0&preserve-view=true#worker-service-template)

## <a name="open-source-sdk"></a>SDK de código aberto

* [Leia e contribua para o código.](https://github.com/microsoft/ApplicationInsights-dotnet)

Para obter as últimas atualizações e correções de erro [consulte as notas de lançamento](./release-notes.md).

## <a name="next-steps"></a>Passos seguintes

* [Utilize a API](./api-custom-events-metrics.md) para enviar os seus próprios eventos e métricas para uma visão detalhada do desempenho e utilização da sua aplicação.
* [Acompanhe as dependências adicionais não rastreadas automaticamente.](./auto-collect-dependencies.md)
* [Enriqueça ou filtre a telemetria recolhida.](./api-filtering-sampling.md)
* [Injeção de Dependência no Núcleo ASP.NET](/aspnet/core/fundamentals/dependency-injection).
