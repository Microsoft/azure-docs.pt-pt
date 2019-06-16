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
ms.openlocfilehash: 0691c35661a6d185a6aa5ed3383ad600653359d3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058592"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider para .NET Core ILogger registos

ASP.NET Core suporta uma API de log que funciona com diferentes tipos de provedores de registro incorporadas e de terceiros. Registo é feito chamando **Log()** ou uma variante do mesmo no *ILogger* instâncias. Este artigo demonstra como usar *ApplicationInsightsLoggerProvider* para capturar os registos de ILogger na consola e aplicativos do ASP.NET Core. Este artigo também descreve como ApplicationInsightsLoggerProvider integra-se com outra telemetria do Application Insights.
Para obter mais informações, consulte [iniciar sessão no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Aplicativos do ASP.NET Core

ApplicationInsightsLoggerProvider está ativada por predefinição no [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 de versão (e versões posteriores) quando Ativar monitorização do Application Insights regulares através de um padrão métodos:
- Ao chamar o **UseApplicationInsights** método de extensão no IWebHostBuilder 
- Ao chamar o **AddApplicationInsightsTelemetry** método de extensão no IServiceCollection

Os registos de ILogger que captura ApplicationInsightsLoggerProvider estão sujeitos a mesma configuração como qualquer outra telemetria que é recolhida. Têm o mesmo conjunto de TelemetryInitializers e TelemetryProcessors, utilize o mesmo TelemetryChannel e são correlacionados e amostragem da mesma forma que outras telemetria. Se usar a versão 2.7.0-beta3 ou mais tarde, é necessária para capturar os registos de ILogger nenhuma ação.

Apenas *aviso* ou superior ILogger registos (a partir de todas as categorias) são enviados para o Application Insights, por predefinição. Mas pode [aplicar filtros para modificar esse comportamento](#control-logging-level). São necessários passos adicionais para capturar ILogger registos a partir **Program.cs** ou **Startup.cs**. (Consulte [ILogger captura inicia a partir de Startup.cs e Program.cs em aplicativos do ASP.NET Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Se usar uma versão anterior do SDK de Microsoft.ApplicationInsights.AspNet ou desejar usar apenas ApplicationInsightsLoggerProvider sem quaisquer outros monitorização do Application Insights, utilize o seguinte procedimento:

1. Instale o pacote NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Modificar **Program.cs** conforme mostrado aqui:

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

O código no passo 2 configura `ApplicationInsightsLoggerProvider`. O código seguinte mostra um exemplo de classe de controlador, que usa `ILogger` para enviar registos. Os registos são capturados pelo Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Capture registos ILogger de Startup.cs e Program.cs em aplicativos do ASP.NET Core

> [!NOTE]
> No ASP.NET Core 3.0 e versões posteriores, já não é possível inserir `ILogger` no Startup.cs e Program.cs. Consulte https://github.com/aspnet/Announcements/issues/353 para obter mais detalhes.

O novo ApplicationInsightsLoggerProvider pode capturar os registos no início do pipeline de inicialização do aplicativo. Embora ApplicationInsightsLoggerProvider é ativada automaticamente no Application Insights (começando com a versão 2.7.0-beta3), ele não tem uma chave de instrumentação definida até mais tarde no pipeline. Assim, apenas inicia a partir de **controlador**/ outras classes serão capturados. Para capturar todos os registos a partir **Program.cs** e **Startup.cs** em si, tem de ativar explicitamente uma chave de instrumentação para ApplicationInsightsLoggerProvider. Além disso, *TelemetryConfiguration* não está completamente configurado quando iniciar sessão a partir **Program.cs** ou **Startup.cs** em si. Portanto, esses logs tiver uma configuração mínima que utiliza InMemoryChannel, nenhum amostragem e não os inicializadores de telemetria standard ou processadores.

Os exemplos seguintes demonstram esta capacidade com **Program.cs** e **Startup.cs**.

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrar a partir do antigo ApplicationInsightsLoggerProvider

Versões do SDK de Microsoft.ApplicationInsights.AspNet para que 2.7.0-beta2 suportado um provedor de log que está agora obsoleta. Esse provedor foi ativado através da **AddApplicationInsights()** método de extensão de ILoggerFactory. Recomendamos que migre para o novo fornecedor, o que envolve dois passos:

1. Remover o *ILoggerFactory.AddApplicationInsights()* chamar a partir do **Startup.Configure()** método para evitar o Registro em log duplo.
2. Volte a aplicar regras de filtragem no código, porque eles não serão respeitados pelo novo fornecedor. Sobrecarrega dos *ILoggerFactory.AddApplicationInsights()* demorou mínimo funções LogLevel ou um filtro. Com o novo fornecedor, a filtragem é parte da arquitetura de registo em si. Não é feita pelo provedor do Application Insights. Portanto, quaisquer filtros que são fornecidos através de *ILoggerFactory.AddApplicationInsights()* sobrecargas devem ser removidas. E regras de filtragem devem ser fornecida ao seguir a [controlar o nível de registo](#control-logging-level) instruções. Se usar *appSettings* para filtrar o Registro em log, irá continuar a trabalhar com o novo fornecedor, uma vez que ambos utilizam o alias do fornecedor mesmo, *Application Insights*.

Pode continuar a utilizar o fornecedor de antigo. (Será removido apenas numa alteração de versão principal para 3. *xx*.) Mas recomendamos que migre para o novo provedor pelos seguintes motivos:

- O fornecedor de anterior não oferece suporte para [âmbitos de registo](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). No novo fornecedor, propriedades de âmbito são adicionadas automaticamente como propriedades personalizadas à telemetria recolhida.
- Agora podem ser capturados registos muito mais antecedência no pipeline de inicialização do aplicativo. Inicia a partir da **programa** e **arranque** classes agora podem ser capturadas.
- Com o novo fornecedor, a filtragem é feita ao nível da estrutura em si. Pode filtrar os registos para o fornecedor do Application Insights da mesma forma como para outros fornecedores, incluindo provedores internos, como o Console, depuração e assim por diante. Também pode aplicar os mesmos filtros para vários fornecedores.
- No ASP.NET Core (2.0 e versões posteriores), a forma recomendada para [os fornecedores de registo](https://github.com/aspnet/Announcements/issues/255) é usando métodos de extensão no ILoggingBuilder na **Program.cs** em si.

> [!Note]
> O novo provedor está disponível para aplicativos que se destinam a NETSTANDARD2.0 ou posterior. Se a sua aplicação versões mais antigas do .NET Core, por exemplo, o .NET Core 1.1, ou se ele destinada ao .NET Framework, continue a utilizar o fornecedor de antigo.

## <a name="console-application"></a>Aplicação de consola

O código seguinte mostra um exemplo de aplicação de consola que está configurado para enviar os rastreios de ILogger para o Application Insights.

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

Este exemplo utiliza o pacote autónomo `Microsoft.Extensions.Logging.ApplicationInsights`. Por predefinição, esta configuração utiliza o "mínimo" TelemetryConfiguration para enviar dados para o Application Insights. Mínimo significa que InMemoryChannel é o canal que está a ser utilizado. Não há nenhum TelemetryInitializers sampling e não padrão. Este comportamento pode ser substituído para um aplicativo de console, como mostrado no exemplo seguinte.

Instale este pacote adicional:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A seção a seguir mostra como substituir a predefinição TelemetryConfiguration utilizando o **serviços. Configurar<TelemetryConfiguration>()** método. Este exemplo configura `ServerTelemetryChannel` e amostragem. Ele adiciona um ITelemetryInitializer personalizado para o TelemetryConfiguration.

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

## <a name="control-logging-level"></a>Nível de registo de controlo

O ASP.NET Core *ILogger* infra-estrutura tem um mecanismo interno para aplicar [filtragem de log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). Isto permite-lhe controlar os registos que são enviados para cada fornecedor registado, incluindo o fornecedor do Application Insights. A filtragem pode ser feita na configuração (normalmente, ao utilizar um *appSettings* ficheiros) ou no código. Esse recurso é fornecido pela própria estrutura. Não é específico para o fornecedor do Application Insights.

Os exemplos seguintes aplicam-se as regras de filtro para ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Criar regras de filtro na configuração com appSettings

Para ApplicationInsightsLoggerProvider, o alias do fornecedor é `ApplicationInsights`. A próxima seção *appSettings* configura os registos para *aviso* e acima de todas as categorias e *erro* e acima de categorias que começam com " Microsoft"a serem enviados para `ApplicationInsightsLoggerProvider`.

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

O fragmento de código seguinte configura os registos para *aviso* e acima de todas as categorias e para *erro* e acima de categorias que começam por "Microsoft" a serem enviados para `ApplicationInsightsLoggerProvider`. Esta configuração é o mesmo como na seção anterior na *appSettings*.

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

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Quais são as versões antigas e novas dos ApplicationInsightsLoggerProvider?

[SDK de Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) incluído um ApplicationInsightsLoggerProvider incorporada (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), que foi ativada através de  **ILoggerFactory** métodos de extensão. Este fornecedor está marcado como obsoleto de 2.7.0-beta2 de versão. Será removido completamente na próxima alteração de versão principal. O [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) pacote em si não está obsoleto. É obrigatório para ativar a monitorização de pedidos, dependências e assim por diante.

A alternativa sugerida é o novo pacote autónomo [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), que contém um (ApplicationInsightsLoggerProvider melhorada Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) e métodos de extensão no ILoggerBuilder para ativá-la.

[SDK de Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.7.0-beta3 utiliza uma dependência num novo pacote e permite ILogger captura automaticamente.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Por que alguns registos de ILogger são apresentados duas vezes no Application Insights?

Duplicação pode ocorrer se tiver a versão mais antiga (agora obsoleta) de ApplicationInsightsLoggerProvider ativada chamando `AddApplicationInsights` no `ILoggerFactory`. Verifique se o seu **configurar** tem o seguinte método e removê-lo:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Se tiver um registo duplo ao depurar a partir do Visual Studio, defina `EnableDebugLogger` para *false* no código que permite-Application Insights, da seguinte forma. Essa duplicação e a correção é relevante apenas quando estiver a depurar a aplicação.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Eu atualizada para [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 de versão e registos de ILogger são capturados automaticamente. Como desativar esta funcionalidade completamente?

Consulte a [controlar o nível de registo](../../azure-monitor/app/ilogger.md#control-logging-level) secção para ver como filtrar registos em geral. Para desative ApplicationInsightsLoggerProvider, utilize `LogLevel.None`:

**No código:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**Na configuração:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Por que alguns registos de ILogger não têm as mesmas propriedades como outras pessoas?

O Application Insights captura e envia ILogger registos ao utilizar o mesmo TelemetryConfiguration que é utilizado para todos os outra telemetria. Mas há uma exceção. Por predefinição, TelemetryConfiguration não está completamente configurado quando iniciar sessão a partir **Program.cs** ou **Startup.cs**. Registos a partir desses lugares não terão da configuração predefinida, pelo que não será executado todos os TelemetryInitializers e TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Estou usando o pacote autônomo Microsoft.Extensions.Logging.ApplicationInsights e quero iniciar alguma telemetria personalizada adicional manualmente. Como posso fazer isso?

Quando utiliza o pacote de autónomo `TelemetryClient` não é injetado para o contêiner de injeção de dependência, por isso terá de criar uma nova instância do `TelemetryClient` e utilizar a mesma configuração de como as utilizações de fornecedor de agente de log, como o seguinte código mostra. Isto garante que a mesma configuração é utilizada para todos os telemetria personalizada, bem como telemetria a partir de ILogger.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
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
> Se utilizar o pacote de Microsoft.ApplicationInsights.AspNetCore para ativar o Application Insights, modifique este código para obter `TelemetryClient` diretamente no construtor. Por exemplo, veja [nestas perguntas frequentes](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Que tipo de telemetria do Application Insights é produzido a partir de registos de ILogger? Ou onde posso ver que registos de ILogger no Application Insights?

ApplicationInsightsLoggerProvider captura os registos de ILogger e cria TraceTelemetry dos mesmos. Se um objeto de exceção é passado para o **Log()** método no ILogger, *ExceptionTelemetry* é criado em vez de TraceTelemetry. Estes itens de telemetria podem ser encontrados nos mesmos locais como qualquer outro TraceTelemetry ou ExceptionTelemetry para o Application Insights, incluindo o portal, o analytics ou o depurador do Visual Studio local.

Se preferir enviar sempre TraceTelemetry, utilize este trecho de código: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Não tenho o SDK instalado, e posso utilizar a extensão de aplicações Web do Azure para ativar o Application Insights para meus aplicativos do ASP.NET Core. Como posso utilizar o novo fornecedor? 

A extensão do Application Insights em aplicações Web do Azure utiliza o fornecedor antigo. Pode modificar as regras de filtragem no *appSettings* ficheiro para a sua aplicação. Para tirar partido do novo fornecedor, utilize a instrumentação do tempo de compilação, efetuando uma dependência de NuGet no SDK. Este artigo será atualizado quando a extensão é comutado para utilizar o novo provedor.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Estou utilizando o pacote autônomo Microsoft.Extensions.Logging.ApplicationInsights e ativar o fornecedor do Application Insights ao chamar **builder. AddApplicationInsights("ikey")** . Existe uma opção para obter uma chave de instrumentação a partir da configuração?


Modificar Program.cs e appSettings da seguinte forma:

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

   Secção relevante de `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Esse código é necessário apenas quando usar um provedor de Registro em log autónomo. Para a monitorização do Application Insights regulares, a chave de instrumentação é carregada automaticamente do caminho de configuração *Application Insights: Instrumentationkey*. AppSettings deve ter este aspeto:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre:

* [Iniciar sessão no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Registos de rastreio de .NET no Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
