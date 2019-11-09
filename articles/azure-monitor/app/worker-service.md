---
title: Application Insights para aplicativos de serviço do Worker (aplicativos não HTTP) | Microsoft Docs
description: Monitorando aplicativos .NET Core/. NET Framework não HTTP com Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cijothomas
ms.author: cithomas
ms.date: 09/15/2019
ms.openlocfilehash: 8dd46d8224567e1883fd2a397d5ba2b00a0fd43d
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887286"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Application Insights para aplicativos de serviço de trabalho (aplicativos não HTTP)

O Application Insights está lançando um novo SDK, chamado `Microsoft.ApplicationInsights.WorkerService`, que é mais adequado para cargas de trabalho não HTTP, como mensagens, tarefas em segundo plano, aplicativos de console, etc. Esses tipos de aplicativos não têm a noção de uma solicitação HTTP de entrada como um aplicativo Web ASP.NET/ASP.NET Core tradicional e, portanto, o uso de pacotes de Application Insights para aplicativos [ASP.net](asp-net.md) ou [ASP.NET Core](asp-net-core.md) não é suportado.

O novo SDK não faz nenhuma coleção de telemetria por si só. Em vez disso, ele traz outros bem conhecidos Application Insights coletores automáticos como [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) , etc. Este SDK expõe métodos de extensão no `IServiceCollection` para habilitar e configurar a coleta de telemetria.

## <a name="supported-scenarios"></a>Cenários suportados

O [SDK do Application insights para o serviço de trabalho](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) é mais adequado para aplicativos não http, independentemente de onde ou como eles são executados. Se seu aplicativo estiver em execução e tiver conectividade de rede com o Azure, a telemetria poderá ser coletada. Há suporte para o monitoramento de Application Insights em todos os lugares do .NET Core. Este pacote pode ser usado no [serviço de trabalho do .NET Core 3,0](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances)introduzido recentemente, [tarefas em segundo plano no ASP.NET Core 2.1/2.2](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2), aplicativos de console (.NET Core/.NET Framework), etc.

## <a name="prerequisites"></a>Pré-requisitos

Uma chave de instrumentação de Application Insights válida. Essa chave é necessária para enviar qualquer telemetria para Application Insights. Se você precisar criar um novo recurso de Application Insights para obter uma chave de instrumentação, consulte [criar um recurso de Application insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="using-application-insights-sdk-for-worker-services"></a>Usando Application Insights SDK para serviços de trabalho

1. Instale o pacote [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) no aplicativo.
   O trecho a seguir mostra as alterações que precisam ser adicionadas ao arquivo de `.csproj` do seu projeto.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.8.0" />
    </ItemGroup>
```

1. Chame o método de extensão `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` no `IServiceCollection`, fornecendo a chave de instrumentação. Esse método deve ser chamado no início do aplicativo. O local exato depende do tipo de aplicativo.

1. Recupere uma instância de `ILogger` ou `TelemetryClient` instância do contêiner injeção de dependência (DI) chamando `serviceProvider.GetRequiredService<TelemetryClient>();` ou usando injeção de construtor. Esta etapa irá disparar a configuração de módulos de `TelemetryConfiguration` e coleção automática.

Instruções específicas para cada tipo de aplicativo são descritas nas seções a seguir.

## <a name="net-core-30-worker-service-application"></a>Aplicativo de serviço de trabalho do .NET Core 3,0

O exemplo completo é compartilhado [aqui](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)

1. Baixe e instale o [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Crie um novo projeto de serviço de trabalho usando o modelo de novo projeto ou a linha de comando do Visual Studio `dotnet new worker`
3. Instale o pacote [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) no aplicativo.

4. Adicione `services.AddApplicationInsightsTelemetryWorkerService();` ao método `CreateHostBuilder()` na sua classe `Program.cs`, como neste exemplo:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Modifique seu `Worker.cs` de acordo com o exemplo abaixo.

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

6. Configure a chave de instrumentação.

    Embora seja possível fornecer a chave de instrumentação como um argumento para `AddApplicationInsightsTelemetryWorkerService`, recomendamos que você especifique a chave de instrumentação na configuração. O exemplo de código a seguir mostra como especificar uma chave de instrumentação em `appsettings.json`. Certifique-se de que `appsettings.json` seja copiado para a pasta raiz do aplicativo durante a publicação.

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

Como alternativa, especifique a chave de instrumentação em qualquer uma das seguintes variáveis de ambiente.
`APPINSIGHTS_INSTRUMENTATIONKEY` ou `ApplicationInsights:InstrumentationKey`

Por exemplo: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
OU `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Normalmente, `APPINSIGHTS_INSTRUMENTATIONKEY` especifica a chave de instrumentação para aplicativos implantados em aplicativos Web como trabalhos da Web.

> [!NOTE]
> Uma chave de instrumentação especificada no código vence sobre a variável de ambiente `APPINSIGHTS_INSTRUMENTATIONKEY`, que vence em outras opções.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET Core tarefas em segundo plano com serviços hospedados
[Este](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) documento descreve como criar tarefas de plano de fundo no aplicativo ASP.NET Core 2.1/2.2.

O exemplo completo é compartilhado [aqui](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)

1. Instale o pacote Microsoft. ApplicationInsights. WorkerService (https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) no aplicativo.
2. Adicione `services.AddApplicationInsightsTelemetryWorkerService();` ao método `ConfigureServices()`, como neste exemplo:

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

A seguir está o código para `TimedHostedService` onde a lógica de tarefa em segundo plano reside.

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
                var res = await httpClient.GetAsync("https://bing.com");
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. Configure a chave de instrumentação.
   Use o mesmo `appsettings.json` do exemplo de serviço de trabalho do .NET Core 3,0 acima.

## <a name="net-corenet-framework-console-application"></a>Aplicativo de console .NET Core/. NET Framework

Conforme mencionado no início deste artigo, o novo pacote pode ser usado para habilitar Application Insights Telemetry de mesmo um aplicativo de console regular. Esse pacote tem como destino [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)e, portanto, pode ser usado para aplicativos de console no .net Core 2,0 ou superior e .NET Framework 4.7.2 ou superior.

O exemplo completo é compartilhado [aqui](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)

1. Instale o pacote Microsoft. ApplicationInsights. WorkerService (https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) no aplicativo.

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
                // Hence instrumentation key must be specified here.
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

Esse aplicativo de console também usa o mesmo `TelemetryConfiguration`padrão e pode ser personalizado da mesma maneira que os exemplos na seção anterior.

## <a name="run-your-application"></a>Executar a aplicação

Execute seu aplicativo. Os operadores de exemplo de todas as versões acima acima fazem uma chamada http a cada segundo para bing.com e também emite alguns logs usando ILogger. Essas linhas são encapsuladas dentro de `StartOperation` chamada de `TelemetryClient`, que é usada para criar uma operação (neste exemplo `RequestTelemetry` chamada "Operation"). Application Insights coletará esses logs do ILogger (aviso ou acima por padrão) e dependências, e eles serão correlacionados à `RequestTelemetry` com relação pai-filho. A correlação também funciona com limites de processo/rede. Por exemplo, se a chamada foi feita para outro componente monitorado, ela também será correlacionada a esse pai.

Essa operação personalizada do `RequestTelemetry` pode ser considerada como o equivalente de uma solicitação da Web de entrada em um aplicativo Web típico. Embora não seja necessário usar uma operação, ela se adapta melhor ao modelo de [dados de correlação Application insights](https://docs.microsoft.com/azure/azure-monitor/app/correlation) , com `RequestTelemetry` atuando como a operação pai, e cada telemetria gerada dentro da iteração do trabalho é tratada como sendo logicamente pertencente à mesma operação. Essa abordagem também garante que toda a telemetria gerada (automática e manual) terá o mesmo `operation_id`. Como a amostragem é baseada em `operation_id`, o algoritmo de amostragem mantém ou descarta toda a telemetria de uma única iteração.

O seguinte lista a telemetria completa coletada automaticamente pelo Application Insights.

### <a name="live-metrics"></a>Métricas ao vivo

As [métricas dinâmicas](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) podem ser usadas para verificar rapidamente se Application insights monitoramento está configurado corretamente. Embora possa levar alguns minutos antes que a telemetria comece a aparecer no portal e na análise, as métricas ao vivo mostrarão o uso da CPU do processo em execução quase em tempo real. Ele também pode mostrar outras telemetrias, como solicitações, dependências, rastreamentos etc.

### <a name="ilogger-logs"></a>Logs do ILogger

Os logs emitidos por meio de `ILogger` de severidade `Warning` ou superior são capturados automaticamente. Siga [ILogger docs](ilogger.md#control-logging-level) para personalizar quais níveis de log são capturados por Application insights.

### <a name="dependencies"></a>Dependências

A coleção de dependências é habilitada por padrão. [Este](asp-net-dependencies.md#automatically-tracked-dependencies) artigo explica as dependências coletadas automaticamente e também contém etapas para fazer o acompanhamento manual.

### <a name="eventcounter"></a>EventCounter

o `EventCounterCollectionModule` é habilitado por padrão e coletará um conjunto padrão de contadores de aplicativos .NET Core 3,0. O tutorial [EventCounter](eventcounters.md) lista o conjunto padrão de contadores coletados. Ele também tem instruções sobre como personalizar a lista.

### <a name="manually-tracking-additional-telemetry"></a>Acompanhamento manual de telemetria adicional

Embora o SDK colete automaticamente a telemetria, conforme explicado acima, na maioria dos casos, o usuário precisará enviar telemetria adicional para Application Insights serviço. A maneira recomendada para acompanhar a telemetria adicional é obter uma instância de `TelemetryClient` da injeção de dependência e, em seguida, chamar um dos métodos de [API](api-custom-events-metrics.md) de `TrackXXX()` com suporte nele. Outro caso de uso típico é o [controle personalizado das operações](custom-operations-tracking.md). Essa abordagem é demonstrada nos exemplos de trabalho acima.

## <a name="configure-the-application-insights-sdk"></a>Configurar o SDK do Application Insights

O `TelemetryConfiguration` padrão usado pelo SDK do serviço de trabalho é semelhante à configuração automática usada em um aplicativo ASP.NET ou ASP.NET Core, menos o TelemetryInitializers usado para enriquecer a telemetria de `HttpContext`.

Você pode personalizar o SDK do Application Insights para o serviço de trabalho alterar a configuração padrão. Os usuários do SDK do Application Insights ASP.NET Core podem estar familiarizados com a alteração da configuração usando ASP.NET Core [injeção de dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)interna. O SDK do WorkerService também é baseado em princípios semelhantes. Faça quase todas as alterações de configuração na seção `ConfigureServices()` chamando os métodos apropriados em `IServiceCollection`, conforme detalhado abaixo.

> [!NOTE]
> Ao usar esse SDK, não há suporte para alterar a configuração modificando `TelemetryConfiguration.Active` e as alterações não serão refletidas.

### <a name="using-applicationinsightsserviceoptions"></a>Usando ApplicationInsightsServiceOptions

Você pode modificar algumas configurações comuns passando `ApplicationInsightsServiceOptions` para `AddApplicationInsightsTelemetryWorkerService`, como neste exemplo:

```csharp
    using Microsoft.ApplicationInsights.WorkerService;

    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
    }
```

Observe que `ApplicationInsightsServiceOptions` nesse SDK está no namespace `Microsoft.ApplicationInsights.WorkerService` em oposição ao `Microsoft.ApplicationInsights.AspNetCore.Extensions` no SDK do ASP.NET Core.

Configurações comumente usadas no `ApplicationInsightsServiceOptions`

|Definição | Descrição | Predefinição
|---------------|-------|-------
|EnableQuickPulseMetricStream | Habilitar/desabilitar o recurso LiveMetrics | true
|EnableAdaptiveSampling | Habilitar/desabilitar amostragem adaptável | true
|EnableHeartbeat | Habilitar/desabilitar o recurso de pulsações, que periodicamente (padrão de 15 min) envia uma métrica personalizada chamada ' HeartBeatstate ' com informações sobre o tempo de execução como versão do .NET, informações de ambiente do Azure, se aplicável, etc. | true
|AddAutoCollectedMetricExtractor | Habilitar/desabilitar o extrator AutoCollectedMetrics, que é um TelemetryProcessor que envia métricas previamente agregadas sobre solicitações/dependências antes que a amostragem ocorra. | true

Consulte as [configurações configuráveis em `ApplicationInsightsServiceOptions`](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) para obter a lista mais atualizada.

### <a name="sampling"></a>Amostragem

O SDK do Application Insights para o serviço de trabalho dá suporte à amostragem de taxa fixa e adaptável. A amostragem adaptável é habilitada por padrão. Configurar a amostragem para o serviço de trabalho é feito da mesma maneira que para [aplicativos ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Adicionando TelemetryInitializers

Use [inicializadores de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) quando desejar definir propriedades que são enviadas com toda a telemetria.

Adicione qualquer `TelemetryInitializer` novo ao contêiner de `DependencyInjection` e o SDK irá adicioná-los automaticamente ao `TelemetryConfiguration`.

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Removendo TelemetryInitializers

Os inicializadores de telemetria estão presentes por padrão. Para remover todos os inicializadores de telemetria ou específicos, use o seguinte código de exemplo *depois* de chamar `AddApplicationInsightsTelemetryWorkerService()`.

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

### <a name="adding-telemetry-processors"></a>Adicionando processadores de telemetria

Você pode adicionar processadores de telemetria personalizados para `TelemetryConfiguration` usando o método de extensão `AddApplicationInsightsTelemetryProcessor` no `IServiceCollection`. Você usa processadores de telemetria em [cenários de filtragem avançada](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) para permitir um controle mais direto sobre o que está incluído ou excluído da telemetria que você envia para o serviço de Application insights. Use o exemplo a seguir.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configurando ou removendo TelemetryModules padrão

Application Insights usa módulos de telemetria para coletar automaticamente a telemetria sobre cargas de trabalho específicas sem a necessidade de rastreamento manual.

Os seguintes módulos de coleta automática estão habilitados por padrão. Esses módulos são responsáveis por coletar automaticamente a telemetria. Você pode desabilitá-los ou configurá-los para alterar seu comportamento padrão.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Para configurar qualquer `TelemetryModule`padrão, use o método de extensão `ConfigureTelemetryModule<T>` em `IServiceCollection`, conforme mostrado no exemplo a seguir.

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

### <a name="configuring-telemetry-channel"></a>Configurando o canal de telemetria

O canal padrão é `ServerTelemetryChannel`. Você pode substituí-lo como mostra o exemplo a seguir.

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

### <a name="disable-telemetry-dynamically"></a>Desabilitar telemetria dinamicamente

Se você quiser desabilitar a telemetria condicional e dinamicamente, poderá resolver `TelemetryConfiguration` instância com ASP.NET Core contêiner de injeção de dependência em qualquer lugar em seu código e definir `DisableTelemetry` sinalizador nela.

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

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Como posso rastrear a telemetria que não é coletada automaticamente?

Obtenha uma instância de `TelemetryClient` usando injeção de construtor e chame o método de `TrackXXX()` necessário nele. Não recomendamos criar novas instâncias de `TelemetryClient`. Uma instância singleton do `TelemetryClient` já está registrada no contêiner `DependencyInjection`, que compartilha `TelemetryConfiguration` com o restante da telemetria. A criação de uma nova instância de `TelemetryClient` é recomendada apenas se precisar de uma configuração separada do restante da telemetria.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Posso usar o IDE do Visual Studio para integrar Application Insights a um projeto de serviço de trabalho?

Atualmente, há suporte para a integração do IDE do Visual Studio apenas para aplicativos ASP.NET/ASP.NET Core. Este documento será atualizado quando o Visual Studio oferecer suporte para aplicativos de serviço de trabalho de integração.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Posso habilitar o monitoramento de Application Insights usando ferramentas como Status Monitor?

Não. [Status monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e [status monitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) atualmente dão suporte apenas a ASP.NET 4. x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se eu executar meu aplicativo no Linux, todos os recursos têm suporte?

Sim. O suporte a recursos para esse SDK é o mesmo em todas as plataformas, com as seguintes exceções:

* Os contadores de desempenho têm suporte apenas no Windows, com exceção da CPU do processo/memória mostrada em métricas ao vivo.
* Embora `ServerTelemetryChannel` esteja habilitado por padrão, se o aplicativo estiver sendo executado no Linux ou no MacOS, o canal não criará automaticamente uma pasta de armazenamento local para manter a telemetria temporariamente se houver problemas de rede. Devido a essa limitação, a telemetria é perdida quando há problemas temporários de rede ou servidor. Para contornar esse problema, configure uma pasta local para o canal:

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

[Aplicativo de console do .NET Core](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Use este exemplo se você estiver usando um aplicativo de console escrito no .NET Core (2,0 ou superior) ou .NET Framework (4.7.2 ou superior)

[Tarefas em segundo plano do ASP .NET Core com hostedservices](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Use este exemplo se você estiver no Asp.Net Core 2.1/2.2 e Criando tarefas em segundo plano de acordo com as diretrizes oficiais [aqui](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)

[Serviço de trabalho do .NET Core 3,0](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Use este exemplo se você tiver um aplicativo de serviço de trabalho do .NET Core 3,0 de acordo com as diretrizes oficiais [aqui](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)

## <a name="open-source-sdk"></a>SDK de código-fonte aberto

[Leia e colabore para o código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>Passos seguintes

* [Use a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar seus próprios eventos e métricas para uma exibição detalhada do desempenho e do uso do seu aplicativo.
* [Rastreie dependências adicionais não rastreadas automaticamente](../../azure-monitor/app/auto-collect-dependencies.md).
* [Enriquecer ou filtrar telemetria coletada automaticamente](../../azure-monitor/app/api-filtering-sampling.md).
* [Injeção de dependência em ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).
