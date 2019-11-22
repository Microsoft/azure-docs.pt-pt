---
title: Aplicativo Azure insights para aplicativos ASP.NET Core | Microsoft Docs
description: Monitore ASP.NET Core aplicativos Web para disponibilidade, desempenho e uso.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/22/2019
ms.openlocfilehash: 823527af81e0cb22fb59421b84983684d4cdfffd
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286249"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights para aplicativos ASP.NET Core

Este artigo descreve como habilitar Application Insights para um aplicativo [ASP.NET Core](https://docs.microsoft.com/aspnet/core) . Quando você concluir as instruções neste artigo, Application Insights coletará solicitações, dependências, exceções, contadores de desempenho, pulsações e logs do seu aplicativo ASP.NET Core.

O exemplo que usaremos aqui é um [aplicativo MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) destinado a `netcoreapp2.2`. Você pode aplicar essas instruções a todos os ASP.NET Core aplicativos.

## <a name="supported-scenarios"></a>Cenários suportados

O [SDK do Application insights para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) pode monitorar seus aplicativos, independentemente de onde ou como eles são executados. Se seu aplicativo estiver em execução e tiver conectividade de rede com o Azure, a telemetria poderá ser coletada. Há suporte para o monitoramento de Application Insights em todos os lugares do .NET Core. O suporte abrange:
* **Sistema operacional**: Windows, Linux ou Mac.
* **Método de hospedagem**: em processo ou fora do processo.
* **Método de implantação**: dependente da estrutura ou independente.
* **Servidor Web**: IIS (servidor de informações da Internet) ou Kestrel.
* **Plataforma de hospedagem**: o recurso de aplicativos Web do serviço de Azure app, VM do Azure, Docker, serviço kubernetes do Azure (AKs) e assim por diante.
* **Versão de tempo de execução do .NET Core**: 1. XX, 2. XX ou 3. XX
* **IDE**: Visual Studio, vs Code ou linha de comando.

> [!NOTE]
> Se você estiver usando ASP.NET Core 3,0 junto com Application Insights, use a versão [2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) ou superior. Essa é a única versão que dá suporte a ASP.NET Core 3,0.

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo ASP.NET Core funcionando. Se você precisar criar um aplicativo ASP.NET Core, siga este [tutorial de ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started/).
- Uma chave de instrumentação de Application Insights válida. Essa chave é necessária para enviar qualquer telemetria para Application Insights. Se você precisar criar um novo recurso de Application Insights para obter uma chave de instrumentação, consulte [criar um recurso de Application insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Habilitar Application Insights telemetria do lado do servidor (Visual Studio)

1. Abra seu projeto no Visual Studio.

    > [!TIP]
    > Se desejar, você pode configurar o controle do código-fonte para o seu projeto para poder acompanhar todas as alterações que Application Insights faz. Para habilitar o controle do código-fonte, selecione **arquivo** > **Adicionar ao controle do código-fonte**.

2. Selecione **projeto** > **Adicionar Application insights Telemetry**.

3. Selecione **introdução**. O texto dessa seleção pode variar, dependendo da sua versão do Visual Studio. Em vez disso, algumas versões anteriores usam um botão **Iniciar gratuito** .

4. Selecione a sua subscrição. Em seguida, selecione **recurso** > **registrar**.

5. Depois de adicionar Application Insights ao seu projeto, verifique se você está usando a versão estável mais recente do SDK. Vá para **projeto** > **gerenciar pacotes NuGet** > **Microsoft. ApplicationInsights. AspNetCore**. Se necessário, escolha **Atualizar**.

     ![Captura de tela mostrando onde selecionar o pacote de Application Insights para atualização](./media/asp-net-core/update-nuget-package.png)

6. Se você seguiu a dica opcional e adicionou o projeto ao controle do código-fonte, acesse **exibir** > **Team Explorer** > **alterações**. Em seguida, selecione cada arquivo para ver uma exibição de comparação das alterações feitas por Application Insights telemetria.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Habilitar Application Insights telemetria do lado do servidor (sem o Visual Studio)

1. Instale o [pacote NuGet do SDK Application insights para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Recomendamos que você sempre use a versão estável mais recente. Encontre notas de versão completas para o SDK no [repositório GitHub de código-fonte aberto](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    O exemplo de código a seguir mostra as alterações a serem adicionadas ao arquivo de `.csproj` do seu projeto.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.0" />
        </ItemGroup>
    ```

2. Adicione `services.AddApplicationInsightsTelemetry();` ao método `ConfigureServices()` na sua classe `Startup`, como neste exemplo:

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

    Embora seja possível fornecer a chave de instrumentação como um argumento para `AddApplicationInsightsTelemetry`, recomendamos que você especifique a chave de instrumentação na configuração. O exemplo de código a seguir mostra como especificar uma chave de instrumentação em `appsettings.json`. Certifique-se de que `appsettings.json` seja copiado para a pasta raiz do aplicativo durante a publicação.

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

    Como alternativa, especifique a chave de instrumentação em qualquer uma das seguintes variáveis de ambiente:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Por exemplo:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    Normalmente, `APPINSIGHTS_INSTRUMENTATIONKEY` especifica a chave de instrumentação para aplicativos implantados nos aplicativos Web do Azure.

    > [!NOTE]
    > Uma chave de instrumentação especificada no código vence sobre a variável de ambiente `APPINSIGHTS_INSTRUMENTATIONKEY`, que vence em outras opções.

## <a name="run-your-application"></a>Executar a aplicação

Execute seu aplicativo e faça solicitações a ele. A telemetria agora deve fluir para Application Insights. O SDK do Application Insights coleta automaticamente as solicitações da Web de entrada para seu aplicativo, juntamente com a telemetria a seguir também.

### <a name="live-metrics"></a>Métricas ao vivo

As [métricas dinâmicas](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) podem ser usadas para verificar rapidamente se Application insights monitoramento está configurado corretamente. Embora possa levar alguns minutos antes que a telemetria comece a aparecer no portal e na análise, as métricas ao vivo mostrarão o uso da CPU do processo em execução quase em tempo real. Ele também pode mostrar outras telemetrias, como solicitações, dependências, rastreamentos, etc.

### <a name="ilogger-logs"></a>Logs do ILogger

Os logs emitidos por meio de `ILogger` de severidade `Warning` ou superior são capturados automaticamente. Siga [ILogger docs](ilogger.md#control-logging-level) para personalizar quais níveis de log são capturados por Application insights.

### <a name="dependencies"></a>Dependências

A coleção de dependências é habilitada por padrão. [Este](asp-net-dependencies.md#automatically-tracked-dependencies) artigo explica as dependências coletadas automaticamente e também contém etapas para fazer o acompanhamento manual.

### <a name="performance-counters"></a>Contadores de desempenho

O suporte para [contadores de desempenho](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) no ASP.NET Core é limitado:

* As versões do SDK 2.4.1 e posteriores coletam contadores de desempenho se o aplicativo estiver em execução nos aplicativos Web do Azure (Windows).
* As versões do SDK 2.7.1 e posteriores coletam contadores de desempenho se o aplicativo estiver em execução no Windows e for direcionado `NETSTANDARD2.0` ou posterior.
* Para aplicativos direcionados à .NET Framework, todas as versões do SDK dão suporte a contadores de desempenho.
* As versões do SDK 2.8.0 e posteriores dão suporte ao contador de CPU/memória no Linux. Nenhum outro contador tem suporte no Linux. A maneira recomendada para obter contadores do sistema no Linux (e em outros ambientes não Windows) é usando [EventCounters](#eventcounter)

### <a name="eventcounter"></a>EventCounter

o `EventCounterCollectionModule` é habilitado por padrão e coletará um conjunto padrão de contadores de aplicativos .NET Core 3,0. O tutorial [EventCounter](eventcounters.md) lista o conjunto padrão de contadores coletados. Ele também tem instruções sobre como personalizar a lista.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Habilitar telemetria do lado do cliente para aplicativos Web

As etapas anteriores são suficientes para ajudá-lo a começar a coletar a telemetria do lado do servidor. Se seu aplicativo tiver componentes do lado do cliente, siga as próximas etapas para começar a coletar a [telemetria de uso](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. Em `_ViewImports.cshtml`, adicione injeção:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. No `_Layout.cshtml`, insira `HtmlHelper` no final da seção `<head>`, mas antes de qualquer outro script. Se você quiser relatar qualquer telemetria personalizada do JavaScript da página, insira-a após este trecho:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

Os nomes de arquivo de `.cshtml` referenciados anteriormente são de um modelo de aplicativo MVC padrão. Por fim, se você quiser habilitar corretamente o monitoramento do lado do cliente para seu aplicativo, o trecho de código JavaScript deverá aparecer na seção `<head>` de cada página do aplicativo que você deseja monitorar. Você pode realizar essa meta para esse modelo de aplicativo adicionando o trecho de JavaScript a `_Layout.cshtml`. 

Se o seu projeto não incluir `_Layout.cshtml`, você ainda poderá adicionar o [monitoramento do lado do cliente](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Você pode fazer isso adicionando o trecho de JavaScript a um arquivo equivalente que controla a `<head>` de todas as páginas em seu aplicativo. Ou você pode adicionar o trecho a várias páginas, mas essa solução é difícil de manter e, em geral, não é recomendável.

## <a name="configure-the-application-insights-sdk"></a>Configurar o SDK do Application Insights

Você pode personalizar o SDK do Application Insights para ASP.NET Core para alterar a configuração padrão. Os usuários do Application Insights SDK do ASP.NET podem estar familiarizados com a alteração da configuração usando `ApplicationInsights.config` ou modificando `TelemetryConfiguration.Active`. Você altera a configuração de forma diferente para ASP.NET Core. Adicione o SDK do ASP.NET Core ao aplicativo e configure-o usando ASP.NET Core [injeção de dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)interna. Faça quase todas as alterações de configuração no método `ConfigureServices()` da sua classe `Startup.cs`, a menos que você seja direcionado de outra forma. As seções a seguir oferecem mais informações.

> [!NOTE]
> Em ASP.NET Core aplicativos, não há suporte para alterar a configuração modificando `TelemetryConfiguration.Active`.

### <a name="using-applicationinsightsserviceoptions"></a>Usando ApplicationInsightsServiceOptions

Você pode modificar algumas configurações comuns passando `ApplicationInsightsServiceOptions` para `AddApplicationInsightsTelemetry`, como neste exemplo:

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

Lista completa de configurações no `ApplicationInsightsServiceOptions`

|Definição | Descrição | Predefinição
|---------------|-------|-------
|EnableQuickPulseMetricStream | Habilitar/desabilitar o recurso LiveMetrics | true
|EnableAdaptiveSampling | Habilitar/desabilitar amostragem adaptável | true
|EnableHeartbeat | Habilitar/desabilitar o recurso de pulsações, que periodicamente (padrão de 15 min) envia uma métrica personalizada chamada ' HeartBeatstate ' com informações sobre o tempo de execução como versão do .NET, informações de ambiente do Azure, se aplicável, etc. | true
|AddAutoCollectedMetricExtractor | Habilitar/desabilitar o extrator AutoCollectedMetrics, que é um TelemetryProcessor que envia métricas previamente agregadas sobre solicitações/dependências antes que a amostragem ocorra. | true
|RequestCollectionOptions.TrackExceptions | Habilitar/desabilitar o relatório de acompanhamento de exceção sem tratamento pelo módulo coleção de solicitações. | false no netstandard 2.0 (como as exceções são rastreadas com ApplicationInsightsLoggerProvider), caso contrário, true.

Consulte as [configurações configuráveis em `ApplicationInsightsServiceOptions`](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) para obter a lista mais atualizada.

### <a name="sampling"></a>Amostragem

O SDK do Application Insights para ASP.NET Core dá suporte à amostragem de taxa fixa e adaptável. A amostragem adaptável é habilitada por padrão. 

Para obter mais informações, consulte [Configurar a amostragem adaptável para aplicativos ASP.NET Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Adicionando TelemetryInitializers

Use [inicializadores de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) quando desejar definir propriedades globais que são enviadas com toda a telemetria.

Adicione qualquer `TelemetryInitializer` novo ao contêiner `DependencyInjection`, conforme mostrado no código a seguir. O SDK seleciona automaticamente qualquer `TelemetryInitializer` que seja adicionada ao contêiner de `DependencyInjection`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>Removendo TelemetryInitializers

Os inicializadores de telemetria estão presentes por padrão. Para remover todos os inicializadores de telemetria ou específicos, use o seguinte código de exemplo *depois* de chamar `AddApplicationInsightsTelemetry()`.

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

### <a name="adding-telemetry-processors"></a>Adicionando processadores de telemetria

Você pode adicionar processadores de telemetria personalizados para `TelemetryConfiguration` usando o método de extensão `AddApplicationInsightsTelemetryProcessor` no `IServiceCollection`. Você usa processadores de telemetria em [cenários de filtragem avançada](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer). Use o exemplo a seguir.

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configurando ou removendo TelemetryModules padrão

Application Insights usa módulos de telemetria para coletar automaticamente telemetria útil sobre cargas de trabalho específicas sem a necessidade de acompanhamento manual pelo usuário.

Os seguintes módulos de coleta automática estão habilitados por padrão. Esses módulos são responsáveis por coletar automaticamente a telemetria. Você pode desabilitá-los ou configurá-los para alterar seu comportamento padrão.

* `RequestTrackingTelemetryModule`-coleta RequestTelemetry de solicitações de entrada da Web.
* `DependencyTrackingTelemetryModule`-coleta DependencyTelemetry de chamadas http de saída e chamadas SQL.
* `PerformanceCollectorModule`-coleta PerformanceCounters do Windows.
* `QuickPulseTelemetryModule`-coleta a telemetria para exibição no portal de métricas ao vivo.
* `AppServicesHeartbeatTelemetryModule`-coleta os batidas no coração (que são enviados como métricas personalizadas), sobre Azure App ambiente de serviço em que o aplicativo está hospedado.
* `AzureInstanceMetadataTelemetryModule`-coleta os batidas no coração (que são enviados como métricas personalizadas), sobre o ambiente de VM do Azure em que o aplicativo está hospedado.
* `EventCounterCollectionModule`-coleta [EventCounters.](eventcounters.md) Este módulo é um novo recurso e está disponível no SDK versão 2.8.0 e superior.

Para configurar qualquer `TelemetryModule`padrão, use o método de extensão `ConfigureTelemetryModule<T>` em `IServiceCollection`, conforme mostrado no exemplo a seguir.

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

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Clear();
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

### <a name="configuring-a-telemetry-channel"></a>Configurando um canal de telemetria

O canal padrão é `ServerTelemetryChannel`. Você pode substituí-lo como mostra o exemplo a seguir.

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

### <a name="disable-telemetry-dynamically"></a>Desabilitar telemetria dinamicamente

Se você quiser desabilitar a telemetria condicional e dinamicamente, poderá resolver `TelemetryConfiguration` instância com ASP.NET Core contêiner de injeção de dependência em qualquer lugar em seu código e definir `DisableTelemetry` sinalizador nela.

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

O acima não impede que nenhum módulo de coleção automática colete telemetria. Somente o envio de telemetria para Application Insights é desabilitado com a abordagem acima. Se um módulo de coleção automática específico não for desejado, é melhor [remover o módulo de telemetria](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="does-application-insights-support-aspnet-core-30"></a>Application Insights oferece suporte a ASP.NET Core 3,0?

Sim. Atualize para [Application insights SDK para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.8.0 ou superior. As versões mais antigas do SDK não dão suporte ao ASP.NET Core 3,0.

Além disso, se você estiver usando as instruções baseadas no Visual Studio [aqui](#enable-application-insights-server-side-telemetry-visual-studio), atualize para a versão mais recente do Visual Studio 2019 (16.3.0) para carregar. As versões anteriores do Visual Studio não dão suporte à integração automática para aplicativos ASP.NET Core 3,0.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Como posso rastrear a telemetria que não é coletada automaticamente?

Obtenha uma instância de `TelemetryClient` usando injeção de construtor e chame o método de `TrackXXX()` necessário nele. Não recomendamos criar novas instâncias de `TelemetryClient` em um aplicativo ASP.NET Core. Uma instância singleton do `TelemetryClient` já está registrada no contêiner `DependencyInjection`, que compartilha `TelemetryConfiguration` com o restante da telemetria. A criação de uma nova instância de `TelemetryClient` é recomendada apenas se precisar de uma configuração separada do restante da telemetria.

O exemplo a seguir mostra como acompanhar a telemetria adicional de um controlador.

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

Para obter mais informações sobre relatórios de dados personalizados em Application Insights, consulte [Application insights referência de API de métricas personalizadas](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Alguns modelos do Visual Studio usaram o método de extensão UseApplicationInsights () em IWebHostBuilder para habilitar Application Insights. Esse uso ainda é válido?

Embora o método de extensão `UseApplicationInsights()` ainda tenha suporte, ele é marcado como obsoleto no Application Insights SDK versão 2.8.0 em diante. Ele será removido na próxima versão principal do SDK. A maneira recomendada para habilitar a telemetria de Application Insights é usar `AddApplicationInsightsTelemetry()` porque ela fornece sobrecargas para controlar algumas configurações. Além disso, nos aplicativos ASP.NET Core 3,0, `services.AddApplicationInsightsTelemetry()` é a única maneira de habilitar o Application insights.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Estou implantando meu aplicativo de ASP.NET Core em aplicativos Web. Ainda devo habilitar a extensão de Application Insights de aplicativos Web?

Se o SDK estiver instalado no momento da compilação, conforme mostrado neste artigo, você não precisará habilitar a [extensão de Application insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) do portal do serviço de aplicativo. Mesmo que a extensão esteja instalada, ela será retirada quando detectar que o SDK já foi adicionado ao aplicativo. Se você habilitar Application Insights da extensão, não precisará instalar e atualizar o SDK. Porém, se você habilitar Application Insights seguindo as instruções neste artigo, terá mais flexibilidade porque:

   * Application Insights telemetria continuará a funcionar em:
       * Todos os sistemas operacionais, incluindo Windows, Linux e Mac.
       * Todos os modos de publicação, incluindo dependentes da estrutura ou independentes.
       * Todas as estruturas de destino, incluindo o .NET Framework completo.
       * Todas as opções de hospedagem, incluindo aplicativos Web, VMs, Linux, contêineres, serviço kubernetes do Azure e hospedagem não Azure.
       * Todas as versões do .NET Core, incluindo versões de visualização.
   * Você pode ver a telemetria localmente quando estiver Depurando do Visual Studio.
   * Você pode acompanhar a telemetria personalizada adicional usando a API `TrackXXX()`.
   * Você tem controle total sobre a configuração.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Posso habilitar o monitoramento de Application Insights usando ferramentas como Status Monitor?

Não. [Status monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e [status monitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) atualmente dão suporte apenas a ASP.NET 4. x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>O é Application Insights habilitado automaticamente para meu aplicativo ASP.NET Core 2,0?

O metapacote `Microsoft.AspNetCore.All` 2,0 incluía o SDK do Application Insights (versão 2.1.0). Se você executar o aplicativo no depurador do Visual Studio, o Visual Studio permitirá Application Insights e mostrará a telemetria localmente no próprio IDE. A telemetria não foi enviada para o serviço de Application Insights, a menos que uma chave de instrumentação tenha sido especificada. É recomendável seguir as instruções neste artigo para habilitar Application Insights, mesmo para aplicativos 2,0.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se eu executar meu aplicativo no Linux, todos os recursos têm suporte?

Sim. O suporte a recursos para o SDK é o mesmo em todas as plataformas, com as seguintes exceções:

* Os contadores de desempenho têm suporte apenas no Windows.
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
        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="is-this-sdk-supported-for-the-new-net-core-30-worker-service-template-applications"></a>Este SDK tem suporte para os novos aplicativos de modelo de serviço de trabalho do .NET Core 3,0?

Este SDK requer `HttpContext`e, portanto, não funciona em aplicativos não HTTP, incluindo os aplicativos do serviço de trabalho do .NET Core 3,0. Consulte [este](worker-service.md) documento para habilitar o Application insights em tais aplicativos, usando o SDK Microsoft. ApplicationInsights. WorkerService recentemente lançado.

## <a name="open-source-sdk"></a>SDK de código-fonte aberto

[Leia e colabore para o código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Vídeo

- Confira este vídeo passo a passo externo para configurar o [Application insights com o .NET Core e o Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) do zero.
- Confira este vídeo passo a passo externo para [configurar Application insights com o .NET Core e Visual Studio Code](https://youtu.be/ygGt84GDync) do zero.

## <a name="next-steps"></a>Passos seguintes

* [Explore os fluxos de usuário](../../azure-monitor/app/usage-flows.md) para entender como os usuários navegam pelo aplicativo.
* [Configure uma coleção de instantâneos](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) para ver o estado do código-fonte e as variáveis no momento em que uma exceção é lançada.
* [Use a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar seus próprios eventos e métricas para uma exibição detalhada do desempenho e do uso do seu aplicativo.
* Use [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) para verificar seu aplicativo constantemente em todo o mundo.
* [Injeção de dependência no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
