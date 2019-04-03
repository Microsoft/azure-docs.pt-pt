---
title: Explore os logs de rastreamento do .NET no Azure Application Insights com ILogger
description: Exemplos de como utilizar o fornecedor do Azure Application Insights ILogger com aplicativos ASP.NET Core e a consola.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: b8c0d84f6989e60c7abef0d423230300896663de
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877912"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider para .NET Core ILogger registos

ASP.NET Core suporta uma API de log que funciona com diferentes tipos de provedores de registro incorporadas e de terceiros. Registo é feito chamando Log() ou numa variante do mesmo no `ILogger` instâncias. Este artigo mostra como usar `ApplicationInsightsLoggerProvider` para capturar `ILogger` registos na consola e aplicativos do ASP.NET Core. Este artigo também descreve como `ApplicationInsightsLoggerProvider` está integrado com outra telemetria do Application Insights.
Para saber mais início de sessão Asp.Net Core, veja [este artigo](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Aplicativos do ASP.NET Core

A partir [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 de versão e posteriores, `ApplicationInsightsLoggerProvider` está ativada por predefinição, quando ativar a monitorização do Application Insights regulares através dos métodos padrão - pela a invocar `UseApplicationInsights` método de extensão no IWebHostBuilder ou `AddApplicationInsightsTelemetry` método de extensão no IServiceCollection. `ILogger` registos capturado pelo `ApplicationInsightsLoggerProvider` estão sujeitos a mesma configuração de qualquer outra telemetria recolhida. ou seja eles têm o mesmo conjunto de `TelemetryInitializer`s, `TelemetryProcessor`s, usa o mesmo `TelemetryChannel`e será ser correlacionados e amostragem da mesma forma como todos os outra telemetria.  Se estiver a utilizar esta versão do SDK ou superior, em seguida, é necessária nenhuma ação para capturar `ILogger` registos.

Por predefinição, apenas `ILogger` registos de `Warning` ou acima (de todas as categorias) são enviados para o Application Insights. Esse comportamento pode ser alterado ao aplicar filtros, conforme mostrado [aqui](#control-logging-level). Também são necessários passos adicionais se `ILogger` inicia a partir de `Program.cs` ou `Startup.cs` devem ser capturadas como mostrado [aqui](#capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications).

Se usar uma versão anterior do SDK de Microsoft.ApplicationInsights.AspNet ou desejar usar apenas ApplicationInsightsLoggerProvider, sem quaisquer outros monitorização do Application Insights, siga os passos abaixo.

1. Instale o pacote nuget.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
    </ItemGroup>
```

2. Modifique `Program.cs` conforme mostrado abaixo

```csharp
    using Microsoft.AspNetCore;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Extensions.Logging;

    public class Program
    {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
            builder =>
                {
                    // Providing an instrumentation key here is required if you are using standalone package Microsoft.Extensions.Logging.ApplicationInsights
                    // or if you want to capture logs from early in the application startup pipeline from Startup.cs or Program.cs itself.
                    builder.AddApplicationInsights("ikey");

                    // Optional: Apply filters to control what logs are sent to Application Insights.
                    // The following configures LogLevel Information or above to be sent to Application Insights for all categories.
                    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("", LogLevel.Information);
                }
            );
    }
```

O código acima irá configurar `ApplicationInsightsLoggerProvider`. Seguinte mostra um exemplo de classe de controlador, que usa `ILogger` para enviar registos, que são capturados pelo Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications"></a>Captura de registos de ILogger de Startup.cs, Program.cs em aplicativos do Asp.Net Core

Com o novo ApplicationInsightsLoggerProvider, é possível capturar os registos no início do pipeline de inicialização do aplicativo. Até mesmo, porém ApplicationInsightsLoggerProvider é automaticamente ativado o Application Insights (de 2.7.0-beta3), ele não tem a configuração de chave de instrumentação até mais tarde no pipeline, então, apenas os registos de controlador / outras classes serão capturados. Para capturar todos os registos a partir `Program.cs` e `Startup.cs` em si, um tem de ativar explicitamente ApplicationInsightsLoggerProvider com uma chave de instrumentação. Também é importante ter em conta que `TelemetryConfiguration` não está totalmente quando configurar a registar algo a partir do `Program.cs` ou `Startup.cs` em si, para que esses registos utilizará uma configuração de mínimo bare, que utiliza InMemoryChannel, nenhum amostragem e sem telemetria standard inicializadores ou processadores.

Seguinte mostra exemplos de `Program.cs` e `Startup.cs` através desta funcionalidade.

#### <a name="example-programcs"></a>Program.cs de exemplo

```csharp
    using Microsoft.AspNetCore;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Extensions.Logging;

    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateWebHostBuilder(args).Build();
            var logger = host.Services.GetRequiredService<ILogger<Program>>();
            // This will be picked up by AI
            logger.LogInformation("From Program. Running the host now..");
            host.Run();
        }

        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>()
            .ConfigureLogging(
              builder =>
                  {
                    // providing an instrumentation key here is required if you are using standalone package Microsoft.Extensions.Logging.ApplicationInsights
                    // or if you want to capture logs from early in the application startup pipeline from Startup.cs or Program.cs itself.
                    builder.AddApplicationInsights("ikey");

                    // Adding the filter below to ensure logs of all severity from Program.cs is sent to ApplicationInsights.
                    // Replace YourAppName with the namespace of your application's Program.cs
                    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("YourAppName.Program", LogLevel.Trace);
                    // Adding the filter below to ensure logs of all severity from Startup.cs is sent to ApplicationInsights.
                    // Replace YourAppName with the namespace of your application's Startup.cs
                    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("YourAppName.Startup", LogLevel.Trace);
                  }
                );
    }
```

#### <a name="example-startupcs"></a>Startup.cs de exemplo

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrating-from-old-applicationinsightsloggerprovider"></a>Migrando do ApplicationInsightsLoggerProvider antigo

Versões do SDK de Microsoft.ApplicationInsights.AspNet antes 2.7.0-beta2, suportado um provedor de log que está obsoleto. Esse provedor foi ativado com `AddApplicationInsights()` método de extensão de `ILoggerFactory`. Este fornecedor está obsoleto e os utilizadores são sugeridos para migrar para o novo provedor. A migração envolve dois passos.

1. Remover chamada ILoggerFactory.AddApplicationInsights() `Startup.Configure()` método para evitar o Registro em log duplo.
2. Volte a aplicar regras de filtragem no código à medida que eles não serão respeitados pelo novo fornecedor. Sobrecargas de ILoggerFactory.AddApplicationInsights() demorou mínimo funções LogLevel ou um filtro. Com o novo fornecedor, a filtragem é parte da arquitetura de registo em si e não é feito pelo fornecedor do Application Insights. Então, todos os filtros fornecidos através de `ILoggerFactory.AddApplicationInsights()` sobrecargas devem ser removidas e regras de filtragem devem ser fornecida a seguir [estes](#control-logging-level) instruções. Se usar `appsettings.json` para filtrar o Registro em log, irá continuar a trabalhar com o novo provedor como ambos utilizam o Alias do fornecedor mesmo - **Application Insights**.

Enquanto fornecedor antigo ainda pode ser usado (será removida apenas numa alteração de versão principal para 3.xx e está agora obsoleta), migrar para o fornecedor mais recente é altamente recomendável pelos seguintes motivos.

1. Fornecedor anterior não tinha suporte dos [âmbitos](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). No novo fornecedor, propriedades de âmbito são adicionadas automaticamente como propriedades personalizadas à telemetria recolhida.
2. Agora podem ser capturados registos muito mais antecedência no pipeline de inicialização do aplicativo. ou seja Registos de classes de programa e inicialização agora podem ser capturados.
3. Com o novo fornecedor, a filtragem é efetuada ao nível da estrutura em si. Filtragem de registos para o fornecedor do Application Insights pode ser feito no exatamente da mesma maneira como para outros fornecedores, incluindo provedores internos, como o Console, depuração e assim por diante. Também é possível aplicar os mesmos filtros para vários fornecedores.
4. O [recomendado](https://github.com/aspnet/Announcements/issues/255) forma no Asp.Net Core (2.0 ou posterior) para os fornecedores de registo é usar métodos de extensão no ILoggingBuilder no `Program.cs` em si.

> [!Note]
O novo provedor está disponível para aplicativos destinados `NETSTANDARD2.0` ou superior. Se seu aplicativo se destina a versões mais antigas do .NET Core, como o .NET Core 1.1, ou se direcionando para o .NET Framework, continue a utilizar o fornecedor de antigo.

## <a name="console-application"></a>Aplicação de consola

O código seguinte mostra um exemplo de aplicação de consola, configurado para enviar `ILogger` rastreios para o Application Insights.

Pacotes instalados:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
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

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(loggingBuilder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to Application Insights for all
            // categories.
            loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                                                                    ("", LogLevel.Trace);
            loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

No exemplo acima, o pacote autônomo `Microsoft.Extensions.Logging.ApplicationInsights` é utilizado. Por predefinição, esta configuração utiliza o mínimo absoluto `TelemetryConfiguration` para enviar dados para o Application Insights. Mínimo significa que o canal utilizado `InMemoryChannel`, nenhum amostragem e não TelemetryInitializers padrão. Este comportamento pode ser substituído para uma aplicação de consola, conforme mostrado no exemplo abaixo.

Instale este pacote adicional:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A seção a seguir mostra como substituir a predefinição `TelemetryConfiguration` usando `services.Configure<TelemetryConfiguration>()` método. Este exemplo configura `ServerTelemetryChannel`amostragem e adiciona um personalizado `ITelemetryInitializer` para o `TelemetryConfiguration`.

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

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly call Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if application terminates, telemetry is sent to the back-end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Nível de registo de controlo

O Asp.Net Core `ILogger` infra-estrutura tem um mecanismo interno para aplicar [filtragem](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) de registos, que permite aos utilizadores controlar os registos enviados para cada fornecedores registados, incluindo o fornecedor do Application Insights. Esta filtragem pode ser feito na configuração (normalmente, a utilização `appsettings.json` ficheiros) ou no código. Esse recurso é fornecido pela própria estrutura e não é específico do fornecedor do Application Insights.

Exemplos de aplicar as regras de filtro para ApplicationInsightsLoggerProvider é indicada abaixo.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Criar regras de filtro na configuração com appSettings

Para ApplicationInsightsLoggerProvider, o alias do fornecedor é `ApplicationInsights`. O abaixo mostra a secção `appsettings.json` configura os registos `Warning` e acima de todas as categorias, `Error` e acima de categorias, começando com "Microsoft" a serem enviados para `ApplicationInsightsLoggerProvider`.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>Criar regras do filtro no código

O abaixo código fragmento configura registos `Warning` e acima de todas as categorias, `Error` e acima de categorias, começando com a Microsoft a serem enviados para `ApplicationInsightsLoggerProvider`. Esta configuração é o mesmo que a configuração acima feita em `appsettings.json`.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
        logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("", LogLevel.Warning)
               .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

*1. O que é o ApplicationInsightsLoggerProvider antigo e novo?*

* [SDK de Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) fornecido com um ApplicationInsightsLoggerProvider incorporada (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), que foi ativada com ILoggerFactory métodos de extensão. Este fornecedor está marcado como obsoleto de 2.7.0-beta2 e posteriores e será ser completamente removido na próxima alteração de versão principal. [Isso](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) do pacote em si não obsoleto e é necessária para ativar a monitorização de pedidos, dependências etc.

* A alternativa sugerida é o novo pacote autónomo [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), que contém um (ApplicationInsightsLoggerProvider melhorada Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) e métodos de extensões em ILoggerBuilder para ativá-la.

* [SDK de Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 de versão e superior irá demorar uma dependência no pacote de acima e permite `ILogger` capturar automaticamente.

*2. Estou a ver algumas `ILogger` registos são apresentados duas vezes no Application Insights?*

* Essa duplicação é possível se tiver a versão mais antiga (agora obsoleta) de `ApplicationInsightsLoggerProvider` ativada ao chamar `AddApplicationInsights` no `ILoggerFactory`. Verifique se sua `Configure` tem o seguinte método e removê-lo.

   ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
        // ..other code.
    }
   ```

* Se estiver a ter registo duplo quando depuração no Visual Studio, em seguida, modifique o código usado para ativar o Application Insights como a seguir, definindo `EnableDebugLogger` como falsa. Este problema da duplicação e a correção é relevante apenas ao depurar a aplicação.

   ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
        options.EnableDebugLogger = false;
        services.AddApplicationInsightsTelemetry(options);
        // ..other code.
    }
   ```

*3. Eu atualizada para [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 de versão e eu agora estou a ver que inicia a partir de `ILogger` são capturados automaticamente. Como pode desativar esta funcionalidade completamente?*

* Ver [isso](../../azure-monitor/app/ilogger.md#control-logging-level) secção para saber como filtrar registos em geral. Para desative ApplicationInsightsLoggerProvider utilize `LogLevel.None` para ele.

  No código

    ```csharp
        builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("", LogLevel.None);
    ```

  Na configuração

    ```json
    {
      "Logging": {
        "ApplicationInsights": {
          "LogLevel": {
            "Default": "None"
          }
    }
    ```

*4. Estou a ver algumas `ILogger` registos não estão a ter as mesmas propriedades como outras pessoas?*

* Capturas de informações de aplicação e envia `ILogger` registos com o mesmo `TelemetryConfiguration` utilizado para todos os outra telemetria. Existe uma exceção a esta regra. A predefinição `TelemetryConfiguration` não está totalmente quando configurar a registar algo a partir do `Program.cs` ou `Startup.cs` em si, para que os registos a partir desses lugares, não terão a configuração predefinida e, por conseguinte, não serão executado todos os `TelemetryInitializer`s e `TelemetryProcessor`s.

*5. Que tipo de telemetria do Application Insights é produzido a partir do `ILogger` registos? ou onde posso ver `ILogger` regista no Application Insights?*

* Captura de ApplicationInsightsLoggerProvider `ILogger` logon e cria `TraceTelemetry` do mesmo. Se um objeto de exceção é transmitido ao método Log() ILogger, em seguida, em vez de `TraceTelemetry`, um `ExceptionTelemetry` é criado. Estes itens de telemetria podem ser encontrados nos mesmos locais como qualquer outro `TraceTelemetry` ou `ExceptionTelemetry` para o Application Insights, incluindo o portal, o analytics ou o depurador do Visual Studio local.
Se preferir enviar sempre `TraceTelemetry`, em seguida, utilize o fragmento ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```.

*5. Não tenho SDK instalado, e posso usar a extensão da aplicação Web do Azure para ativar o Application Insights para meus aplicativos do Asp.Net Core. Como posso utilizar o novo fornecedor?*

* Extensão do Application Insights na aplicação Web do Azure utiliza o fornecedor antigo. Regras de filtragem podem ser modificadas de `appsettings.json` para a sua aplicação. Se pretender tirar partido do novo fornecedor, utilize instrumentação do tempo de compilação, efetuando a dependência do nuget no SDK. Este documento será atualizado quando muda de extensão para utilizar o novo fornecedor.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre:

* [Iniciar sessão no Asp.Net Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Registos de rastreio de .NET no Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
