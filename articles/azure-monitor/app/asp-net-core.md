---
title: Aplicativo Azure insights para aplicativos ASP.NET Core | Microsoft Docs
description: Monitore ASP.NET Core aplicativos Web para disponibilidade, desempenho e uso.
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
ms.openlocfilehash: 9e14a9f3f2f27112a591f14e9a93580f66aadef7
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169562"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights para aplicativos ASP.NET Core

Este artigo descreve como habilitar Application Insights para um aplicativo [ASP.NET Core](https://docs.microsoft.com/aspnet/core) . Quando você concluir as instruções neste artigo, Application Insights coletará solicitações, dependências, exceções, contadores de desempenho, pulsações e logs do seu aplicativo ASP.NET Core.

O exemplo que usaremos aqui é um [aplicativo MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) que tem como `netcoreapp2.2`destino. Você pode aplicar essas instruções a todos os ASP.NET Core aplicativos.

## <a name="supported-scenarios"></a>Cenários suportados

O [SDK do Application insights para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) pode monitorar seus aplicativos, independentemente de onde ou como eles são executados. Se seu aplicativo estiver em execução e tiver conectividade de rede com o Azure, a telemetria poderá ser coletada. Há suporte para o monitoramento de Application Insights em todos os lugares do .NET Core. O suporte abrange:
* **Sistema operacional**: Windows, Linux ou Mac.
* **Método de hospedagem**: Em andamento ou fora do processo.
* **Método de implantação**: Dependente da estrutura ou independente.
* **Servidor Web**: IIS (Internet Information Server) ou Kestrel.
* **Plataforma de hospedagem**: O recurso de aplicativos Web do serviço de Azure App, VM do Azure, Docker, serviço kubernetes do Azure (AKS) e assim por diante.
* **IDE**: Visual Studio, VS Code ou linha de comando.

> [!NOTE]
> Se você estiver usando ASP.NET Core 3,0-Preview junto com Application Insights, use a versão [2.8.0-Beta3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0-beta3) ou superior. Essa é a única versão conhecida para funcionar bem com o ASP.NET Core 3,0. Além disso, a integração baseada no Visual Studio ainda não tem suporte para aplicativos ASP.NET Core 3,0.

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo ASP.NET Core funcionando. Se você precisar criar um aplicativo ASP.NET Core, siga este [tutorial de ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started/).
- Uma chave de instrumentação de Application Insights válida. Essa chave é necessária para enviar qualquer telemetria para Application Insights. Se você precisar criar um novo recurso de Application Insights para obter uma chave de instrumentação, consulte [criar um recurso de Application insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Habilitar Application Insights telemetria do lado do servidor (Visual Studio)

1. Abra seu projeto no Visual Studio.

    > [!TIP]
    > Se desejar, você pode configurar o controle do código-fonte para o seu projeto para poder acompanhar todas as alterações que Application Insights faz. Para habilitar o controle do código-fonte, selecione **arquivo** > **Adicionar ao controle do código-fonte**.

2. Selecione **projeto** > **Adicionar Application insights Telemetry**.

3. Selecione **introdução**. O texto dessa seleção pode variar, dependendo da sua versão do Visual Studio. Em vez disso, algumas versões anteriores usam um botão **Iniciar gratuito** .

4. Selecione a sua subscrição. Em seguida, selecione**registro**de **recursos** > .

5. Depois de adicionar Application Insights ao seu projeto, verifique se você está usando a versão estável mais recente do SDK. Vá para **projeto** > **gerenciar pacotes** > NuGet**Microsoft. ApplicationInsights. AspNetCore**. Se necessário, escolha **Atualizar**.

     ![Captura de tela mostrando onde selecionar o pacote de Application Insights para atualização](./media/asp-net-core/update-nuget-package.png)

6. Se você seguiu a dica opcional e adicionou o projeto ao controle do código-fonte, acesse **Exibir** > **Team Explorer** > **alterações**. Em seguida, selecione cada arquivo para ver uma exibição de comparação das alterações feitas por Application Insights telemetria.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Habilitar Application Insights telemetria do lado do servidor (sem o Visual Studio)

1. Instale o [pacote NuGet do SDK Application insights para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Recomendamos que você sempre use a versão estável mais recente. Encontre notas de versão completas para o SDK no [repositório GitHub de código-fonte aberto](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    O exemplo de código a seguir mostra as alterações a serem adicionadas ao `.csproj` arquivo do seu projeto.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. Adicione `services.AddApplicationInsightsTelemetry();` `Startup` ao métodoemsuaclasse,comonesteexemplo:`ConfigureServices()`

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

    Embora seja possível fornecer a chave de instrumentação como um argumento `AddApplicationInsightsTelemetry`para, recomendamos que você especifique a chave de instrumentação na configuração. O exemplo de código a seguir mostra como especificar uma chave de instrumentação no `appsettings.json`. Certifique- `appsettings.json` se de que o seja copiado para a pasta raiz do aplicativo durante a publicação.

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

    Normalmente, `APPINSIGHTS_INSTRUMENTATIONKEY` especifica a chave de instrumentação para aplicativos implantados em aplicativos Web.

    > [!NOTE]
    > Uma chave de instrumentação especificada no código vence sobre a variável `APPINSIGHTS_INSTRUMENTATIONKEY`de ambiente, que vence em outras opções.

## <a name="run-your-application"></a>Executar a aplicação

Execute seu aplicativo e faça solicitações a ele. A telemetria agora deve fluir para Application Insights. O SDK do Application Insights coleta automaticamente a telemetria a seguir.

|Solicitações/dependências |Detalhes|
|---------------|-------|
|Pedidos | Solicitações da Web de entrada para seu aplicativo. |
|HTTP ou HTTPS | Chamadas feitas com `HttpClient`. |
|SQL | Chamadas feitas com `SqlClient`. |
|[Armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) | Chamadas feitas com o cliente de armazenamento do Azure. |
|[SDK do cliente do EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versão 1.1.0 e posterior. |
|[SDK do cliente do ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versão 3.0.0 e posterior. |
|Azure Cosmos DB | Rastreado automaticamente somente se HTTP/HTTPS for usado. Application Insights não captura o modo TCP. |

### <a name="performance-counters"></a>Contadores de desempenho

O suporte para [contadores de desempenho](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) no ASP.NET Core é limitado:

* As versões do SDK 2.4.1 e posteriores coletam contadores de desempenho se o aplicativo estiver em execução nos aplicativos Web do Azure (Windows).
* As versões do SDK 2.7.1 e posteriores coletam contadores de desempenho se o aplicativo estiver `NETSTANDARD2.0` em execução no Windows e nos destinos ou posterior.
* Para aplicativos direcionados à .NET Framework, todas as versões do SDK dão suporte a contadores de desempenho.
* As versões do SDK 2.8.0-Beta3 e posteriores dão suporte ao contador de CPU/memória no Linux. Nenhum outro contador tem suporte no Linux. A maneira recomendada para obter contadores do sistema no Linux (e em outros ambientes não Windows) é usando [EventCounters](#eventcounter)

### <a name="eventcounter"></a>EventCounter

[EventCounter](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md), é um método de plataforma cruzada para publicar e consumir contadores no .NET/.NET Core. Embora esse recurso existisse antes, não havia provedores internos que publicaram esses contadores. A partir do .NET Core 3,0, vários contadores são publicados prontos para uso, como contadores CLR, ASP.NET Core contadores etc.

As versões do SDK 2.8.0-Beta3 e superior dão suporte à coleta de EventCounters. Por padrão, o SDK coleta os contadores a seguir, e esses contadores podem ser consultados em Metrics Explorer ou usando a consulta de análise na tabela PerformanceCounter. O nome dos contadores estará no formato "categoria | Counter ".

|Category | Contador|
|---------------|-------|
|System. Runtime | uso da CPU |
|System. Runtime | conjunto de trabalho |
|System. Runtime | GC-heap-tamanho |
|System. Runtime | Gen-0-GC-contagem |
|System. Runtime | Gen-1-GC-contagem |
|System. Runtime | Gen-2-GC-contagem |
|System. Runtime | tempo de GC |
|System. Runtime | Ger-0-tamanho |
|System. Runtime | Ger-1-tamanho |
|System. Runtime | Gen-2-tamanho |
|System. Runtime | Loh-tamanho |
|System. Runtime | taxa de alocação |
|System. Runtime | assembly-contagem |
|System. Runtime | contagem de exceção |
|System. Runtime | ThreadPool-contagem de threads |
|System. Runtime | monitor-contagem de contenção de bloqueio |
|System. Runtime | ThreadPool-comprimento da fila |
|System. Runtime | ThreadPool-concluído-itens-contagem |
|System. Runtime | active-timer-Count |
|Microsoft. AspNetCore. Hosting | solicitações por segundo |
|Microsoft. AspNetCore. Hosting | total-solicitações |
|Microsoft. AspNetCore. Hosting | solicitações atuais |
|Microsoft. AspNetCore. Hosting | solicitações com falha |

### <a name="ilogger-logs"></a>Logs do ILogger

[Os logs](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) de ILogger `Warning` de severidade ou superior são capturados automaticamente nas versões do SDK 2.7.0-Beta3 e posteriores.

### <a name="live-metrics"></a>Métricas Dinâmicas

Pode levar alguns minutos antes que a telemetria comece a aparecer no Portal. Para garantir que tudo esteja funcionando rapidamente, é melhor usar métricas em [tempo real](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) ao fazer solicitações para o aplicativo em execução.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Habilitar telemetria do lado do cliente para aplicativos Web

As etapas anteriores são suficientes para ajudá-lo a começar a coletar a telemetria do lado do servidor. Se seu aplicativo tiver componentes do lado do cliente, siga as próximas etapas para começar a coletar a telemetria de [uso](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. No `_ViewImports.cshtml`, adicione injeção:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. No `_Layout.cshtml`, insira `HtmlHelper` nofinaldaseção,masantesdequalqueroutroscript.`<head>` Se você quiser relatar qualquer telemetria personalizada do JavaScript da página, insira-a após este trecho:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

Os `.cshtml` nomes de arquivo referenciados anteriormente são de um modelo de aplicativo MVC padrão. Por fim, se você quiser habilitar corretamente o monitoramento do lado do cliente para seu aplicativo, o trecho de código JavaScript `<head>` deverá aparecer na seção de cada página do aplicativo que você deseja monitorar. Você pode realizar essa meta para esse modelo de aplicativo adicionando o trecho de JavaScript `_Layout.cshtml`ao. 

Se o projeto não incluir `_Layout.cshtml`, você ainda poderá adicionar o [monitoramento do lado do cliente](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Você pode fazer isso adicionando o trecho de JavaScript a um arquivo equivalente que controla o `<head>` de todas as páginas em seu aplicativo. Ou você pode adicionar o trecho a várias páginas, mas essa solução é difícil de manter e, em geral, não é recomendável.

## <a name="configure-the-application-insights-sdk"></a>Configurar o SDK do Application Insights

Você pode personalizar o SDK do Application Insights para ASP.NET Core para alterar a configuração padrão. Os usuários do Application insights SDK do ASP.net podem estar familiarizados com a alteração `ApplicationInsights.config` da configuração usando `TelemetryConfiguration.Active`ou modificando. Você altera a configuração de forma diferente para ASP.NET Core. Adicione o SDK do ASP.NET Core ao aplicativo e configure-o usando ASP.NET Core [injeção de dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)interna. Faça quase todas as alterações de configuração `ConfigureServices()` no método da `Startup.cs` sua classe, a menos que você seja direcionado de outra forma. As seções a seguir oferecem mais informações.

> [!NOTE]
> Em ASP.NET Core aplicativos, não há suporte para `TelemetryConfiguration.Active` alterar a configuração modificando.

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

Lista completa de configurações em`ApplicationInsightsServiceOptions`

|Definição | Descrição | Predefinição
|---------------|-------|-------
|EnableQuickPulseMetricStream | Habilitar/desabilitar o recurso LiveMetrics | true
|EnableAdaptiveSampling | Habilitar/desabilitar amostragem adaptável | true
|EnableHeartbeat | Habilitar/desabilitar o recurso de pulsações, que periodicamente (15 min padrão) envia uma métrica personalizada chamada ' HeartBeatstate ' com informações sobre o tempo de execução como versão do .NET, informações de ambiente do Azure, se aplicável, etc. | true
|AddAutoCollectedMetricExtractor | Habilitar/desabilitar o extrator AutoCollectedMetrics, que é um TelemetryProcessor que envia métricas previamente agregadas sobre solicitações/dependências antes que a amostragem ocorra. | true
|RequestCollectionOptions.TrackExceptions | Habilitar/desabilitar o relatório de acompanhamento de exceção sem tratamento pelo módulo coleção de solicitações. | false no netstandard 2.0 (como as exceções são rastreadas com ApplicationInsightsLoggerProvider), caso contrário, true.

Consulte as [configurações configuráveis `ApplicationInsightsServiceOptions` no](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) para obter a lista mais atualizada.

### <a name="sampling"></a>Amostragem

O SDK do Application Insights para ASP.NET Core dá suporte à amostragem de taxa fixa e adaptável. A amostragem adaptável é habilitada por padrão. 

Para obter mais informações, consulte [Configurar a amostragem adaptável para aplicativos ASP.NET Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Adicionando TelemetryInitializers

Use [inicializadores](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) de telemetria quando desejar definir propriedades globais que são enviadas com toda a telemetria.

Adicione qualquer novo `TelemetryInitializer` `DependencyInjection` ao contêiner, conforme mostrado no código a seguir. O SDK seleciona automaticamente qualquer `TelemetryInitializer` que seja adicionado `DependencyInjection` ao contêiner.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Removendo TelemetryInitializers

Os inicializadores de telemetria estão presentes por padrão. Para remover todos os inicializadores de telemetria específicos, use o código de exemplo a `AddApplicationInsightsTelemetry()`seguir depois de chamar.

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

Você pode adicionar processadores de telemetria `TelemetryConfiguration` personalizados ao usando o método `AddApplicationInsightsTelemetryProcessor` de `IServiceCollection`extensão no. Você usa processadores de telemetria em [cenários de filtragem avançada](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) para permitir um controle mais direto sobre o que está incluído ou excluído da telemetria que você envia para o serviço de Application insights. Use o exemplo a seguir.

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

* `RequestTrackingTelemetryModule`-Coleta RequestTelemetry de solicitações de entrada da Web.
* `DependencyTrackingTelemetryModule`-Coleta DependencyTelemetry de chamadas http de saída e chamadas SQL.
* `PerformanceCollectorModule`-Coleta os PerformanceCounters do Windows.
* `QuickPulseTelemetryModule`-Coleta a telemetria para exibição no portal de métricas ao vivo.
* `AppServicesHeartbeatTelemetryModule`-Coleta os batidas no coração (que são enviados como métricas personalizadas), sobre o ambiente de serviço Azure App em que o aplicativo está hospedado.
* `AzureInstanceMetadataTelemetryModule`-Coleta os batidas no coração (que são enviados como métricas personalizadas), sobre o ambiente de VM do Azure em que o aplicativo está hospedado.
* `EventCounterCollectionModule`-Coleta [EventCounters.](#eventcounter). Este módulo é um novo recurso e está disponível no SDK versão 2.8.0-Beta3 e superior.

Para configurar qualquer padrão `TelemetryModule`, use o método `ConfigureTelemetryModule<T>` de extensão `IServiceCollection`em, conforme mostrado no exemplo a seguir.

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

Se você quiser desabilitar a telemetria condicional e dinamicamente, poderá resolver `TelemetryConfiguration` a instância com ASP.NET Core contêiner de injeção de dependência em qualquer lugar em seu código e definir `DisableTelemetry` o sinalizador nela.

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

Observe que o mostrado acima não impede que nenhum módulo de coleção automática colete telemetria. Somente o envio de telemetria para Application Insights é desabilitado com a abordagem acima. Se um módulo de coleção automática específico não for desejado, o melhor é [remover o módulo de telemetria](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Como posso rastrear a telemetria que não é coletada automaticamente?

Obtenha uma instância do `TelemetryClient` usando injeção de construtor e chame o método necessário `TrackXXX()` nele. Não recomendamos a criação `TelemetryClient` de novas instâncias em um aplicativo ASP.NET Core. Uma instância singleton do `TelemetryClient` já está registrada `DependencyInjection` no contêiner, que compartilha `TelemetryConfiguration` com o restante da telemetria. A criação de `TelemetryClient` uma nova instância é recomendada apenas se precisar de uma configuração separada do restante da telemetria. 

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

Sim, habilitar Application Insights com esse método é válido. Essa técnica é usada na integração do Visual Studio e nas extensões de aplicativos Web. No entanto, é `services.AddApplicationInsightsTelemetry()` recomendável usar o porque ele fornece sobrecargas para controlar algumas configurações. Ambos os métodos fazem a mesma coisa internamente, portanto, se você não precisar aplicar a configuração personalizada, você pode chamar um dos métodos.

`IWebHostBuilder`é substituído por `IHostBuilder` no ASP.NET Core 3,0 e para evitar confusão, Application insights versão 2.8.0-Beta3 em diante está marcando o método UseApplicationInsights () como obsoleto e será removido na próxima versão principal.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Estou implantando meu aplicativo de ASP.NET Core em aplicativos Web. Ainda devo habilitar a extensão de Application Insights de aplicativos Web?

Se o SDK estiver instalado no momento da compilação, conforme mostrado neste artigo, você não precisará habilitar a [extensão de Application insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) do portal do serviço de aplicativo. Mesmo que a extensão esteja instalada, ela será retirada quando detectar que o SDK já foi adicionado ao aplicativo. Se você habilitar Application Insights da extensão, não precisará instalar e atualizar o SDK. Porém, se você habilitar Application Insights seguindo as instruções neste artigo, terá mais flexibilidade porque:

   * Application Insights telemetria continuará a funcionar em:
       * Todos os sistemas operacionais, incluindo Windows, Linux e Mac.
       * Todos os modos de publicação, incluindo dependentes da estrutura ou independentes.
       * Todas as estruturas de destino, incluindo o .NET Framework completo.
       * Todas as opções de hospedagem, incluindo aplicativos Web, VMs, Linux, contêineres, serviço kubernetes do Azure e hospedagem não Azure.
       * Todas as versões do .NET Core, incluindo versões de visualização.
   * Você pode ver a telemetria localmente quando estiver Depurando do Visual Studio.
   * Você pode acompanhar a telemetria personalizada adicional usando `TrackXXX()` a API.
   * Você tem controle total sobre a configuração.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Posso habilitar o monitoramento de Application Insights usando ferramentas como Status Monitor?

Não. [Status monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e [status monitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) atualmente dão suporte apenas a ASP.NET 4. x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>O é Application Insights habilitado automaticamente para meu aplicativo ASP.NET Core 2,0?

O `Microsoft.AspNetCore.All` metapacote 2,0 incluiu o SDK do Application insights (versão 2.1.0). Se você executar o aplicativo no depurador do Visual Studio, o Visual Studio permitirá Application Insights e mostrará a telemetria localmente no próprio IDE. A telemetria não foi enviada para o serviço de Application Insights, a menos que uma chave de instrumentação tenha sido especificada. É recomendável seguir as instruções neste artigo para habilitar Application Insights, mesmo para aplicativos 2,0.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se eu executar meu aplicativo no Linux, todos os recursos têm suporte?

Sim. O suporte a recursos para o SDK é o mesmo em todas as plataformas, com as seguintes exceções:

* Os contadores de desempenho têm suporte apenas no Windows.
* Embora `ServerTelemetryChannel` o esteja habilitado por padrão, se o aplicativo estiver sendo executado no Linux ou no MacOS, o canal não criará automaticamente uma pasta de armazenamento local para manter a telemetria temporariamente se houver problemas de rede. Devido a essa limitação, a telemetria é perdida quando há problemas temporários de rede ou servidor. Para contornar esse problema, configure uma pasta local para o canal:

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

[Leia e colabore para o código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Vídeo

- Confira este vídeo passo a passo externo para configurar o [Application insights com o .NET Core e o Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) do zero.
- Confira este vídeo passo a passo externo para [configurar Application insights com o .NET Core e Visual Studio Code](https://youtu.be/ygGt84GDync) do zero.

## <a name="next-steps"></a>Passos seguintes

* [Explore os fluxos de usuário](../../azure-monitor/app/usage-flows.md) para entender como os usuários navegam pelo aplicativo.
* [Configure uma coleção](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) de instantâneos para ver o estado do código-fonte e as variáveis no momento em que uma exceção é lançada.
* [Use a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar seus próprios eventos e métricas para uma exibição detalhada do desempenho e do uso do seu aplicativo.
* Use [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) para verificar seu aplicativo constantemente em todo o mundo.
* [Injeção de dependência no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
