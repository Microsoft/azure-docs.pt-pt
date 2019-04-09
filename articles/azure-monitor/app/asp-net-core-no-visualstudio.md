---
title: Azure Application Insights para ASP.NET Core sem o Visual Studio | Documentos da Microsoft
description: Monitorizar aplicações web de ASP.NET Core para disponibilidade, desempenho e utilização.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: cithomas
ms.openlocfilehash: 8243523887ec9861459b2d196126237cf89bad97
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288370"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights para aplicações ASP.NET Core

Este artigo descreve os passos para ativar o Application Insights para uma [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2) aplicativos sem usar o IDE do Visual Studio. Se tiver o IDE do Visual Studio instalado, em seguida, [isso](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) documento contém instruções específicas do Visual Studio. Ao concluir as instruções neste artigo, o Application Insights irá iniciar a recolha de pedidos, dependências, exceções, contadores de desempenho, heartbeats e registos da sua aplicação ASP.NET Core. A aplicação de exemplo utilizada é um [aplicação MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/?view=aspnetcore-2.2) direcionamento `netcoreapp2.2`, mas instruções aqui são aplicáveis a todas as aplicações do ASP.NET Core. Todas as exceções são realçadas quando aplicável.

## <a name="supported-scenarios"></a>Cenários suportados

Application Insights SDK (Software Development Kit) para o ASP.NET Core pode monitorizar as aplicações, independentemente de onde ou como o aplicativo é executado. Se seu aplicativo está em execução e tem conectividade de rede para o serviço do Application Insights, telemetria deve ser recolhidos. Este suporte inclui, mas não se limita a qualquer sistema operativo (Windows, Linux, Mac), o método de alojamento (vs em processo fora de processo), o método de implementação (dependente do framework o vs independente,) servidor Web (IIS, Kestrel), plataforma (aplicações Web do Azure, VM do Azure, Docker, AKS e assim por diante.) ou IDE (Visual Studio, o VS Code, linha de comandos).

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo de núcleo do ASP.NET funcional. Siga [isso](https://docs.microsoft.com/aspnet/core/getting-started/) guia para criar uma aplicação ASP.NET Core, se necessário.
- Uma válido instrumentação chave do Application Insights, que é necessária para enviar telemetria para o serviço do Application Insights. Siga [estes](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) instruções para criar um novo recurso do Application Insights, se for necessário e obter uma chave de instrumentação.

## <a name="enabling-application-insights-server-side-telemetry"></a>Ativar a telemetria de lado do servidor do Application Insights

1. Instale o SDK do Application Insights para ASP.NET Core, que é um pacote de NuGet regular. Recomenda-se para utilizar sempre a versão estável mais recente. Algumas das funcionalidades descritas neste artigo podem não estar disponíveis em versões mais antigas. Notas de versão completa para o SDK podem ser encontradas [aqui](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases). 

Seguinte mostra as alterações sejam adicionados ao arquivo. csproj de seu projeto.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
```

2. Adicione `services.AddApplicationInsightsTelemetry();` para `ConfigureServices()` método na sua `Startup` classe. Exemplo completo abaixo.

```csharp
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        // The following line enables Application Insights telemetry collection.
        services.AddApplicationInsightsTelemetry();

        // code adding other services for your application
        services.AddMvc();
    }
```

3. Chave de instrumentação do programa de configuração.

   Embora seja possível fornecer a chave de instrumentação como um argumento para `services.AddApplicationInsightsTelemetry("putinstrumentationkeyhere");`, é recomendado que especifique a chave de instrumentação na configuração. O seguinte mostra como especificar uma chave de instrumentação no `appsettings.json`. Certifique-se de `appsettings.json` é copiado para a pasta de raiz do aplicativo durante a publicação.

```json
    {
      "ApplicationInsights": {
        "InstrumentationKey": "putinstrumentationkeyhere"
      },
      "Logging": {
        "LogLevel": {
          "Default": "Warning"
        }
      }
    }
```

    Alternately, the instrumentation key can also be specified in either of the following environment variables.
    APPINSIGHTS_INSTRUMENTATIONKEY
    ApplicationInsights:InstrumentationKey

    Example:
    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

`APPINSIGHTS_INSTRUMENTATIONKEY` Normalmente, é utilizado para especificar a chave de instrumentação para aplicativos implantados em aplicações Web do Azure.

> [!NOTE]
> Uma chave de instrumentação especificada no código wins sobre a variável de ambiente `APPINSIGHTS_INSTRUMENTATIONKEY`, que o wins sobre outras opções.

4. Execute a sua aplicação e fazer pedidos à mesma. Telemetria deve começar a fluir para o Application Insights. A seguinte telemetria é automaticamente recolhida pelo Application Insights SDK.

    1. Pedidos - solicitações da web de entrada para a sua aplicação.
    1. Dependências
        1. Chamadas de HTTP/Https feitas com `HttpClient`
        1. Chamadas SQL feitas com `SqlClient`
        1. Chamadas de armazenamento do Azure feitas com [cliente de armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage/)*
        1. [SDK de cliente de EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) versão 1.1.0 e acima
        1. [SDK de cliente de ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) versão 3.0.0 e posterior

             * O azure Cosmos DB é controlado automaticamente apenas se for utilizada a HTTP/HTTPS. Modo TCP não ser capturado pelo Application Insights.


    1. [Contadores de Desempenho](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)
        1. Suporte para contadores de desempenho em ASP.NET Core é limitado ao seguinte
            1. SDK versão 2.4.1 e acima coletados contadores de desempenho se a aplicação está em execução numa aplicação Web do Azure (Windows)
            1. SDK versão 2.7.0-beta3 e acima coletados contadores de desempenho se a aplicação está em execução no Windows e direcionamento `NETSTANDARD2.0` ou superior.
            1. Para aplicativos visando o .NET Framework completo, os contadores de desempenho são suportados em todas as versões do SDK.

            Este documento será atualizado quando é adicionado suporte de contador de desempenho no Linux.
    1. [Métricas Dinâmicas](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)
    1. `ILogger` registos de gravidade `Warning` ou acima estão automaticamente capturada a partir do SDK versão 2.7.0-beta3 ou superior. Leia mais [aqui](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

Pode demorar alguns minutos para a telemetria começar a aparecer no portal. Para verificar rapidamente se tudo estiver funcionando, é melhor usar [métricas em direto](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream), enquanto efetuar pedidos para a aplicação em execução.

## <a name="send-ilogger-logs-to-application-insights"></a>Enviar registos de ILogger para o Application Insights

O Application Insights suporta captura os registos enviados por ILogger. Leia a documentação completa [aqui](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

## <a name="enable-client-side-telemetry-for-web-applications"></a>Ativar a telemetria de lado do cliente para aplicações Web

Os passos acima são suficientes para iniciar a recolha de telemetria de lado do servidor. Se seu aplicativo tiver componentes do lado do cliente, em seguida, siga os passos abaixo para começar a recolher [telemetria de utilização](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) a partir daí.

1. No _ViewImports.cshtml, adicione injeção:

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. No layout. cshtml, inserir HtmlHelper ao final do `<head>` secção, mas antes de qualquer outro script. Qualquer telemetria de JavaScript personalizada, que pretende relatório a partir da página deve ser inserida após este trecho de código:

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

Modificar os nomes de ficheiros de acordo com a aplicação real. Os nomes utilizados acima são a partir de um modelo de aplicativo MVC padrão.

## <a name="configuring-application-insights-sdk"></a>Configuração do Application Insights SDK

Application Insights SDK para ASP.NET Core podem ser personalizada para alterar a configuração predefinida. Os usuários do ASP.NET SDK do Application Insights podem estar familiarizados com a configuração através de `ApplicationInsights.config`, ou ao modificar `TelemetryConfiguration.Active`. Para o ASP.NET Core, a configuração é feita forma diferente. ASP.NET Core SDK é adicionado para a aplicação utilizando o interno do ASP.NET Core [DependencyInjection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) mecanismo e a configuração da mesma também usará DependencyInjection. Quase todas as alterações de configuração são feitas no `ConfigureServices()` método de sua `Startup.cs` de classe, salvo indicação em contrário. Siga as secções abaixo para saber mais.

> [!NOTE]
> É importante observar que a configuração de modificação, modificando `TelemetryConfiguration.Active` não é recomendada em aplicativos do ASP.NET Core.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>Configurar com ApplicationInsightsServiceOptions

É possível modificar algumas configurações comuns ao transmitir `ApplicationInsightsServiceOptions` para `services.AddApplicationInsightsTelemetry();`. Um exemplo é mostrado abaixo.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    }
```

A lista exata de definições configuráveis no `ApplicationInsightsServiceOptions` podem ser encontradas [aqui](https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/f0b8631e482d25982eb52092103b34e3ff6e5fef/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Amostragem

Application Insights SDK para ASP.NET Core suporta FixedRate e amostragem adaptável. Amostragem adaptável está ativada por predefinição. Siga [isso](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) documento para saber como configurar a amostragem para aplicativos do ASP.NET Core.

### <a name="adding-telemetryinitializers"></a>Adicionar TelemetryInitializers

Para adicionar um novo `TelemetryInitializer`, adicioná-lo para o contentor de DependencyInjection, conforme mostrado abaixo. `TelemetryInitializer`s adicionado ao contentor de DependencyInjection será detetado pelo SDK automaticamente.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Remover TelemetryInitializers

Remover todas ou específicas TelemetryInitializers, que estão presentes por predefinição, utilize o seguinte código de exemplo **após** chamar `AddApplicationInsightsTelemetry()`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in TelemetryInitializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetryprocessors"></a>Adicionar TelemetryProcessors

Processadores de telemetria personalizada podem ser adicionadas para o `TelemetryConfiguration` usando o método de extensão `AddApplicationInsightsTelemetryProcessor` no `IServiceCollection`. Utilize o seguinte exemplo.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configurar ou remover padrão TelemetryModules

Os seguintes módulos de coleção automática estão ativados por predefinição e são responsáveis pela recolha automática de telemetria. Pode ser desativados e configurados para alterar o comportamento predefinido.

1. `RequestTrackingTelemetryModule`
1. `DependencyTrackingTelemetryModule`
1. `PerformanceCollectorModule`
1. `QuickPulseTelemetryModule`
1. `AppServicesHeartbeatTelemetryModule`
1. `AzureInstanceMetadataTelemetryModule`

Para configurar qualquer padrão `TelemetryModule`, utilize o método de extensão `ConfigureTelemetryModule<T>` no `IServiceCollection` conforme mostrado no exemplo abaixo.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures DependencyTrackingTelemetryModule.
        // Similarly, any other default modules can be configured.
        services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
                        {
                            module.EnableW3CHeadersInjection = true;
                        });

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-telemetry-channel"></a>Configurar o canal de telemetria

O canal predefinido utilizado é o `ServerTelemetryChannel`. Ele pode ser substituído pelo exemplo a seguir abaixo.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

*1. Quero controlar algumas telemetria adicional para além da telemetria de automática recolhida. Como fazer isso?*

* Obter uma instância do `TelemetryClient` ao usando injeção de construtor e chamar necessários `TrackXXX()` método nele. Não é recomendado para criar novos `TelemetryClient` instâncias no aplicativo do ASP.NET Core, como uma instância singleton de `TelemetryClient` já está registado no contêiner de injeção de dependência, compartilha `TelemetryConfiguration` com rest de telemetria. Criar um novo `TelemetryClient` instância recomenda-se apenas se o ter uma configuração separada do restante de telemetria. O exemplo seguinte mostra como monitorizar a telemetria adicional a partir de um controlador.

```csharp
public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // use constructor injection to get TelemetryClient instance
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // call required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

 Consulte a [métricas personalizadas do Application Insights referência da API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/) para descrição dos dados personalizados a relatórios do Application Insights.

*2. Alguns modelos do Visual Studio usado o método de extensão UseApplicationInsights() no IWebHostBuilder para ativar o Application Insights. Esta utilização ainda é válida?*

* Ativar o Application Insights com este método é válido e é utilizado na integração do Visual Studio e também as extensões de aplicação Web do Azure. No entanto, é recomendado utilizar `services.AddApplicationInsightsTelemery()` pois fornece sobrecargas para controlar alguma configuração. Tanto o método internamente faz a mesma coisa, por isso, se não existe nenhuma configuração personalizada a ser aplicado, chamada qualquer uma serve.

*3. Eu esteja implantando a minha aplicação ASP.NET Core para aplicações Web do Azure. Deve eu ainda ativar a extensão do Application Insights a partir de aplicações Web?*

* Se o SDK é instalado no momento da compilação, conforme mostrado neste artigo, não é necessário ativar a extensão do Application Insights no portal Web Apps. Mesmo que a extensão estiver instalada, ele será retirada, quando ele detecta que o SDK já foi adicionado à aplicação. Ativar o Application Insights a partir da extensão permite que de instalação e atualização do SDK ao seu aplicativo. No entanto, a ativar o Application Insights de acordo com este artigo é mais flexível por motivos abaixo.
    1. Telemetria do Application Insights irá continuar a funcionar
        1. Todos os sistemas operativos - Windows, Linux, Mac.
        1. Publicar todas modo - autónomo ou dependente do Framework.
        1. Todas as estruturas de destino, incluindo o .NET Framework completo.
        1. Todas as opções de alojamento - aplicação Web do Azure, VMs, Linux, contentores, AKS, não pertencente ao Azure.
    1. Telemetria pode ser vista localmente, quando a depuração no Visual Studio.
    1. Permite o uso de telemetria personalizada adicional de controlo `TrackXXX()` API.
    1. Tem controlo total sobre a configuração.

*4. Posso ativar a monitorização do Application Insights usando ferramentas como o Monitor de estado?*

* Não. [Monitor de estado](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e a respetiva substituição futura [IISConfigurator](https://github.com/Microsoft/ApplicationInsights-Announcements/issues/21) atualmente suporta apenas o ASP.NET. O documento será atualizado quando o suporte para a aplicação ASP.NET Core está disponível.

*5. Tenho um aplicativo do ASP.NET Core 2.0? Não é o Application Insights ativado automaticamente para os mesmos sem que eu fazer nada?*

* `Microsoft.AspNetCore.All` 2.0 metapackage inclui o SDK do Application Insights (versão 2.1.0), e se executar o aplicativo sob o depurador do Visual Studio, Visual Studio permite que o application insights e mostra a telemetria localmente no IDE do próprio. Telemetria não foi enviada para o serviço do Application Insights, a menos que uma chave de instrumentação é especificada explicitamente. Recomendamos que siga as instruções neste artigo para ativar o Application Insights, mesmo para 2.0 aplicações.

*6. Executo meu aplicativo no Linux. São todas as funcionalidades suportadas no Linux também?*

* Sim. Suporte de funcionalidades para o SDK é a mesmo em todas as plataformas, com as seguintes exceções:
    1. Contadores de desempenho ainda não são suportados no Windows não. Este documento será atualizado quando é adicionado o suporte do Linux.
    1. Embora `ServerTelemetryChannel` está ativada por predefinição, se a aplicação for executada no não-windows, o canal não cria automaticamente uma pasta de armazenamento local para manter temporariamente a telemetria se existirem problemas de rede. Esta limitação faz com que a telemetria para ser perdido se existem temporários da rede ou problemas de servidor. A solução para este problema é para o utilizador configurar uma pasta local para o canal, conforme mostrado abaixo.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure channel to use the given folder to temporarily
        // store telemetry items during network or application insights server issues.
        // User should ensure that the given folder already exists,
        // and that application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```
