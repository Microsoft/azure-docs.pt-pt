---
title: Informações de aplicação para aplicações de serviço de trabalhador (aplicações não HTTP)
description: Monitorizar as aplicações do Núcleo net/.NET Framework non-HTTP com o Azure Monitor Application Insights.
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: f043140e5a342d114f777ad16bba588790b7f8cc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536731"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Informações de aplicação para aplicações de serviço ao trabalhador (aplicações não HTTP)

Application Insights está lançando um novo `Microsoft.ApplicationInsights.WorkerService`SDK, chamado , que é mais adequado para cargas de trabalho não HTTP, como mensagens, tarefas de fundo, aplicações de consola, etc. Este tipo de aplicações não tem a noção de um pedido http como um tradicional ASP.NET/ASP.NET Aplicação Web Core, e, portanto, não é suportado o uso de pacotes de Insights de aplicação para [ASP.NET](asp-net.md) ou [ASP.NET aplicações Core.](asp-net-core.md)

O novo SDK não faz nenhuma coleção de telemetria por si só. Em vez disso, traz outros colecionadores de automóveis bem conhecidos da Application Insights, como [DependencyCollector,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) [PerfCounterCollector,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) etc. Este SDK expõe métodos de extensão `IServiceCollection` para ativar e configurar a recolha de telemetria.

## <a name="supported-scenarios"></a>Cenários suportados

O [SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) application Insights para o Serviço de Trabalhadores é mais adequado para aplicações não HTTP, independentemente de onde ou como funcionam. Se a sua aplicação estiver em execução e tiver conectividade de rede com o Azure, a telemetria pode ser recolhida. A monitorização de Insights de Aplicação é suportada em todo o lado .NET Core é suportado. Este pacote pode ser utilizado no recém-introduzido [.NET Core 3.0 Worker Service](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances), tarefas de [fundo em Asp.Net Core 2.1/2.2,](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)aplicações de consola (.NET Core/ .NET Framework), etc.

## <a name="prerequisites"></a>Pré-requisitos

Uma chave de instrumentação de insights de aplicação válida. Esta chave é necessária para enviar qualquer telemetria para Application Insights. Se precisar de criar um novo recurso Application Insights para obter uma chave de instrumentação, consulte [Criar um recurso Deinsights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)de Aplicação .

## <a name="using-application-insights-sdk-for-worker-services"></a>Utilização de Insights de Aplicação SDK para serviços de trabalhadores

1. Instale o pacote [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) na aplicação.
   O seguinte corte mostra as alterações que precisam de `.csproj` ser adicionadas ao ficheiro do seu projeto.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. Método `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` de `IServiceCollection`extensão de chamada, fornecendo a chave de instrumentação. Este método deve ser chamado no início da aplicação. A localização exata depende do tipo de aplicação.

1. Recupere `ILogger` uma `TelemetryClient` instância ou instância do recipiente de `serviceProvider.GetRequiredService<TelemetryClient>();` injeção de dependência (DI) chamando ou utilizando a injeção de construtor. Este passo irá desencadear `TelemetryConfiguration` a instalação de módulos de recolha automática e de recolha automática.

As instruções específicas para cada tipo de aplicação são descritas nas seguintes secções.

## <a name="net-core-30-worker-service-application"></a>.NET Core 3.0 aplicação de serviço ao trabalhador

O exemplo completo é partilhado [aqui](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)

1. Descarregue e instale [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Crie um novo projeto de Serviço de Trabalhadores, quer usando o novo modelo de projeto do Estúdio Visual ou linha de comando`dotnet new worker`
3. Instale o pacote [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) na aplicação.

4. Adicione `services.AddApplicationInsightsTelemetryWorkerService();` ao `CreateHostBuilder()` método `Program.cs` na sua classe, como neste exemplo:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Modifique `Worker.cs` o seu de acordo com o exemplo abaixo.

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

6. Instale a chave de instrumentação.

    Embora possa fornecer a chave de `AddApplicationInsightsTelemetryWorkerService`instrumentação como argumento para, recomendamos que especifique a chave de instrumentação na configuração. A amostra de código que se segue `appsettings.json`mostra como especificar uma chave de instrumentação em . Certifique-se de que `appsettings.json` está copiada para a pasta raiz da aplicação durante a publicação.

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
OU`SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Normalmente, `APPINSIGHTS_INSTRUMENTATIONKEY` especifica a chave de instrumentação para aplicações implementadas nas Aplicações Web como Web Jobs.

> [!NOTE]
> Uma chave de instrumentação especificada no `APPINSIGHTS_INSTRUMENTATIONKEY`código ganha sobre a variável ambiental , que ganha sobre outras opções.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET core tarefas de fundo com serviços hospedados

[Este](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) documento descreve como criar tarefas de fundo na aplicação Core 2.1/2.2 ASP.NET.

O exemplo completo é partilhado [aqui](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)

1. Instale o Microsoft.ApplicationInsights.WorkerService(pacotehttps://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) para a aplicação.
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

Segue-se o `TimedHostedService` código para onde reside a lógica da tarefa de fundo.

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

3. Instale a chave de instrumentação.
   Utilize o `appsettings.json` mesmo a partir do exemplo de Serviço de Trabalhador .NET Core 3.0 acima.

## <a name="net-corenet-framework-console-application"></a>.NET Core/.NET Framework Console application

Como mencionado no início deste artigo, o novo pacote pode ser usado para permitir a Telemetria de Insights de Aplicação até mesmo de uma aplicação regular de consola. Este pacote [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)tem como alvo , e por isso pode ser utilizado para aplicações de consola em .NET Core 2.0 ou superior, e .NET Framework 4.7.2 ou superior.

O exemplo completo é partilhado [aqui](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)

1. Instale o Microsoft.ApplicationInsights.WorkerService(pacotehttps://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) para a aplicação.

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

Esta aplicação de consola `TelemetryConfiguration`também utiliza o mesmo padrão , e pode ser personalizada da mesma forma que os exemplos na secção anterior.

## <a name="run-your-application"></a>Executar a aplicação

Execute a sua candidatura. Os trabalhadores de todos os acima referidos fazem uma chamada http a cada segundo para bing.com, e também emitem poucos registos usando o ILogger. Estas linhas são `StartOperation` embrulhadas `TelemetryClient`dentro da chamada de , que `RequestTelemetry` é usada para criar uma operação (neste exemplo chamada "operação"). Os Insights de Aplicação recolherão estes registos ILogger (aviso ou acima por `RequestTelemetry` defeito) e dependências, e serão correlacionados com a relação pai-filho. A correlação também funciona entre processos cruzados/limite de rede. Por exemplo, se a chamada foi feita para outro componente monitorizado, então também será correlacionado com este progenitor.

Esta operação `RequestTelemetry` personalizada pode ser considerada como o equivalente a um pedido web de entrada numa aplicação web típica. Embora não seja necessário utilizar uma Operação, encaixa-se melhor no `RequestTelemetry` modelo de dados de correlação de Insights de [Aplicação](https://docs.microsoft.com/azure/azure-monitor/app/correlation) - com a atuação como operação dos pais, e todas as telemetrias geradas dentro da iteração do trabalhador sendo tratadas logicamente como pertencentes à mesma operação. Esta abordagem também garante que toda a telemetria gerada (automática e manual) terá a mesma `operation_id`. Como a amostragem é baseada em `operation_id`, algoritmo de amostragem ou mantém ou deixa cair toda a telemetria de uma única iteração.

O seguinte enumera a telemetria completa recolhida automaticamente pela Application Insights.

### <a name="live-metrics"></a>Live Metrics

[As Métricas Vivas](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) podem ser usadas para verificar rapidamente se a monitorização de Insights de Aplicação está configurada corretamente. Embora possa demorar alguns minutos até que a telemetria comece a aparecer no portal e na análise, a Live Metrics mostraria o uso do CPU do processo de execução em quase tempo real. Também pode mostrar outras telemetrias como Pedidos, Dependências, Vestígios, etc.

### <a name="ilogger-logs"></a>Registos ILogger

Os registos `ILogger` emitidos por `Warning` gravidade ou superior são automaticamente capturados. Siga os [docs iLogger](ilogger.md#control-logging-level) para personalizar quais os níveis de registo capturados pela Application Insights.

### <a name="dependencies"></a>Dependências

A recolha de dependência é ativada por defeito. [Este](asp-net-dependencies.md#automatically-tracked-dependencies) artigo explica as dependências que são recolhidas automaticamente, e também contêm passos para fazer o rastreio manual.

### <a name="eventcounter"></a>Contador de Eventos

`EventCounterCollectionModule`é ativado por padrão, e irá recolher um conjunto padrão de contadores de aplicações .NET Core 3.0. O tutorial [EventCounter](eventcounters.md) lista o conjunto padrão de contadores recolhidos. Também tem instruções sobre personalizar a lista.

### <a name="manually-tracking-additional-telemetry"></a>Rastreio manual de telemetria adicional

Enquanto o SDK recolhe automaticamente a telemetria como explicado acima, na maioria dos casos o utilizador terá de enviar telemetria adicional para o serviço Application Insights. A forma recomendada de rastrear a telemetria `TelemetryClient` adicional é obtendo uma instância de `TrackXXX()` injeção de dependência e, em seguida, chamando um dos métodos [de API](api-custom-events-metrics.md) suportados nele. Outro caso de uso típico é [o rastreio personalizado de operações.](custom-operations-tracking.md) Esta abordagem é demonstrada nos exemplos do Trabalhador acima.

## <a name="configure-the-application-insights-sdk"></a>Configure o SDK de Insights de Aplicação

O `TelemetryConfiguration` padrão utilizado pelo serviço de trabalhador SDK é semelhante à configuração automática utilizada numa aplicação ASP.NET ou `HttpContext`ASP.NET Core, menos os TelemettryInitializers utilizados para enriquecer a telemetria de .

Pode personalizar o SDK de Insights de Aplicação para o Serviço de Trabalhadores para alterar a configuração predefinida. Os utilizadores dos Insights de Aplicação ASP.NET Core SDK podem estar familiarizados com a alteração da configuração utilizando ASP.NET injeção de [dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)incorporada do Núcleo . O SDK do Serviço de Trabalhadores baseia-se também em princípios semelhantes. Faça quase todas as `ConfigureServices()` alterações de `IServiceCollection`configuração na secção, chamando métodos apropriados, conforme descrito abaixo.

> [!NOTE]
> Ao utilizar este SDK, alterar `TelemetryConfiguration.Active` a configuração modificando não é suportado e as alterações não serão refletidas.

### <a name="using-applicationinsightsserviceoptions"></a>Utilização de Opções de Serviço sintetizá-lo

Pode modificar algumas configurações `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetryWorkerService`comuns passando para, como neste exemplo:

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

Note `ApplicationInsightsServiceOptions` que neste SDK está `Microsoft.ApplicationInsights.WorkerService` no espaço `Microsoft.ApplicationInsights.AspNetCore.Extensions` de nome em oposição ao ASP.NET Core SDK.

Configurações comumente usadas em`ApplicationInsightsServiceOptions`

|Definição | Descrição | Predefinição
|---------------|-------|-------
|EnableQuickPulseMetricStream | Ativar/desativar a funcionalidade LiveMetrics | true
|EnableAdaptiveSampling | Ativar/desativar a amostragem adaptativa | true
|EnableHeartbeat | Ativar/Desativar a funcionalidade Heartbeats, que periodicamente (padrão de 15 min) envia uma métrica personalizada chamada 'HeartBeatState' com informações sobre o tempo de execução como .NET Version, Azure Environment information, se aplicável, etc. | true
|Extrator métrico addautocollected | Ativar/Desativar o extrator de métricas automáticas, que é um Processador de Telemetria que envia métricas pré-agregadas sobre Pedidos/Dependências antes da amostragem. | true

Consulte as [definições `ApplicationInsightsServiceOptions` configuráveis](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) para obter a lista mais atualizada.

### <a name="sampling"></a>Amostragem

O SDK application Insights para o Serviço de Trabalhadores suporta amostras de taxa fixa e adaptável. A amostragem adaptativa é ativada por defeito. Configurar a amostragem para o Serviço de Trabalhadores é feito da mesma forma que para [ASP.NET Aplicações Core](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Adicionar Ininicializadores de Telemetria

Utilize iniciais de [telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) quando quiser definir propriedades que são enviadas com toda a telemetria.

Adicione qualquer `TelemetryInitializer` novidade `DependencyInjection` ao recipiente e o SDK `TelemetryConfiguration`irá adicioná-los automaticamente ao .

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Remoção de TelemettryInitializers

Os iniciais de telemetria estão presentes por defeito. Para remover todos ou rubricas específicos de telemetria, `AddApplicationInsightsTelemetryWorkerService()`utilize o seguinte código de amostra *depois* de ligar .

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

Pode adicionar processadores de telemetria `TelemetryConfiguration` personalizados `AddApplicationInsightsTelemetryProcessor` `IServiceCollection`utilizando o método de extensão em . Utiliza processadores de telemetria em [cenários avançados](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) de filtragem para permitir um controlo mais direto sobre o que está incluído ou excluído da telemetria que envia para o serviço Application Insights. Use o seguinte exemplo.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configurar ou remover os Módulos de Telemetria predefinidos

Application Insights usa módulos de telemetria para recolher automaticamente telemetria sobre cargas de trabalho específicas sem necessitar de rastreio manual.

Os seguintes módulos de recolha automática são ativados por defeito. Estes módulos são responsáveis pela recolha automática de telemetria. Pode desativá-los ou configurá-los para alterar o seu comportamento padrão.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Para configurar `TelemetryModule`qualquer predefinição, utilize o método `ConfigureTelemetryModule<T>` de extensão, `IServiceCollection`como mostra o seguinte exemplo.

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

### <a name="configuring-telemetry-channel"></a>Configurar canal de telemetria

O canal `ServerTelemetryChannel`predefinido é . Pode anular como mostra o exemplo seguinte.

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

### <a name="disable-telemetry-dynamically"></a>Desativar a telemetria dinamicamente

Se pretender desativar a telemetria condicional `TelemetryConfiguration` e dinamicamente, poderá resolver o `DisableTelemetry` exemplo com ASP.NET recipiente de injeção de dependência do Núcleo em qualquer lugar do seu código e colocar a bandeira nele.

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

Obtenha uma `TelemetryClient` instância usando a injeção de construtor, e ligue para o método necessário `TrackXXX()` nele. Não recomendamos a `TelemetryClient` criação de novos casos. Uma instância `TelemetryClient` singleton já está `DependencyInjection` registada no `TelemetryConfiguration` contentor, que partilha com o resto da telemetria. A criação de uma nova `TelemetryClient` instância só é recomendada se precisar de uma configuração separada do resto da telemetria.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Posso usar o Visual Studio IDE para embarcar na Aplicação Insights para um projeto de Serviço de Trabalhadores?

O onboarding visual Studio IDE é atualmente suportado apenas para ASP.NET/ASP.NET Aplicações Core. Este documento será atualizado quando o Visual Studio apoiar aplicações de serviço de trabalhadores de bordo.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Posso ativar a monitorização de Insights de Aplicação utilizando ferramentas como o Status Monitor?

Não. [O Monitor de Estado](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e o Monitor de [Estado v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) suportam atualmente ASP.NET apenas 4.x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se eu executar a minha candidatura em Linux, todos os recursos são suportados?

Sim. O suporte de funcionalidades para este SDK é o mesmo em todas as plataformas, com as seguintes exceções:

* Os contadores de desempenho são suportados apenas no Windows, com exceção do Processo CPU/Memória mostrado em Métricas Ao Vivo.
* Mesmo `ServerTelemetryChannel` que esteja ativado por padrão, se a aplicação estiver em execução no Linux ou macOS, o canal não cria automaticamente uma pasta de armazenamento local para manter a telemetria temporariamente se houver problemas de rede. Devido a esta limitação, a telemetria perde-se quando existem problemas temporários de rede ou servidor. Para contornar este problema, configure uma pasta local para o canal:

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

[.NET Central Console Application](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Utilize esta amostra se estiver a utilizar uma aplicação de consola escrita em .NET Core (2.0 ou superior) ou .NET (4.7.2 ou superior)

[ASP .NET Core tarefas de fundo com Serviços Hospedados](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Utilize esta amostra se estiver em Asp.Net Core 2.1/2.2, e criando tarefas de fundo de acordo com a orientação oficial [aqui](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)

[.NET Core 3.0 Serviço de Trabalhador](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Utilize esta amostra se tiver uma aplicação .NET Core 3.0 Worker Service de acordo com a orientação oficial [aqui](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)

## <a name="open-source-sdk"></a>SDK de código aberto

[Leia e contribua para o código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>Passos seguintes

* [Utilize a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar os seus próprios eventos e métricas para uma visão detalhada do desempenho e utilização da sua aplicação.
* [Acompanhe dependências adicionais não rastreadas automaticamente.](../../azure-monitor/app/auto-collect-dependencies.md)
* [Enriquecer ou filtrar telemetria recolhida automaticamente.](../../azure-monitor/app/api-filtering-sampling.md)
* [Injeção de Dependência no núcleo ASP.NET.](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
