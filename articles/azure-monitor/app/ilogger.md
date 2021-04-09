---
title: Explore registos de traços .NET com ILogger - Azure Application Insights
description: Amostras de utilização do fornecedor Azure Application Insights ILogger com aplicações ASP.NET Core e Consola.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e7d0bd6f7d93eac944e0d53a8e898463bf9887f9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592273"
---
# <a name="applicationinsightsloggerprovider-for-microsoftextensionlogging"></a>ApplicationInsightsLoggerProvider para Microsoft.Extension.Logging

Este artigo demonstra como usar `ApplicationInsightsLoggerProvider` para capturar `ILogger` registos nas aplicações core da consola e ASP.NET.
Para saber mais registos, consulte [iniciar sessão no ASP.NET Core](/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>aplicações ASP.NET Core

`ApplicationInsightsLoggerProvider`é ativado por padrão para aplicações ASP.NET Core quando as visualizações de aplicações são configuradas usando [code](./asp-net-core.md) ou abordagem [sem código.](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring)

Apenas os registos *de advertência* e acima `ILogger` (de todas as [categorias)](/aspnet/core/fundamentals/logging/#log-category)são enviados para Insights de Aplicação por padrão. Mas pode [personalizar este comportamento.](./asp-net-core.md#how-do-i-customize-ilogger-logs-collection) São necessários passos adicionais para capturar registos ILogger do **Programa.cs** ou **Startup.cs**. (Ver [capturar registos ILogger de Startup.cs e Programa.cs em aplicações core ASP.NET](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Se pretender utilizar `ApplicationInsightsLoggerProvider` sem qualquer outra monitorização de Insights de Aplicação, utilize os seguintes passos.

1. Instale o pacote NuGet:

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
    </ItemGroup>
   ```

1. Modificar `Program.cs` como mostrado aqui:

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
                   builder.AddApplicationInsights("put-actual-ikey-here");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

O código no passo 2 configura `ApplicationInsightsLoggerProvider` . O código que se segue mostra uma classe de controlador de exemplo, que utiliza `ILogger` para enviar registos. Os registos são capturados por Application Insights.

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
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Capture registos ILogger de Startup.cs e Program.cs em aplicações ASP.NET Core

> [!NOTE]
> Em ASP.NET Core 3.0 e mais tarde, já não é possível injetar `ILogger` no .cs Startup.cs e Programa. Veja https://github.com/aspnet/Announcements/issues/353 mais detalhes.

`ApplicationInsightsLoggerProvider` pode capturar registos a partir do início da aplicação. Embora o ApplicationInsightsLoggerProvider esteja automaticamente ativado em Application Insights (a começar pela versão 2.7.1), não tem uma chave de instrumentação configurada até mais tarde no pipeline. Assim, apenas os registos do **Controlador**/outras classes serão capturados. Para capturar todos os registos que começam com **o Programa.cs** e **o próprio .cs Startup,** tem de ativar explicitamente uma chave de instrumentação para o ApplicationInsightsLoggerProvider. Além disso, *a TelemetriaConfiguration* não está totalmente configurada quando se regista a partir do **Programa.cs** ou **Startup.cs** si próprio. Assim, estes registos terão uma configuração mínima que utiliza [o InMemoryChannel](./telemetry-channels.md), sem [amostragem,](./sampling.md)e sem [inicializadores ou processadores de telemetria](./api-filtering-sampling.md)padrão.

Os exemplos que se seguem demonstram esta capacidade com **o Programa.cs** e **Startup.cs**.

#### <a name="example-programcs"></a>Programa Exemplo.cs

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

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrar do antigo ApplicationInsightsLoggerProvider

Microsoft.ApplicationInsights.AspNet SDK antes do 2.7.1 suportava um fornecedor de registos que agora está obsoleto. Este fornecedor foi ativado através do método de extensão **AddApplicationInsights()** do ILoggerFactory. Recomendamos que emigre para o novo provedor, que envolve dois passos:

1. Remova a chamada *ILoggerFactory.AddApplicationInsights ()* do método **Startup.Configure()** para evitar a dupla exploração.
2. Recandidatar as regras de filtragem em código, uma vez que não serão respeitadas pelo novo fornecedor. Sobrecargas de *ILoggerFactory.AddApplicationInsights()* tomaram funções mínimas de LogLevel ou filtro. Com o novo fornecedor, a filtragem faz parte da própria estrutura de registo. Não é feito pelo fornecedor application insights. Assim, quaisquer filtros fornecidos via *ILoggerFactory.AddApplicationInsights()* sobrecargas devem ser removidos. E as regras de filtragem devem ser fornecidas seguindo as instruções do [nível de registo](#control-logging-level) do controlo. Se utilizar *appsettings.js* para filtrar a madeira, continuará a trabalhar com o novo fornecedor, pois ambos utilizam o mesmo pseudónimo do fornecedor, *ApplicationInsights*.

Ainda pode usar o antigo provedor. (Só será removido numa versão importante para 3. *xx.)* Mas recomendamos que migrar para o novo fornecedor pelas seguintes razões:

- O fornecedor anterior carece de suporte para [os âmbitos de registo.](/aspnet/core/fundamentals/logging#log-scopes) No novo fornecedor, as propriedades de âmbito são automaticamente adicionadas como propriedades personalizadas à telemetria recolhida.
- Os registos podem agora ser capturados muito mais cedo no pipeline de arranque de aplicações. Os registos das aulas **de Programa** e **Startup** podem agora ser capturados.
- Com o novo fornecedor, a filtragem é feita ao nível do quadro em si. Pode filtrar registos para o fornecedor Application Insights da mesma forma que para outros fornecedores, incluindo fornecedores incorporados como Consola, Debug, e assim por diante. Também pode aplicar os mesmos filtros a vários fornecedores.
- Em ASP.NET Core (2.0 e posterior), a forma recomendada de  [permitir os fornecedores de registo](https://github.com/aspnet/Announcements/issues/255) é utilizando métodos de extensão no ILoggingBuilder no **programa.cs** si próprio.

> [!Note]
> O novo fornecedor está disponível para aplicações que visam NETSTANDARD2.0 ou posterior. A partir da [microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.14.0 em diante, o novo fornecedor também está disponível para aplicações que visam .NET Framework NET461 ou posterior. Se a sua aplicação tiver como alvo versões mais antigas .NET Core, como .NET Core 1.1, ou se tiver como alvo o Quadro .NET inferior ao NET46, continue a utilizar o fornecedor antigo.

## <a name="console-application"></a>Aplicação de consola

> [!NOTE]
> Existe um novo SDK de Insights de Aplicação chamado [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) que pode ser utilizado para permitir insights de aplicação (ILogger e outras telemetria de Insights de Aplicações) para quaisquer aplicações de consola. Recomenda-se a utilização deste pacote e instruções associadas [a](./worker-service.md)partir daqui.

Se pretender utilizar apenas o ApplicationInsightsLoggerProvider sem qualquer outra monitorização de Insights de Aplicação, utilize os seguintes passos.

Pacotes instalados:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
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

        logger.LogInformation("Logger is working");

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

Este exemplo utiliza o pacote `Microsoft.Extensions.Logging.ApplicationInsights` autónomo. Por predefinição, esta configuração utiliza a "mínima" Configuração de Telemetria para o envio de dados para Insights de Aplicação. O mínimo significa que o InMemoryChannel é o canal que é usado. Não há amostragem nem telemetriainitializadores padrão. Este comportamento pode ser ultrapassado para uma aplicação de consola, como mostra o exemplo a seguir.

Instale este pacote adicional:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A seguinte secção mostra como anular a configuração padrão da telemetria utilizando o método **services.Configure \<TelemetryConfiguration> ().** Este exemplo cria-se `ServerTelemetryChannel` e experimenta. Adiciona um ITelemetryInitializador personalizado à Configuração de Telemetria.

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
    
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Nível de registo de controlo

`ILogger` tem um mecanismo incorporado para aplicar [a filtragem de registos](/aspnet/core/fundamentals/logging#log-filtering). Isto permite-lhe controlar os registos que são enviados a cada fornecedor registado, incluindo o fornecedor de Insights de Aplicação. A filtragem pode ser feita na configuração (normalmente utilizando uma *appsettings.jsno* ficheiro) ou em código.

Os exemplos a seguir mostram como aplicar regras de filtro a `ApplicationInsightsLoggerProvider` .

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Crie regras de filtro na configuração com appsettings.jsligado

Para applicationInsightsLoggerProvider, o pseudónimo do fornecedor é `ApplicationInsights` . A seguinte secção de *appsettings.jsinstrui* os fornecedores de registo geralmente a registarem-se ao nível *aviso* e acima. Em seguida, substitui as `ApplicationInsightsLoggerProvider` categorias de registo para registar que começam com "Microsoft" no nível *Erro* e acima.

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

O seguinte código corta registos de *Aviso* e acima de todas as categorias e de *Erro* e acima de categorias que começam com "Microsoft" a enviar para `ApplicationInsightsLoggerProvider` . Esta configuração é a mesma da secção anterior em *appsettings.jsem*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="logging-scopes"></a>Âmbitos de registo

`ApplicationInsightsLoggingProvider` suporta [Âmbitos](/aspnet/core/fundamentals/logging#log-scopes) de registo e âmbitos são ativados por padrão.

Se o âmbito for do `IReadOnlyCollection<KeyValuePair<string,object>>` tipo, então cada par de valores-chave na coleção é adicionado à telemetria de insights de aplicação como propriedades personalizadas. No exemplo abaixo, os registos serão capturados como `TraceTelemetry` e terão ("MyKey", "MyValue") em propriedades.

```csharp
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
    {
        _logger.LogError("An example of an Error level message");
    }
```

Se qualquer outro tipo for utilizado como Scope, então serão armazenados sob a propriedade "Scope" em telemetria de insights de aplicação. No exemplo abaixo, o `TraceTelemetry` terá uma propriedade chamada "Scope" contendo o âmbito.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Quais são as versões antigas e novas do ApplicationInsightsLoggerProvider?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) incluiu um ApplicationInsightsLoggerProvider incorporado (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), que foi ativado através de métodos de extensão **ILoggerFactory.** Este fornecedor está marcado como obsoleto a partir da versão 2.7.1. Será removido completamente na próxima grande mudança de versão. O pacote [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) em si não é obsoleto. É necessário para permitir a monitorização de pedidos, dependências, e assim por diante.

A alternativa sugerida é o novo pacote autónomo [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), que contém um ApplicationInsightsLoggerProvider melhorado (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) e métodos de extensão no ILoggerBuilder para o permitir.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.7.1 assume uma dependência do novo pacote e permite a captura de ILogger automaticamente.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Por que alguns registos ILogger são mostrados duas vezes em Application Insights?

A duplicação pode ocorrer se tiver a versão mais antiga (agora obsoleta) do ApplicationInsightsLoggerProvider ativada através da chamada `AddApplicationInsights` `ILoggerFactory` . Verifique se o seu método **de Configuração** tem o seguinte, e remova-o:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Se tiver registo duplo quando depurar do Visual Studio, `EnableDebugLogger` desative-o no código que permite insights de aplicação, da seguinte forma.  Esta duplicação e correção só é relevante quando se está a depurar a aplicação.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Atualizei para [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.7.1 e os registos do ILogger são capturados automaticamente. Como desativar completamente esta funcionalidade?

Consulte a secção [de nível de registo de registo de controlo](#control-logging-level) para ver como filtrar registos em geral. Para desligar o ApplicationInsightsLoggerProvider, `LogLevel.None` utilize:

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

O Application Insights captura e envia registos ILogger usando a mesma TelemetriaConfiguration que é usada para qualquer outra telemetria. Mas há uma exceção. Por padrão, a TelemetriaConfiguration não está totalmente configurada quando se regista a partir do **Programa.cs** ou **Startup.cs**. Os registos destes locais não terão a configuração padrão, pelo que não estarão a executar todos os TelemetriaInitializers e TelemetriaProcessadores.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Estou a usar o pacote autónomo Microsoft.Extensions.Logging.ApplicationInsights, e quero registar manualmente algumas telemetrias personalizadas adicionais. Como devo fazer isto?

Quando utilizar o pacote autónomo, `TelemetryClient` não é injetado no recipiente DI, pelo que é necessário criar uma nova `TelemetryClient` instância e utilizar a mesma configuração que o fornecedor do madeireiro utiliza, como mostra o código que se segue. Isto garante que a mesma configuração é usada para toda a telemetria personalizada, bem como telemetria da ILogger.

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
> Se utilizar o pacote Microsoft.ApplicationInsights.AspNetCore para ativar o Application Insights, modifique este código para entrar `TelemetryClient` diretamente no construtor. Por exemplo, consulte [este FAQ.](./asp-net-core.md#frequently-asked-questions)


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Que tipo de telemetria Application Insights é produzido a partir de registos ILogger? Ou onde posso ver os registos do ILogger no Application Insights?

ApplicationInsightsLoggerProvider captura registos ILogger e cria TraceTelemetry a partir deles. Se um objeto de exceção for transmitido ao `Log` método `ILogger` em , *ExceptionTelemetry* é criado em vez de TraceTelemetry. Estes itens de telemetria podem ser encontrados nos mesmos locais que qualquer outro TraceTelemetry ou ExceptionTelemetry for Application Insights, incluindo portal, analytics ou Visual Studio local depurir.

Se preferir enviar sempre TraceTelemetry, use este corte: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Não tenho o SDK instalado, e uso a extensão de Aplicações Web Azure para ativar o Application Insights para as minhas aplicações core ASP.NET. Como uso o novo fornecedor? 

A extensão De Insights de Aplicação em Azure Web Apps utiliza o novo fornecedor. Pode modificar as regras de filtragem do *appsettings.jsno* ficheiro para a sua aplicação.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Estou a usar o pacote autónomo Microsoft.Extensions.Logging.ApplicationInsights e habilitar o fornecedor de Insights de Aplicações chamando **o construtor. AddApplicationInsights("ikey")**. Existe uma opção para obter uma chave de instrumentação da configuração?


Modificar o Programa.cs e appsettings.jsda seguinte forma:

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

   Secção relevante a partir `appsettings.json` de:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Este código só é necessário quando utilizar um fornecedor de registo autónomo. Para uma monitorização regular dos Insights de Aplicação, a tecla de instrumentação é carregada automaticamente a partir do caminho de configuração *ApplicationInsights: InstrumentationKey*. Appsettings.jsdeve ficar assim:

   ```json
   {
     "ApplicationInsights":
       {
           "InstrumentationKey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:

* [Iniciar sessão no núcleo ASP.NET](/aspnet/core/fundamentals/logging)
* [.NET registos de vestígios em Insights de Aplicação](./asp-net-trace-logs.md)

