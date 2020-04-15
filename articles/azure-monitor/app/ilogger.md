---
title: Explore os registos de rastreio .NET com iLogger - Azure Application Insights
description: Amostras da utilização do fornecedor Azure Application Insights ILogger com aplicações ASP.NET Core e Console.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 33effe9cfec6d766d573617ff03b58564e5b34d1
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313666"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider para registos .NET Core ILogger

ASP.NET Core suporta uma API de exploração madeireira que funciona com diferentes tipos de fornecedores de exploração madeireira incorporados e de terceiros. O registo é feito através do **log()** ou de uma variante do mesmo em instâncias *ILogger.* Este artigo demonstra como utilizar o *ApplicationInsightsLoggerProvider* para capturar os registos do ILogger nas aplicações de consola e ASP.NET Core. Este artigo também descreve como applicationInsightsLoggerProvider se integra com outras telemetria sinuosas de aplicação.
Para saber mais, consulte [a exploração madeireira no núcleo ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>aplicações ASP.NET Core

ApplicationInsightsLoggerProvider é ativado por padrão na [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.7.1 (e mais tarde) quando ativa a monitorização regular de Insights de Aplicação através de qualquer um dos métodos:

- Ao chamar o método de extensão **UseApplicationInsights** no IWebHostBuilder (agora obsoleto)
- Ao chamar o método de extensão **AddApplicationInsightsTelemettry** no IServiceCollection

Os registos iLogger que o ApplicationInsightsLoggerProvider captura estão sujeitos à mesma configuração que qualquer outra telemetria que seja recolhida. Têm o mesmo conjunto de TelemettryInitializers e TelemettryProcessors, usam o mesmo TelemettryChannel, e são correlacionados e amostrados da mesma forma que outras telemetrias. Se utilizar a versão 2.7.1 ou posterior, não é necessária qualquer ação para capturar os registos do ILogger.

Apenas os registos *de aviso* ou iLogger mais elevados (de todas [as categorias)](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-3.1#log-category)são enviados para A aplicação Insights por padrão. Mas pode [aplicar filtros para modificar este comportamento.](#control-logging-level) São necessários passos adicionais para capturar registos do ILogger a partir de **Program.cs** ou **Startup.cs**. (Ver [registos ilogger de captura de Startup.cs e Program.cs em aplicações ASP.NET Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Se utilizar uma versão anterior do Microsoft.ApplicationInsights.AspNet SDK ou pretender utilizar apenas o ApplicationInsightsLoggerProvider sem qualquer outra monitorização do Application Insights, utilize o seguinte procedimento:

1. Instale o pacote NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Modifique **Program.cs** como mostrado aqui:

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
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

O código no passo `ApplicationInsightsLoggerProvider`2 configura. O seguinte código mostra uma classe `ILogger` controlador exemplo, que utiliza para enviar registos. Os registos são capturados pela Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Capture registos iLogger de Startup.cs e Program.cs em aplicações ASP.NET Core

> [!NOTE]
> Em ASP.NET Core 3.0 e mais tarde, `ILogger` já não é possível injetar em Startup.cs e Program.cs. Veja https://github.com/aspnet/Announcements/issues/353 mais detalhes.

O novo ApplicationInsightsLoggerProvider pode capturar registos desde o início do pipeline de arranque de aplicações. Embora o ApplicationInsightsLoggerProvider esteja automaticamente ativado em Insights de Aplicação (a partir da versão 2.7.1), não tem uma chave de instrumentação configurada até mais tarde no pipeline. Assim, apenas os **Controller**registos do Controlador/outras classes serão capturados. Para capturar cada registo a partir **de Program.cs** e **Startup.cs** em si, deve ativar explicitamente uma chave de instrumentação para ApplicationInsightsLoggerProvider. Além disso, *a Configuração Telemettry* não está totalmente configurada quando faz login a partir de **Program.cs** ou **Startup.cs** si mesma. Assim, esses registos terão uma configuração mínima que utiliza o InMemoryChannel, sem amostragem, e sem inicializadores ou processadores de telemetria padrão.

Os exemplos que se seguem demonstram esta capacidade com **Program.cs** e **Startup.cs.**

#### <a name="example-programcs"></a>Exemplo Program.cs

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
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Exemplo Startup.cs

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrar do antigo ApplicationInsightsLoggerProvider

As versões Microsoft.ApplicationInsights.AspNet SDK antes do 2.7.1 suportavam um fornecedor de registo sintetizado que agora está obsoleto. Este fornecedor foi ativado através do método de extensão **AddApplicationInsights()** do ILoggerFactory. Recomendamos que emigra para o novo fornecedor, que envolve duas etapas:

1. Remova a chamada *iLoggerFactory.AddApplicationInsights()* do método **Startup.Configure()** para evitar o duplo registo.
2. Reaplicar quaisquer regras de filtragem em código, uma vez que não serão respeitadas pelo novo fornecedor. As sobrecargas de *ILoggerFactory.AddApplicationInsights()* tomaram funções mínimas de LogLevel ou filtro. Com o novo fornecedor, a filtragem faz parte do próprio quadro de exploração madeireira. Não é feito pelo fornecedor de Insights de Aplicação. Assim, quaisquer filtros fornecidos através do *ILoggerFactory.AddApplicationInsights()* devem ser removidos. E as regras de filtragem devem ser fornecidas seguindo as instruções de nível de registo de [controlo.](#control-logging-level) Se utilizar as *definições de appsettings.json* para filtrar o registo, continuará a trabalhar com o novo fornecedor, porque ambos utilizam o mesmo pseudónimo de fornecedor, *ApplicationInsights*.

Ainda pode usar o antigo provedor. (Será removido apenas numa grande versão alterada para 3. *xx*.) Mas recomendamos que emigra para o novo fornecedor pelas seguintes razões:

- O fornecedor anterior carece de suporte para [os âmbitos](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes)de registo . No novo fornecedor, as propriedades de âmbito são automaticamente adicionadas como propriedades personalizadas à telemetria recolhida.
- Os registos podem agora ser capturados muito mais cedo no pipeline de startups de aplicações. Os registos das aulas de **Programa** e **Arranque** podem agora ser capturados.
- Com o novo fornecedor, a filtragem é feita ao nível-quadro em si. Pode filtrar os registos para o fornecedor de Insights de Aplicação da mesma forma que para outros fornecedores, incluindo fornecedores incorporados como consola, Debug, e assim por diante. Também pode aplicar os mesmos filtros a vários fornecedores.
- Em ASP.NET Core (2.0 e mais tarde), a forma recomendada de permitir os fornecedores de [exploração madeireira](https://github.com/aspnet/Announcements/issues/255) é utilizando métodos de extensão no ILoggingBuilder em **Program.cs** si mesmo.

> [!Note]
> O novo fornecedor está disponível para aplicações que visam netSTANDARD2.0 ou posteriormente. A partir da [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.14.0 em diante, o novo fornecedor também está disponível para aplicações que visam .NET Framework NET461 ou posteriormente. Se a sua aplicação tiver como alvo versões mais antigas .NET Core, tais como .NET Core 1.1, ou se tiver como alvo o Quadro .NET inferior ao NET46, continue a utilizar o antigo fornecedor.

## <a name="console-application"></a>Aplicação de consola

> [!NOTE]
> Existe uma nova aplicação Insights SDK chamada [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) que pode ser usada para ativar insights de aplicação (iLogger e outras telemetrias de Insights de Aplicação) para quaisquer Aplicações de Consola. Recomenda-se utilizar este pacote e instruções associadas a partir [daqui](../../azure-monitor/app/worker-service.md).

O código seguinte mostra uma aplicação de consola de amostra seletiva configurada para enviar vestígios iLogger para Application Insights.

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
        // Create the DI container.
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
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
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

Este exemplo utiliza o `Microsoft.Extensions.Logging.ApplicationInsights`pacote autónomo . Por padrão, esta configuração utiliza a Configuração Telemettry "bare minimum" para o envio de dados para Insights de Aplicação. O mínimo significa que o InMemoryChannel é o canal que é utilizado. Não há amostragem nem telemetria. Este comportamento pode ser ultrapassado para uma aplicação de consola, como mostra o exemplo seguinte.

Instale este pacote adicional:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A secção seguinte mostra como anular a Configuração DeTelemettry padrão utilizando os **serviços. Configure\<o método de configuração da configuração da configuração da configuração>().** Este exemplo configura-se `ServerTelemetryChannel` e amostragem. Adiciona um ITelemettryInitializer personalizado à Configuração telemetria.

```csharp
    // Create the DI container.
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

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Nível de exploração madeireira de controlo

O ASP.NET o infra Core *ILogger* tem um mecanismo incorporado para aplicar [a filtragem de troncos](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). Isto permite-lhe controlar os registos enviados a cada fornecedor registado, incluindo o fornecedor de Informações de Aplicação. A filtragem pode ser feita na configuração (normalmente utilizando um ficheiro *appsettings.json)* ou em código. Esta instalação é fornecida pelo próprio quadro. Não é específico para o fornecedor de Insights de Aplicação.

Os seguintes exemplos aplicam regras de filtro ao ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Criar regras de filtro na configuração com appssettings.json

Para applicationInsightsLoggerProvider, o pseudónimo `ApplicationInsights`do fornecedor é . A seguinte secção de *appssettings.json* instrui os fornecedores de registo geralmente a iniciar o login no nível *de aviso* e acima. Em seguida, `ApplicationInsightsLoggerProvider` substitui as categorias de log que começam com "Microsoft" no nível *Erro* e acima.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Criar regras de filtro em código

Os seguintes códigos de snippet configuram os registos de *Aviso* e acima de todas as `ApplicationInsightsLoggerProvider`categorias e para *erro* e acima de categorias que começam com "Microsoft" a serem enviados para . Esta configuração é a mesma que na secção anterior em *appssettings.json*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Quais são as versões antigas e novas do ApplicationInsightsLoggerProvider?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) incluiu um ApplicationInsightsLoggerProvider incorporado (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), que foi ativado através de métodos de extensão **ILoggerFactory.** Este fornecedor é marcado obsoleto a partir da versão 2.7.1. Será removido completamente na próxima grande versão alterada. O pacote [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) em si não é obsoleto. É necessário permitir a monitorização de pedidos, dependências, e assim por diante.

A alternativa sugerida é o novo pacote autónomo [Microsoft.Extensions.ApplicationInsights,](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)que contém uma aplicação melhoradaInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsights.ApplicationInsightsLoggerProvider) e métodos de extensão no ILoggerBuilder para o permitir.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.7.1 assume uma dependência do novo pacote e permite a captura automática do ILogger.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Por que alguns registos ILogger são mostrados duas vezes em Insights de Aplicação?

A duplicação pode ocorrer se tiver a versão mais antiga (agora obsoleta) do ApplicationInsightsLoggerProvider ativada por chamada `AddApplicationInsights` . `ILoggerFactory` Verifique se o seu método **Configure** tem o seguinte e remova-o:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Se experimentar o duplo registo quando depura `EnableDebugLogger` do Visual Studio, desative-o para *falso* no código que permite a Aplicação Insights, da seguinte forma. Esta duplicação e correção só são relevantes quando se está a depurar a aplicação.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Atualizei para [microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.7.1, e os registos do ILogger são capturados automaticamente. Como desligo completamente esta funcionalidade?

Consulte a secção de [nível](../../azure-monitor/app/ilogger.md#control-logging-level) de registo de controlo para ver como filtrar os registos em geral. Para desligar o ApplicationInsightsLoggerProvider, utilize: `LogLevel.None`

**Em código:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**Em config:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Por que alguns registos ILogger não têm as mesmas propriedades que outros?

Application Insights captura e envia registos ILogger usando a mesma Configuração de Telemetria que é usada para todas as outras telemetrias. Mas há uma exceção. Por predefinição, a Configuração Telemettry não está totalmente configurada quando faz login a partir de **Program.cs** ou **Startup.cs**. Os registos destes locais não terão a configuração padrão, por isso não estarão a executar todos os TelemettryInitializers e TelemettryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Estou a usar o pacote autónomo Microsoft.Extensions.Logging.ApplicationInsights, e quero registar uma telemetria personalizada adicional manualmente. Como devo fazer isto?

Quando utilizar o pacote `TelemetryClient` autónomo, não é injetado no recipiente DI, pelo `TelemetryClient` que tem de criar uma nova instância e utilizar a mesma configuração que o fornecedor de madeireiros utiliza, como mostra o seguinte código. Isto garante que a mesma configuração é usada para toda a telemetria personalizada, bem como para a telemetria do ILogger.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Se utilizar o pacote Microsoft.ApplicationInsights.AspNetCore para ativar os `TelemetryClient` Insights de Aplicação, modifique este código para entrar diretamente no construtor. Por exemplo, consulte [este FAQ](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Que tipo de telemetria Application Insights é produzido a partir de registos ILogger? Ou onde posso ver os registos do ILogger em Insights de Aplicação?

ApplicationInsightsLoggerProvider captura registos ILogger e cria TraceTelemettry a partir deles. Se um objeto de exceção for passado para o método **log()** no ILogger, *a ExceptionTelemetria* é criada em vez da TraceTelemetria. Estes itens de telemetria podem ser encontrados em mesmos lugares que qualquer outro TraceTelemetria ou ExceptionTelemettry para Application Insights, incluindo portal, analytics ou Debugger local do Estúdio Visual.

Se preferir sempre enviar TraceTelemettry, use este corte:```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Não tenho o SDK instalado e uso a extensão de Web Apps Azure para permitir insights de aplicação para as minhas aplicações core ASP.NET. Como uso o novo fornecedor? 

A extensão de Insights de Aplicação em Web Apps Azure utiliza o novo fornecedor. Pode modificar as regras de filtragem no ficheiro *appsettings.json* para a sua aplicação.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Estou a usar o pacote autónomo Microsoft.Extensions.LoggingInsights.ApplicationInsights e a ativar o fornecedor de Insights de Aplicação, chamando **o construtor. AddApplicationInsights ("ikey")**. Existe alguma opção para obter uma chave de instrumentação a partir da configuração?


Modificar Program.cs e appsettings.json da seguinte forma:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   Secção relevante `appsettings.json`de:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Este código só é exigido quando utilizar um fornecedor de registo autónomo. Para monitorização regular de Insights de Aplicação, a chave de instrumentação é carregada automaticamente a partir do caminho de configuração *ApplicationInsights: Instrumentationkey*. As definições de apps.json devem ser assim:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:

* [Exploração madeireira no núcleo ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [.NET rastreios em Insights de Aplicação](../../azure-monitor/app/asp-net-trace-logs.md)
