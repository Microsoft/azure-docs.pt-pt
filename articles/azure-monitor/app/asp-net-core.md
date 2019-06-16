---
title: Azure Application Insights para ASP.NET Core | Documentos da Microsoft
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
ms.openlocfilehash: cb7ace20fd0a59dafff3d7f8240f54c3c8e12492
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66226404"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights para aplicações ASP.NET Core

Este artigo descreve como ativar o Application Insights para uma [ASP.NET Core](https://docs.microsoft.com/aspnet/core) aplicação. Quando concluir as instruções neste artigo, Application Insights irá iniciar a recolha de pedidos, dependências, exceções, contadores de desempenho, heartbeats e registos provenientes da sua aplicação ASP.NET Core. O aplicativo de exemplo é uma [aplicação MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) direcionamento `netcoreapp2.2`, mas estas instruções são aplicáveis a todas as aplicações do ASP.NET Core.

## <a name="supported-scenarios"></a>Cenários suportados

O [Application Insights SDK (Software Development Kit) para o ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) pode monitorizar as suas aplicações, independentemente de onde ou como o aplicativo é executado. Se seu aplicativo está em execução e tem conectividade de rede para o Azure, pode ser recolhida telemetria. Isso significa que a monitorização do Application Insights é suportada em todo o lado que .NET Core é suportado. Este suporte inclui, qualquer sistema operativo (Windows, Linux, Mac), o método de alojamento (vs em processo fora de processo), o método de implementação (dependente do framework o vs independente,) servidor Web (IIS, Kestrel), plataforma de hospedagem (aplicações Web do Azure, VM do Azure, Docker, Serviço Kubernetes do Azure (AKS) e assim por diante.) ou IDE (Visual Studio, o VS Code, linha de comandos).

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo de núcleo do ASP.NET funcional. Siga os [guia de introdução ao ASP.NET Core introdução](https://docs.microsoft.com/aspnet/core/getting-started/) para criar uma aplicação ASP.NET Core, se necessário.
- Uma válido instrumentação chave do Application Insights, que é necessária para enviar telemetria para o serviço do Application Insights. Siga os [criar um recurso instruções](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) para criar um novo recurso do Application Insights, se for necessário e obter uma chave de instrumentação.

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Ativar a telemetria de lado do servidor do Application Insights (Visual Studio)

1. Abra o projeto no Visual Studio.

    > [!TIP]
    > Ao mesmo tempo não é um passo obrigatório, pode ser útil configurar o controlo de origem para o seu projeto para que possa acompanhar todas as alterações feitas pelo Application Insights. Para ativar selecione de controle de origem **arquivo** > **adicionar ao controlo de origem**.

2. Selecione **Project** > **adicionar telemetria do Application Insights**.

3. Selecione **começar**. (Dependendo da versão do Visual Studio, o texto pode variar um pouco. Algumas versões anteriores de ter uma **começar gratuitamente** botão em vez disso.)

4. Selecione a sua subscrição, em seguida, selecione **Resource** > **registar**.

5. Depois de adicionar o Application Insights ao seu projeto, de verificação para confirmar que está a utilizar a versão estável mais recente do SDK. Aceda a **Project** > **gerir pacotes NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > se for necessário escolha **Atualização**.

     ![Captura de ecrã do gerir ecrã de pacote NuGet com o pacote do Application Insights selecionado para atualização](./media/asp-net-core/update-nuget-package.png)

6. Se seguiu a dica opcional e adicionados a seu projeto ao controle de origem, em seguida, pode aceder à **View** > **Team Explorer** > **alterações** e Selecione cada ficheiro individual para uma visão de diff das alterações feitas desde adicionar telemetria do Application Insights.

## <a name="enable-application-insights-server-side-telemetry-without-visual-studio"></a>Ativar a telemetria de lado do servidor do Application Insights (sem o Visual Studio)

1. Instalar o [pacote NuGet do Application Insights SDK para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Recomendamos que utilize sempre a versão estável mais recente. Notas de versão completa para o SDK podem ser encontradas no [abrir o repositório do GitHub de origem](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    O fragmento seguinte mostra as alterações sejam adicionados ao seu projeto `.csproj` ficheiro.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
        </ItemGroup>
    ```

2. Adicione `services.AddApplicationInsightsTelemetry();` para o `ConfigureServices()` método na sua `Startup` classe. Exemplo completo abaixo.

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

3. Configure a chave de instrumentação.

    Embora seja possível fornecer a chave de instrumentação como um argumento para `AddApplicationInsightsTelemetry`, recomendamos que especifica a chave de instrumentação na configuração. O seguinte mostra como especificar uma chave de instrumentação no `appsettings.json`. Certifique-se de `appsettings.json` é copiado para a pasta de raiz do aplicativo durante a publicação.

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

    Em alternativa, a chave de instrumentação também pode ser especificada em qualquer uma das seguintes variáveis de ambiente.

    APPINSIGHTS_INSTRUMENTATIONKEY

    ApplicationInsights:InstrumentationKey

    Exemplo:

    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    `APPINSIGHTS_INSTRUMENTATIONKEY` Normalmente, é utilizado para especificar a chave de instrumentação para aplicativos implantados em aplicações Web do Azure.

    > [!NOTE]
    > Uma chave de instrumentação especificada no código wins sobre a variável de ambiente `APPINSIGHTS_INSTRUMENTATIONKEY`, que o wins sobre outras opções.

## <a name="run-your-application"></a>Executar a aplicação

 Execute a sua aplicação e fazer pedidos à mesma. Telemetria deve começar a fluir para o Application Insights. A seguinte telemetria é automaticamente recolhida pelo Application Insights SDK.

|Solicitações/dependências |Detalhes|
|---------------|-------|
|Pedidos | Pedidos web recebidos para a sua aplicação. |
|Http/Https | As chamadas feitas com `HttpClient`. |
|SQL | As chamadas feitas com `SqlClient`. |
|[Armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) | Chamadas feitas com o cliente de armazenamento do Azure. |
|[SDK de cliente de EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versão 1.1.0 e acima. |
|[SDK de cliente de ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versão 3.0.0 e posterior. |
|Azure Cosmos DB | Apenas controlados automaticamente se for utilizado HTTP/HTTPS. Modo TCP não ser capturado pelo Application Insights. |

### <a name="performance-counters"></a>Contadores de Desempenho

Suporte para [contadores de desempenho](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) ASP.NET Core está limitado ao seguinte

   * SDK versão 2.4.1 e acima coletados contadores de desempenho se a aplicação está em execução numa aplicação Web do Azure (Windows)
   * SDK versão 2.7.0-beta3 e acima coletados contadores de desempenho se a aplicação está em execução no Windows e direcionamento `NETSTANDARD2.0` ou superior.
   * Para aplicativos visando o .NET Framework, os contadores de desempenho são suportados em todas as versões do SDK.
   * Este artigo será atualizado quando é adicionado suporte de contador de desempenho no Linux.

### <a name="ilogger-logs"></a>Registos de ILogger

[Registos de ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) de gravidade `Warning` ou acima estão automaticamente capturada a partir do SDK versão 2.7.0-beta3 ou superior.

### <a name="live-metrics"></a>Métricas em direto

Pode demorar alguns minutos para a telemetria começar a aparecer no portal. Para verificar rapidamente se tudo estiver funcionando, é melhor usar [métricas em direto](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream), enquanto efetuar pedidos para a aplicação em execução.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Ativar a telemetria de lado do cliente para aplicações Web

Os passos acima são suficientes para iniciar a recolha de telemetria do lado do servidor. Se seu aplicativo tiver componentes do lado do cliente, em seguida, siga os passos abaixo para começar a recolher [telemetria de utilização](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) a partir daí.

1. No `_ViewImports.cshtml`, adicionar injeção:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. Na `_Layout.cshtml`, insira HtmlHelper ao final do `<head>` secção, mas antes de qualquer outro script. Qualquer telemetria de JavaScript personalizada, que pretende relatório a partir da página deve ser inserida após este trecho de código:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

O `.cshtml` nomes de ficheiros mencionados acima são a partir de um modelo de aplicação do padrão MVC. Por fim, para habilitar adequadamente a monitorização do lado do cliente para a sua aplicação tem do fragmento do JavaScript estar presente no `<head>` secção de cada página da sua aplicação que pretende monitorizar. Para este modelo de aplicativo adicionando o fragmento do Javascript para `_Layout.cshtml` efetivamente irá realizar esse objetivo. Se seu projeto não tiver este ficheiro específico pode ainda adicionar [monitorização do lado do cliente](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Apenas precisa para qualquer um adicionaria o JavaScript para um ficheiro equivalente que controla o `<head>` de todas as páginas na sua aplicação, ou, em alternativa, pode adicionar o trecho a pessoa de várias páginas, embora isso seria difícil de manter e geralmente não é recomendado.

## <a name="configuring-application-insights-sdk"></a>Configuração do Application Insights SDK

Application Insights SDK para ASP.NET Core podem ser personalizada para alterar a configuração predefinida. Os usuários do ASP.NET SDK do Application Insights podem estar familiarizados com a configuração através de `ApplicationInsights.config`, ou ao modificar `TelemetryConfiguration.Active`. Para o ASP.NET Core, a configuração é feita forma diferente. O ASP.NET Core SDK é adicionado à aplicação e configurado através da utilização interna do ASP.NET Core [injeção de dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Quase todas as alterações de configuração são feitas no `ConfigureServices()` método de sua `Startup.cs` de classe, salvo indicação em contrário. Siga as secções abaixo para saber mais.

> [!NOTE]
>  A alteração da configuração modificando `TelemetryConfiguration.Active` não é recomendada em aplicativos do ASP.NET Core.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>Configurar com ApplicationInsightsServiceOptions

É possível modificar algumas configurações comuns ao transmitir `ApplicationInsightsServiceOptions` para `AddApplicationInsightsTelemetry`. Um exemplo é mostrado abaixo.

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

A lista exata de definições configuráveis no `ApplicationInsightsServiceOptions` podem ser encontradas [aqui](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Amostragem

Application Insights SDK para ASP.NET Core suporta FixedRate e amostragem adaptável. Amostragem adaptável está ativada por predefinição. Siga nosso [documentação de orientação sobre a amostragem adaptável](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications), para saber como configurar a amostragem para aplicativos do ASP.NET Core.

### <a name="adding-telemetryinitializers"></a>Adicionar TelemetryInitializers

[Inicializadores de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) são utilizadas quando desejar definir propriedades globais que são enviadas com toda a telemetria.

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

Processadores de telemetria personalizada podem ser adicionadas para o `TelemetryConfiguration` usando o método de extensão `AddApplicationInsightsTelemetryProcessor` no `IServiceCollection`. Processadores de telemetria são utilizados no [cenários de filtragem avançada](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) para permitir o controle mais direto sobre o que é incluído ou excluído da telemetria que envia para o serviço Application Insights. Utilize o seguinte exemplo.

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

O Application Insights utiliza os módulos de telemetria, como uma forma de [automática a recolher informações úteis](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) sobre cargas de trabalho específicas sem a necessidade de configuração adicional.

Os seguintes módulos de coleção automática estão ativados por predefinição e são responsáveis pela recolha automática de telemetria. Pode ser desativados e configurados para alterar o comportamento predefinido.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Para configurar qualquer padrão `TelemetryModule`, utilize o método de extensão `ConfigureTelemetryModule<T>` no `IServiceCollection` conforme mostrado no exemplo abaixo.

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

### <a name="configuring-telemetry-channel"></a>Configurar o canal de telemetria

O canal predefinido utilizado é o `ServerTelemetryChannel`. Ele pode ser substituído pelo exemplo a seguir abaixo.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="i-want-to-track-additional-telemetry-other-than-the-auto-collected-telemetry-how-do-i-do-it"></a>Quero controlar telemetria adicional que não seja a telemetria de automática recolhida. Como fazer isso?

Obter uma instância do `TelemetryClient` ao usando injeção de construtor e chamar necessários `TrackXXX()` método nele. Não é recomendado para criar novos `TelemetryClient` instâncias no aplicativo do ASP.NET Core, como uma instância singleton de `TelemetryClient` já está registado no contêiner de injeção de dependência, compartilha `TelemetryConfiguration` com rest de telemetria. Criar um novo `TelemetryClient` instância recomenda-se apenas se o ter uma configuração separada do restante de telemetria. O exemplo seguinte mostra como monitorizar a telemetria adicional a partir de um controlador.

```csharp
using Microsoft.ApplicationInsights;

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

 Consulte a [métricas personalizadas do Application Insights referência da API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/) para obter uma descrição de dados personalizados a relatórios do Application Insights.

### <a name="some-visual-studio-templates-used-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Alguns modelos do Visual Studio usado o método de extensão UseApplicationInsights() no IWebHostBuilder para ativar o Application Insights. Esta utilização ainda é válida?

Ativar o Application Insights com este método é válido e é utilizado na integração do Visual Studio e também as extensões de aplicação Web do Azure. No entanto, é recomendado utilizar `services.AddApplicationInsightsTelemetry()` pois fornece sobrecargas para controlar alguma configuração. Ambos os métodos internamente fazem a mesma coisa, por isso, se não existe nenhuma configuração personalizada a ser aplicado, chamada qualquer uma serve.

### <a name="i-am-deploying-my-aspnet-core-application-to-azure-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Eu esteja implantando a minha aplicação ASP.NET Core para aplicações Web do Azure. Deve eu ainda ativar a extensão do Application Insights a partir de aplicações Web?

Se o SDK é instalado no momento da compilação, conforme mostrado neste artigo, não é necessário ativar a extensão do Application Insights no portal de serviço de aplicações. Mesmo que a extensão é instalada, ela será voltar e quando Deteta que o SDK já foi adicionado à aplicação. Ativar o Application Insights a partir da extensão permite que de instalação e atualização do SDK. No entanto, a ativar o Application Insights de acordo com este artigo é mais flexível por motivos abaixo.
   * Telemetria do Application Insights irá continuar a trabalhar no:
       * Todos os sistemas operativos - Windows, Linux, Mac.
       * Publicar todos os modos - autónomo ou dependente do Framework.
       * Todas as estruturas de destino, incluindo o .NET Framework completo.
       * Todas as opções de alojamento - aplicação Web do Azure, VMs, Linux, contentores, AKS, não pertencente ao Azure.
   * Telemetria pode ser vista localmente, quando a depuração no Visual Studio.
   * Permite o uso de telemetria personalizada adicional de controlo `TrackXXX()` API.
   * Tem controle total sobre a configuração.

### <a name="can-i-enable-application-insights-monitoring-using-tools-like-status-monitor"></a>Posso ativar a monitorização do Application Insights usando ferramentas como o Monitor de estado?

Não. [Monitor de estado](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e a respetiva substituição futura [v2 de Monitor de estado](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) suporta atualmente o ASP.NET 4.x apenas.

### <a name="i-have-an-aspnet-core-20-application-isnt-application-insights-automatically-enabled-without-me-doing-anything"></a>Tenho um aplicativo do ASP.NET Core 2.0. Application Insights não está ativado automaticamente sem que eu fazer nada?

`Microsoft.AspNetCore.All` 2.0 metapackage inclui o SDK do Application Insights (versão 2.1.0), e se executar o aplicativo sob o depurador do Visual Studio, Visual Studio permite-Application Insights e mostra a telemetria localmente no IDE do próprio. Telemetria não foi enviada para o serviço do Application Insights, a menos que uma chave de instrumentação é especificada explicitamente. Recomendamos que siga as instruções neste artigo para ativar o Application Insights, mesmo para 2.0 aplicações.

### <a name="i-run-my-application-in-linux-are-all-features-supported-in-linux-as-well"></a>Executo meu aplicativo no Linux. São todas as funcionalidades suportadas no Linux também?

* Sim. Suporte de funcionalidades para o SDK é a mesmo em todas as plataformas, com as seguintes exceções:

    * Contadores de desempenho ainda não são suportados no Windows não.
    * Embora `ServerTelemetryChannel` está ativada por predefinição, se a aplicação for executada no Linux ou MacOS, o canal não cria automaticamente uma pasta de armazenamento local para manter temporariamente a telemetria se existirem problemas de rede. Esta limitação faz com que a telemetria para ser perdido se existem temporários da rede ou problemas de servidor. A solução para este problema é para o utilizador configurar uma pasta local para o canal, conforme mostrado abaixo.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

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

## <a name="open-source-sdk"></a>SDK de código-fonte aberto
[Ler e contribuir para o código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Vídeo

- Externo vídeo passo a passo sobre [configuração do Application Insights com o Visual Studio e .NET Core](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) do zero.
- Externo vídeo passo a passo sobre [configuração do Application Insights com o .NET Core e o Visual Studio Code](https://youtu.be/ygGt84GDync) do zero.

## <a name="next-steps"></a>Passos Seguintes
* [Explore os fluxos de utilizador](../../azure-monitor/app/usage-flows.md) para compreender a forma como os utilizadores navegam pela aplicação.
* [Configurar a recolha de instantâneo](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) para ver o estado do código-fonte e variáveis no momento é emitida uma exceção.
* [Utilizar a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar os seus próprios eventos e métricas para uma visão mais detalhada do desempenho e a utilização da sua aplicação.
* Uso [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) para verificar a sua aplicação a partir do constantemente em todo o mundo.
