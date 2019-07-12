---
title: O Azure Application Insights para aplicações ASP.NET Core | Documentos da Microsoft
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
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: 5ea7ec41ccc721e8eafda56aa7463505ba089845
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827815"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights para aplicações ASP.NET Core

Este artigo descreve como ativar o Application Insights para uma [ASP.NET Core](https://docs.microsoft.com/aspnet/core) aplicação. Quando concluir as instruções neste artigo, o Application Insights irá recolher pedidos, dependências, exceções, contadores de desempenho, heartbeats e registos provenientes da sua aplicação ASP.NET Core. 

O exemplo que usaremos aqui é um [aplicação MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) voltado `netcoreapp2.2`. Pode aplicar estas instruções para todos os aplicativos do ASP.NET Core.

## <a name="supported-scenarios"></a>Cenários suportados

O [Application Insights SDK para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) pode monitorizar as suas aplicações, independentemente de onde e como eles são executados. Se seu aplicativo está em execução e tem conectividade de rede para o Azure, pode ser recolhida telemetria. Monitorização do Application Insights é suportada em todo o lado .NET Core é suportado. Suporte de bastidores:
* **Sistema operativo**: Windows, Linux ou Mac.
* **Método de hospedagem**: No processo ou fora do processo. 
* **Método de implementação**: Framework dependente ou independente.
* **Servidor Web**: IIS (Internet Information Server) ou Kestrel. 
* **Plataforma de hospedagem**: A funcionalidade de aplicações Web do serviço de aplicações do Azure, VM do Azure, Docker, Azure Kubernetes Service (AKS) e assim por diante.
* **IDE**: Visual Studio, VS Code ou linha de comandos.

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo do ASP.NET Core funcional. Se precisar de criar uma aplicação ASP.NET Core, siga este [tutorial de ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started/).
- Uma chave de instrumentação do Application Insights válida. Esta chave é necessária para enviar telemetria para o Application Insights. Se precisar de criar um novo recurso do Application Insights para obter uma chave, consulte de instrumentação [criar um recurso do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Ativar a telemetria de lado do servidor do Application Insights (Visual Studio)

1. Abra o projeto no Visual Studio.

    > [!TIP]
    > Se quiser, pode definir o controle de origem para o seu projeto para que possa acompanhar todas as alterações que faz com que o Application Insights. Para ativar o controlo de origem, selecione **arquivo** > **adicionar ao controlo de origem**.

2. Selecione **Project** > **adicionar telemetria do Application Insights**.

3. Selecione **começar**. Texto desta seleção pode variar, dependendo da versão do Visual Studio. Algumas versões anteriores de utilizar um **começar gratuitamente** botão em vez disso.

4. Selecione a sua subscrição. Em seguida, selecione **Resource** > **registar**.

5. Depois de adicionar o Application Insights ao seu projeto, de verificação para confirmar que está a utilizar a versão estável mais recente do SDK. Aceda a **Project** > **gerir pacotes NuGet** > **Microsoft.ApplicationInsights.AspNetCore**. Se for necessário, escolha **atualização**.

     ![Captura de ecrã que mostra onde pode selecionar o pacote do Application Insights para atualização](./media/asp-net-core/update-nuget-package.png)

6. Se seguiu a dica opcional e adicionados a seu projeto ao controle de origem, aceda a **View** > **Team Explorer** > **alterações**. Em seguida, selecione cada ficheiro para ver uma vista de diff das alterações feitas por telemetria do Application Insights.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Ativar a telemetria de lado do servidor do Application Insights (nenhum Visual Studio)

1. Instalar o [pacote NuGet do Application Insights SDK para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Recomendamos que utilize sempre a versão estável mais recente. Encontrar notas de versão completa para o SDK sobre o [repositório do GitHub de código-fonte aberto](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    O exemplo de código seguinte mostra as alterações sejam adicionados ao seu projeto `.csproj` ficheiro.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. Adicione `services.AddApplicationInsightsTelemetry();` para o `ConfigureServices()` método na sua `Startup` classe, tal como neste exemplo:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. Configure a chave de instrumentação.

    Embora possa fornecer a chave de instrumentação como um argumento para `AddApplicationInsightsTelemetry`, recomendamos que especifica a chave de instrumentação na configuração. O exemplo de código seguinte mostra como especificar uma chave de instrumentação no `appsettings.json`. Certifique-se de `appsettings.json` é copiado para a pasta de raiz do aplicativo durante a publicação.

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

    Em alternativa, especifica a chave de instrumentação em qualquer uma das seguintes variáveis de ambiente:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Por exemplo:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    Normalmente, `APPINSIGHTS_INSTRUMENTATIONKEY` Especifica a chave de instrumentação para aplicações implementadas nas aplicações Web.

    > [!NOTE]
    > Uma chave de instrumentação especificada no código wins sobre a variável de ambiente `APPINSIGHTS_INSTRUMENTATIONKEY`, que o wins sobre outras opções.

## <a name="run-your-application"></a>Executar a aplicação

Execute a sua aplicação e fazer pedidos à mesma. Telemetria agora deve fluir para o Application Insights. O SDK do Application Insights recolhe automaticamente a seguinte telemetria.

|Solicitações/dependências |Detalhes|
|---------------|-------|
|Pedidos | Pedidos web recebidos para a sua aplicação. |
|HTTP ou HTTPS | As chamadas feitas com `HttpClient`. |
|SQL | As chamadas feitas com `SqlClient`. |
|[Armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) | Chamadas feitas com o cliente de armazenamento do Azure. |
|[SDK de cliente de EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versão 1.1.0 e mais tarde. |
|[SDK de cliente de ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versão 3.0.0 e posterior. |
|Azure Cosmos DB | Controlado automaticamente apenas se for utilizada a HTTP/HTTPS. O Application Insights não captura o modo TCP. |

### <a name="performance-counters"></a>Contadores de desempenho

Suporte para [contadores de desempenho](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) em ASP.NET Core é limitado:

   * Versões do SDK 2.4.1 e contadores mais tarde recolher desempenho se a aplicação está em execução nas aplicações Web (Windows).
   * Contadores de 2.7.0-beta3 de versões do SDK e o desempenho de recolha posterior se a aplicação for executada no Windows e destinos `NETSTANDARD2.0` ou posterior.
   * Para aplicativos visando o .NET Framework, todas as versões do SDK do suportam de contadores de desempenho.
 
Este artigo será atualizado quando é adicionado suporte de contador de desempenho no Linux.

### <a name="ilogger-logs"></a>Registos de ILogger

[Registos de ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) de gravidade `Warning` ou superior são automaticamente capturados no 2.7.0-beta3 de versões do SDK e versões posteriores.

### <a name="live-metrics"></a>Métricas em direto

Poderá demorar alguns minutos antes de telemetria começa a aparecer no portal. Para certificar-se rapidamente de tudo o que está a funcionar, é melhor usar [métricas em direto](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) quando efetuar pedidos para a aplicação em execução.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Ativar a telemetria de lado do cliente para aplicações web

Os passos anteriores serão suficientes para o ajudar a começar a recolher telemetria de lado do servidor. Se seu aplicativo tiver componentes do lado do cliente, siga os passos seguintes para começar a recolher [telemetria de utilização](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. No `_ViewImports.cshtml`, adicionar injeção:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. No `_Layout.cshtml`, insira `HtmlHelper` no final da `<head>` secção, mas antes de qualquer outro script. Se quiser qualquer telemetria personalizada do JavaScript na página de relatório, inseri-lo Depois deste trecho de código:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

O `.cshtml` nomes de arquivo mencionados anteriormente são a partir de um modelo de aplicação do padrão MVC. Por fim, se pretender ativar a monitorização do lado do cliente para a sua aplicação corretamente, o fragmento do JavaScript deve aparecer no `<head>` secção de cada página da sua aplicação que pretende monitorizar. Esse objetivo para este modelo de aplicativo pode ser feito adicionando o fragmento do JavaScript para `_Layout.cshtml`. 

Se o seu projeto não inclua `_Layout.cshtml`, pode adicionar ainda [monitorização do lado do cliente](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Pode fazê-lo ao adicionar o fragmento do JavaScript para um ficheiro equivalente que controla o `<head>` de todas as páginas na sua aplicação. Ou pode adicionar o fragmento a várias páginas, mas esta solução é difícil de manter e geralmente não o recomendamos.

## <a name="configure-the-application-insights-sdk"></a>Configurar o SDK do Application Insights

Pode personalizar o Application Insights SDK para ASP.NET Core alterar a configuração predefinida. Os utilizadores do ASP.NET SDK do Application Insights podem estar familiarizados com a alteração de configuração, utilizando `ApplicationInsights.config` ou ao modificar `TelemetryConfiguration.Active`. Alterar configuração diferentes para ASP.NET Core. Adicionar o SDK do ASP.NET Core para a aplicação e configurá-lo, com o ASP.NET Core incorporada [injeção de dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Fazer quase todas as alterações de configuração no `ConfigureServices()` método de sua `Startup.cs` de classe, a menos que está direcionado caso contrário. As seções a seguir oferecem mais informações.

> [!NOTE]
> Em aplicativos do ASP.NET Core, alterar a configuração modificando `TelemetryConfiguration.Active` não é suportada.

### <a name="using-applicationinsightsserviceoptions"></a>Usando ApplicationInsightsServiceOptions

Pode modificar algumas configurações comuns ao transmitir `ApplicationInsightsServiceOptions` para `AddApplicationInsightsTelemetry`, como neste exemplo:

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

Para obter mais informações, consulte a [definições configuráveis no `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Amostragem

O Application Insights SDK para ASP.NET Core suporta taxa fixa e amostragem adaptável. Amostragem adaptável está ativada por predefinição. 

Para obter mais informações, consulte [configurar amostragem adaptável para os aplicativos do ASP.NET Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Adicionar TelemetryInitializers

Uso [inicializadores de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) quando pretender definir propriedades globais que são enviadas com toda a telemetria.

Adicionar qualquer novo `TelemetryInitializer` para o `DependencyInjection` contentor, conforme mostrado no código a seguir. O SDK recolhe automaticamente qualquer `TelemetryInitializer` que é adicionado ao `DependencyInjection` contentor.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Remover TelemetryInitializers

Inicializadores de telemetria estão presentes por predefinição. Remover todas ou inicializadores de telemetria específica, utilize o seguinte código de exemplo *após* chamar `AddApplicationInsightsTelemetry()`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

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

### <a name="adding-telemetry-processors"></a>Adição de processadores de telemetria

Pode adicionar processadores de telemetria personalizada para `TelemetryConfiguration` usando o método de extensão `AddApplicationInsightsTelemetryProcessor` no `IServiceCollection`. Utilizar processadores de telemetria em [cenários de filtragem avançada](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) para permitir o controle mais direto sobre o que tem incluído ou excluído da telemetria que envia para o serviço Application Insights. Utilize o seguinte exemplo.

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

O Application Insights utiliza dos módulos de telemetria [recolher automaticamente informações úteis](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) sobre cargas de trabalho específicas sem a necessidade de configuração adicionais.

Os seguintes módulos de coleção automático estão ativados por predefinição. Estes módulos são responsáveis pela recolha automática de telemetria. Pode desativar ou configurá-las para alterar seu comportamento padrão.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Para configurar qualquer padrão `TelemetryModule`, utilize o método de extensão `ConfigureTelemetryModule<T>` no `IServiceCollection`, conforme mostrado no exemplo a seguir.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

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

### <a name="configuring-a-telemetry-channel"></a>Configurar um canal de telemetria

O canal predefinido é `ServerTelemetryChannel`. Pode substituí-la como mostrado no exemplo seguinte.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>Desativar a telemetria dinamicamente

Se pretender desativar a telemetria condicionalmente e dinamicamente, pode resolver `TelemetryConfiguration` instância com o contêiner de injeção de dependência do ASP.NET Core em qualquer lugar no seu código e defina `DisableTelemetry` sinalizador no mesmo.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Como controlar a telemetria que é recolhida automaticamente?

Obter uma instância de `TelemetryClient` ao usando injeção de construtor e chamar necessários `TrackXXX()` método nele. Não é recomendada a criar nova `TelemetryClient` instâncias num aplicativo do ASP.NET Core. Uma instância singleton do `TelemetryClient` já está registado no `DependencyInjection` contentor, o que compartilha `TelemetryConfiguration` com rest de telemetria. Criar um novo `TelemetryClient` instância recomenda apenas se precisar de uma configuração que está separada do resto de telemetria. 

O exemplo seguinte mostra como monitorizar a telemetria adicional a partir de um controlador.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Para obter mais informações sobre dados personalizados a relatórios do Application Insights, veja [métricas personalizadas do Application Insights referência da API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Alguns modelos do Visual Studio usado o método de extensão UseApplicationInsights() no IWebHostBuilder para ativar o Application Insights. Esta utilização ainda é válida?

Sim, ativar o Application Insights com este método é válido. Essa técnica é utilizada na integração do Visual Studio e as extensões de aplicações Web. No entanto, recomendamos que utilize `services.AddApplicationInsightsTelemetry()` porque fornece sobrecargas para controlar alguma configuração. Ambos os métodos fazem a mesma coisa internamente, se não precisar de aplicar a configuração personalizada, pode chamar qualquer um dos métodos.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Eu esteja implantando a minha aplicação ASP.NET Core para aplicações Web. Deve eu ainda ativar a extensão do Application Insights a partir de aplicações Web?

Se o SDK é instalado no momento da compilação, conforme mostrado neste artigo, não precisa de ativar a extensão do Application Insights no portal de serviço de aplicações. Mesmo que a extensão é instalada, ela será voltar e quando Deteta que o SDK já foi adicionado à aplicação. Se ativar o Application Insights a partir da extensão, não tem de instalar e atualizar o SDK. Mas, se ativar o Application Insights ao seguir as instruções neste artigo, tem mais flexibilidade porque:

   * Telemetria do Application Insights irá continuar a trabalhar no:
       * Todos os sistemas operativos, incluindo Windows, Linux e Mac.
       * Publicar todos os modos, incluindo independente ou framework dependente.
       * Todas as estruturas de destino, incluindo o .NET Framework completo.
       * Todas as opções de hospedagem, incluindo aplicações Web, VMs, Linux, contentores, serviço Kubernetes do Azure e de alojamento não pertencente ao Azure.
   * Pode ver a telemetria localmente na depuração do Visual Studio.
   * Pode controlar a telemetria personalizada adicional ao utilizar o `TrackXXX()` API.
   * Tem controle total sobre a configuração.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Posso ativar a monitorização utilizando ferramentas como o Monitor de estado do Application Insights?

Não. [Monitor de estado](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e [v2 de Monitor de estado](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) atualmente dão suporte ao ASP.NET 4.x apenas.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights é ativado automaticamente para a minha aplicação ASP.NET Core 2.0?

O `Microsoft.AspNetCore.All` 2.0 metapackage inclui o SDK do Application Insights (versão 2.1.0). Se executar o aplicativo sob o depurador do Visual Studio, o Visual Studio permite-Application Insights e mostra a telemetria localmente no IDE em si. Telemetria não foi enviada para o serviço do Application Insights, a menos que foi especificada uma chave de instrumentação. Recomendamos que siga as instruções neste artigo para ativar o Application Insights, mesmo para 2.0 aplicações.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se eu executar meu aplicativo no Linux, são todas as funcionalidades suportadas?

Sim. Suporte de funcionalidades para o SDK é a mesma em todas as plataformas, com as seguintes exceções:

* Contadores de desempenho são suportados apenas no Windows.
* Embora `ServerTelemetryChannel` está ativada por predefinição, se a aplicação for executada no Linux ou MacOS, o canal não cria automaticamente uma pasta de armazenamento local para manter temporariamente a telemetria se existirem problemas de rede. Por causa da limitação, a telemetria é perdida quando surgem problemas de rede ou servidor temporários. Para contornar este problema, configure uma pasta local para o canal:

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
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>SDK de código-fonte aberto

[Ler e contribuir para o código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Vídeo

- Veja este vídeo passo a passo externo para [configurar o Application Insights com o Visual Studio e .NET Core](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) do zero.
- Veja este vídeo passo a passo externo para [configurar o Application Insights com o .NET Core e o Visual Studio Code](https://youtu.be/ygGt84GDync) do zero.

## <a name="next-steps"></a>Passos Seguintes

* [Explore os fluxos de utilizador](../../azure-monitor/app/usage-flows.md) para compreender a forma como os utilizadores navegam pela aplicação.
* [Configure uma coleção de instantâneo](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) para ver o estado do código-fonte e variáveis no momento é emitida uma exceção.
* [Utilizar a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar os seus próprios eventos e métricas para uma vista detalhada do desempenho e a utilização da sua aplicação.
* Uso [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) para verificar a sua aplicação a partir do constantemente em todo o mundo.
* [Injeção de dependência no ASP.NET Core](https://docs.microsoft.com/aspnet/fundamentals/dependency-injection)
