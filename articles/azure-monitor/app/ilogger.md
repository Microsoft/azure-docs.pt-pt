---
title: Explore os logs de rastreamento do .NET no Azure Application Insights com ILogger
description: Exemplos de como utilizar a implementação do Azure Application Insights ILogger com aplicativos ASP.NET Core e a consola.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: 14c9a453913a9a8080c840df3f23751487b86c34
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58006163"
---
# <a name="ilogger"></a>ILogger

ASP.NET Core suporta uma API de log que funciona com uma variedade de provedores de registo interna e de terceiros. Este artigo mostra como lidar com o registo com a implementação do Application Insights ILogger na consola e aplicativos do ASP.NET Core. Para saber mais sobre o registo de ILogger com base, veja [este artigo](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="console-application"></a>Aplicação de consola

O código a seguir mostra um exemplo de aplicação de consola, configurado para enviar os rastreios de ILogger para o Application Insights.

Pacotes instalados:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create DI container.
        IServiceCollection services = new ServiceCollection();
            
        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(loggingBuilder =>
        {
        // Optional: Apply filters to configure LogLevel Trace or above is sent to ApplicationInsights for all
        // categories.
        loggingBuilder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
            loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");                
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional. Epecially in case of AspNetCore request info is already
        // present in scope.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }
    }
}
```

## <a name="aspnet-core-application"></a>Aplicação ASP.NET Core

Seguinte mostra um exemplo de que aplicação ASP.NET Core configurada para enviar os rastreios de ILogger para o application insights. Neste exemplo, pode ser seguido para enviar os rastreios de ILogger do Program.cs, Startup.cs ou qualquer outra lógica Contoller/aplicação.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = BuildWebHost(args);
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now.."); // This will be picked up up by AI
        host.Run();
    }

    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()                
        .ConfigureLogging(logging =>
        {                
        logging.AddApplicationInsights("ikeyhere");
                
        // Optional: Apply filters to configure LogLevel Trace or above is sent to
        // ApplicationInsights for all categories.
            logging.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
                
            // Additional filtering For category starting in "Microsoft",
        // only Warning or above will be sent to Application Insights.
        logging.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Warning);
        })
        .Build();
}
```

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    
    // The following be picked up up by Application Insights.
        _logger.LogInformation("From ConfigureServices. Services.AddMVC invoked"); 
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
        // The following be picked up up by Application Insights.   
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following be picked up up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked upby Application Insights.
    // and all have ("MyKey", "MyValue") in Properties.
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
        {           
        _logger.LogInformation("An example of a Information trace..");
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogTrace("An example of a Trace level message");
        }

        return new string[] { "value1", "value2" };
    }
}
```

Nos exemplos acima, o pacote autónomo é utilizado Microsoft.Extensions.Logging.ApplicationInsights. Por predefinição, esta configuração utiliza o mínimo absoluto `TelemetryConfiguration` para enviar dados para o Application Insights. Mínimo significa que o canal utilizado `InMemoryChannel`, nenhum amostragem e não TelemetryInitializers padrão. Este comportamento pode ser substituído para uma aplicação de consola, conforme mostrado no exemplo abaixo.

Instale este pacote adicional:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A seção a seguir mostra como substituir a predefinição `TelemetryConfiguration`. Este exemplo configura `ServerTelemetryChannel`, sampling e um personalizado `ITelemetryInitializer`.

```csharp
    // Create DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {                            
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding ApplicationInsights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });
```

Embora a abordagem acima pode ser usada num aplicativo do ASP.NET Core, uma abordagem mais comum seria combinar a monitorização de aplicações regulares (pedidos, etc. de dependências) com a captura de ILogger conforme mostrado abaixo.

Instale este pacote adicional:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
```

Adicione o seguinte para o `ConfigureServices` método. Este código permitirá que aplicativo regular monitorização com a configuração predefinida (ServerTelemetryChannel, LiveMetrics, pedido/dependências, correlação etc.)

```csharp
services.AddApplicationInsightsTelemetry("ikeyhere");
```

Neste exemplo, a configuração utilizado pelo `ApplicationInsightsLoggerProvider` é igual ao utilizado pela monitorização de aplicações regular. Por isso ambos `ILogger` rastreios e outra telemetria (pedidos, etc. de dependências) que irão estar a executar o mesmo conjunto de `TelemetryInitializers`, `TelemetryProcessors`, e `TelemetryChannel`. Irá ser correlacionados e amostragem de objeto de amostragem/não da mesma forma.

No entanto, existe uma exceção a este comportamento. A predefinição `TelemetryConfiguration` não está totalmente quando configurar a registar algo a partir do `Program.cs` ou `Startup.cs` em si, para que esses registos não tenham a configuração predefinida. No entanto, cada registo (por exemplo, registos de controladores, etc. de modelos) compartilhariam a configuração.

## <a name="control-logging-level"></a>Nível de registo de controlo

Para além de filtragem de registos no código como nos exemplos acima, também é possível controlar o nível de registo do Application Insights captura, modificando o `appsettings.json`. O [registo de documentação de conceitos básicos do ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) mostra como fazer isso. Especificamente para o Application Insights, o nome do alias do fornecedor é `ApplicationInsights`, como mostra o exemplo que configura abaixo `ApplicationInsights` para capturar apenas os registos de `Warning` e acima de todas as categorias.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre:

- [Registo de ILogger com base em](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
- [Logs de rastreamento do .NET](../../azure-monitor/app/asp-net-trace-logs.md)
