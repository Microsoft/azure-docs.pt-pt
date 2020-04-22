---
title: Insights de aplicação azure para aplicações ASP.NET Core Microsoft Docs
description: Monitorize ASP.NET aplicações web Core para disponibilidade, desempenho e utilização.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 1a9a81d76df7f14fb99b8521e7bfa2edff6c9e9e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687376"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Insights de aplicação para aplicações ASP.NET Core

Este artigo descreve como ativar os Insights de Aplicação para uma aplicação [ASP.NET Core.](https://docs.microsoft.com/aspnet/core) Quando completar as instruções deste artigo, a Application Insights irá recolher pedidos, dependências, exceções, contadores de desempenho, batimentos cardíacos e registos da sua aplicação ASP.NET Core.

O exemplo que usaremos aqui é uma `netcoreapp2.2` [aplicação MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) que visa. Pode aplicar estas instruções a todas as aplicações ASP.NET Core.

## <a name="supported-scenarios"></a>Cenários suportados

O [SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) de Insights de Aplicação para ASP.NET Core pode monitorizar as suas aplicações independentemente de onde ou como funcionam. Se a sua aplicação estiver em execução e tiver conectividade de rede com o Azure, a telemetria pode ser recolhida. A monitorização de Insights de Aplicação é suportada em todo o lado .NET Core é suportado. Coberturas de suporte:
* **Sistema operativo**: Windows, Linux ou Mac.
* Método de **hospedagem**: Em processo ou fora de processo.
* **Método de implantação**: Dependente da estrutura ou independente.
* **Servidor web**: IIS (Internet Information Server) ou Kestrel.
* **Plataforma de hospedagem**: A funcionalidade de Aplicações Web do Azure App Service, Azure VM, Docker, Azure Kubernetes Service (AKS), e assim por diante.
* **.NET Core Runtime versão**: 1.XX, 2.XX ou 3.XX
* **IDE**: Estúdio Visual, Código VS ou linha de comando.

> [!NOTE]
> Se estiver a utilizar ASP.NET Core 3.X juntamente com os Insights da Aplicação, utilize a versão [2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) ou superior. Esta é a única versão que suporta ASP.NET Core 3.X.

## <a name="prerequisites"></a>Pré-requisitos

- Uma aplicação central ASP.NET funcional. Se precisar de criar uma aplicação ASP.NET Core, siga este [tutorial ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started/).
- Uma chave de instrumentação de insights de aplicação válida. Esta chave é necessária para enviar qualquer telemetria para Application Insights. Se precisar de criar um novo recurso Application Insights para obter uma chave de instrumentação, consulte [Criar um recurso Deinsights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)de Aplicação .

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Ativar a telemetria do lado do servidor do servidor Insights (Estúdio Visual)

1. Abra o seu projeto no Estúdio Visual.

    > [!TIP]
    > Se quiser, pode configurar o controlo de origem do seu projeto para que possa acompanhar todas as alterações que a Application Insights faz. Para ativar o controlo de origem, selecione **File** > **Add to Source Control**.

2. Selecione **Project** > **Add Application Insights Telemetria**.

3. Selecione **Começar**. O texto desta seleção pode variar, dependendo da sua versão do Estúdio Visual. Algumas versões anteriores usam um botão **Start Free.**

4. Selecione a sua subscrição. Em seguida, selecione**Registo** **de Recursos** > .

5. Depois de adicionar Insights de Aplicação ao seu projeto, verifique para confirmar que está a utilizar a mais recente versão estável do SDK. Vá para **Project** > **Manage NuGet Packages** > **Microsoft.ApplicationInsights.AspNetCore**. Se precisar, escolha **Atualizar**.

     ![Screenshot mostrando onde selecionar o pacote Deinsights de Aplicação para atualização](./media/asp-net-core/update-nuget-package.png)

6. Se seguiu a dica opcional e adicionou o seu projeto ao controlo de fontes, vá ao **View** > **Team Explorer** > **Changes**. Em seguida, selecione cada ficheiro para ver uma visão difusa das alterações efetuadas pela telemetria Application Insights.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Ativar a telemetria do lado do servidor De Insights (sem Estúdio Visual)

1. Instale o [pacote NuGet SDK SDK Para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Recomendamos que use sempre a versão mais recente e estável. Encontre notas de lançamento completas para o SDK no [repo gitHub de código aberto](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    A amostra de código seguinte mostra as alterações a adicionar ao ficheiro do `.csproj` seu projeto.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. Adicione `services.AddApplicationInsightsTelemetry();` ao `ConfigureServices()` método `Startup` na sua classe, como neste exemplo:

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

3. Instale a chave de instrumentação.

    Embora possa fornecer a chave de `AddApplicationInsightsTelemetry`instrumentação como argumento para, recomendamos que especifique a chave de instrumentação na configuração. A amostra de código que se segue `appsettings.json`mostra como especificar uma chave de instrumentação em . Certifique-se de que `appsettings.json` está copiada para a pasta raiz da aplicação durante a publicação.

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

    Em alternativa, especifique a chave de instrumentação em qualquer uma das seguintes variáveis ambientais:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Por exemplo:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    Normalmente, `APPINSIGHTS_INSTRUMENTATIONKEY` especifica a chave de instrumentação para aplicações implementadas nas Web Apps do Azure.

    > [!NOTE]
    > Uma chave de instrumentação especificada no `APPINSIGHTS_INSTRUMENTATIONKEY`código ganha sobre a variável ambiental , que ganha sobre outras opções.

## <a name="run-your-application"></a>Executar a aplicação

Faça o seu pedido e faça-lhe pedidos. A telemetria deve agora fluir para a Aplicação Insights. O Application Insights SDK recolhe automaticamente pedidos web de entrada para a sua aplicação, juntamente com a seguinte telemetria também.

### <a name="live-metrics"></a>Live Metrics

[As Métricas Vivas](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) podem ser usadas para verificar rapidamente se a monitorização de Insights de Aplicação está configurada corretamente. Embora possa demorar alguns minutos até que a telemetria comece a aparecer no portal e na análise, a Live Metrics mostraria o uso do CPU do processo de execução em quase tempo real. Também pode mostrar outras telemetrias como Pedidos, Dependências, Vestígios, etc.

### <a name="ilogger-logs"></a>Registos ILogger

Os registos `ILogger` emitidos por `Warning` gravidade ou superior são automaticamente capturados. Siga os [docs iLogger](ilogger.md#control-logging-level) para personalizar quais os níveis de registo capturados pela Application Insights.

### <a name="dependencies"></a>Dependências

A recolha de dependência é ativada por defeito. [Este](asp-net-dependencies.md#automatically-tracked-dependencies) artigo explica as dependências que são recolhidas automaticamente, e também contêm passos para fazer o rastreio manual.

### <a name="performance-counters"></a>Contadores de desempenho

O suporte para contadores de [desempenho](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) em ASP.NET Core é limitado:

* As versões SDK 2.4.1 e posteriormente recolhem contadores de desempenho se a aplicação estiver em execução em Aplicações Web Do Azure (Windows).
* As versões SDK 2.7.1 e posteriormente recolhem contadores de `NETSTANDARD2.0` desempenho se a aplicação estiver a ser recorrida no Windows e alvos ou posteriormente.
* Para aplicações direcionadas para o .NET Framework, todas as versões dos contadores de desempenho de suporte SDK.
* Versões SDK 2.8.0 e posteriormente suporte cpu/contador de memória em Linux. Nenhum outro contador é apoiado em Linux. A forma recomendada de obter contadores de sistemas em Linux (e outros ambientes não Windows) é usando [EventCounters](#eventcounter)

### <a name="eventcounter"></a>Contador de Eventos

`EventCounterCollectionModule`é ativado por padrão, e irá recolher um conjunto padrão de contadores de aplicações .NET Core 3.X. O tutorial [EventCounter](eventcounters.md) lista o conjunto padrão de contadores recolhidos. Também tem instruções sobre personalizar a lista.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Ativar a telemetria do lado do cliente para aplicações web

Os passos anteriores são suficientes para ajudá-lo a começar a recolher a telemetria do lado do servidor. Se a sua aplicação tiver componentes do lado do cliente, siga os próximos passos para começar a recolher [telemetria](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)de utilização .

1. Em, `_ViewImports.cshtml`adicione a injeção:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. In `_Layout.cshtml`, `HtmlHelper` insira `<head>` no final da secção, mas antes de qualquer outro script. Se pretender reportar qualquer telemetria JavaScript personalizada da página, injete-a depois deste corte:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
Em alternativa, `FullScript` a `ScriptBody` utilização do está disponível a partir do SDK v2.14. Utilize isto se precisar `<script>` de controlar a etiqueta para definir uma Política de Segurança de Conteúdo:

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

Os `.cshtml` nomes de ficheiros referenciados anteriormente são de um modelo de aplicação padrão do MVC. Em última análise, se pretender ativar adequadamente a monitorização do lado do cliente `<head>` para a sua aplicação, o corte JavaScript deve aparecer na secção de cada página da sua aplicação que pretende monitorizar. Pode atingir este objetivo para este modelo de aplicação `_Layout.cshtml`adicionando o snippet JavaScript a . 

Se o seu projeto `_Layout.cshtml`não incluir, ainda pode adicionar [monitorização do lado do cliente](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Pode fazê-lo adicionando o corte JavaScript a `<head>` um ficheiro equivalente que controla todas as páginas dentro da sua aplicação. Ou pode adicionar o corte a várias páginas, mas esta solução é difícil de manter e geralmente não o recomendamos.

## <a name="configure-the-application-insights-sdk"></a>Configure o SDK de Insights de Aplicação

Pode personalizar o SDK de Insights de Aplicação para ASP.NET Core para alterar a configuração predefinida. Os utilizadores dos Insights de Aplicação ASP.NET SDK podem estar familiarizados com a alteração da configuração utilizando `ApplicationInsights.config` ou modificando `TelemetryConfiguration.Active`. Muda a configuração de forma diferente para ASP.NET Core. Adicione o ASP.NET Core SDK à aplicação e configure-o utilizando ASP.NET injeção de [dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)incorporada do Núcleo . Faça quase todas as `ConfigureServices()` alterações `Startup.cs` de configuração no método da sua classe, a menos que seja dirigido de outra forma. As seguintes secções oferecem mais informações.

> [!NOTE]
> Em ASP.NET aplicações Core, alterar `TelemetryConfiguration.Active` a configuração modificando não é suportado.

### <a name="using-applicationinsightsserviceoptions"></a>Utilização de Opções de Serviço sintetizá-lo

Pode modificar algumas configurações `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetry`comuns passando para, como neste exemplo:

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
|Módulo de Recolha de Contra-Recolha de Desempenho Ativado  | Ativar/Desativar`PerformanceCounterCollectionModule` | true
|Módulo de Telemetria EnableRequestTrackingTelemettry   | Ativar/Desativar`RequestTrackingTelemetryModule` | true
|Módulo de Recolha de Contador de Eventos   | Ativar/Desativar`EventCounterCollectionModule` | true
|EnableDependencyTrackingTelemettryModule   | Ativar/Desativar`DependencyTrackingTelemetryModule` | true
|EnableAppServicesHeartbeatTelemettryModule  |  Ativar/Desativar`AppServicesHeartbeatTelemetryModule` | true
|EnableAzureInstanceMetadataTelemettryModule   |  Ativar/Desativar`AzureInstanceMetadataTelemetryModule` | true
|EnableQuickPulseMetricStream | Ativar/desativar a funcionalidade LiveMetrics | true
|EnableAdaptiveSampling | Ativar/desativar a amostragem adaptativa | true
|EnableHeartbeat | Ativar/Desativar a funcionalidade Heartbeats, que periodicamente (padrão de 15 min) envia uma métrica personalizada chamada 'HeartBeatState' com informações sobre o tempo de execução como .NET Version, Azure Environment information, se aplicável, etc. | true
|Extrator métrico addautocollected | Ativar/Desativar o extrator de métricas automáticas, que é um Processador de Telemetria que envia métricas pré-agregadas sobre Pedidos/Dependências antes da amostragem. | true
|RequestCollectionOptions.TrackExceptions | Ativar/desativar o relato do rastreio de exceção não manipulado pelo módulo de recolha de pedidos. | falso em NETSTANDARD2.0 (porque as exceções são rastreadas com ApplicationInsightsLoggerProvider), verdadeiramente de outra forma.

Consulte as [definições `ApplicationInsightsServiceOptions` configuráveis](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) para obter a lista mais atualizada.

### <a name="sampling"></a>Amostragem

O SDK de Insights de Aplicação para ASP.NET Core suporta amostras de taxa fixa e adaptável. A amostragem adaptativa é ativada por defeito. 

Para mais informações, consulte a [amostragem adaptável da Configuração para ASP.NET aplicações Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Adicionar Ininicializadores de Telemetria

Use iniciais de [telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) quando quiser definir propriedades globais que são enviadas com toda a telemetria.

Adicione qualquer `TelemetryInitializer` novo `DependencyInjection` ao recipiente, como mostrado no seguinte código. O SDK recolhe automaticamente `TelemetryInitializer` qualquer um que `DependencyInjection` seja adicionado ao recipiente.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>Remoção de TelemettryInitializers

Os iniciais de telemetria estão presentes por defeito. Para remover todos ou rubricas específicos de telemetria, `AddApplicationInsightsTelemetry()`utilize o seguinte código de amostra *depois* de ligar .

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

### <a name="adding-telemetry-processors"></a>Adicionar processadores de telemetria

Pode adicionar processadores de telemetria `TelemetryConfiguration` personalizados `AddApplicationInsightsTelemetryProcessor` `IServiceCollection`utilizando o método de extensão em . Utiliza processadores de telemetria em [cenários de filtragem avançados.](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) Use o seguinte exemplo.

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configurar ou remover os Módulos de Telemetria predefinidos

Aplicação Insights utiliza módulos de telemetria para recolher automaticamente telemetria útil sobre cargas de trabalho específicas sem necessitar de rastreio manual por parte do utilizador.

Os seguintes módulos de recolha automática são ativados por defeito. Estes módulos são responsáveis pela recolha automática de telemetria. Pode desativá-los ou configurá-los para alterar o seu comportamento padrão.

* `RequestTrackingTelemetryModule`- Recolhe PedidoTelemetria a partir de pedidos web de entrada.
* `DependencyTrackingTelemetryModule`- Recolhe dependênciaTelemetria de chamadas de http e chamadas sql.
* `PerformanceCollectorModule`- Recolhe Balcões de Desempenho do Windows.
* `QuickPulseTelemetryModule`- Recolhe telemetria para exibição no portal Live Metrics.
* `AppServicesHeartbeatTelemetryModule`- Recolhe batimentos cardíacos (que são enviados como métricas personalizadas), sobre o ambiente do Serviço de Aplicações Azure onde a aplicação é hospedada.
* `AzureInstanceMetadataTelemetryModule`- Recolhe batimentos cardíacos (que são enviados como métricas personalizadas), sobre o ambiente Azure VM onde a aplicação é hospedada.
* `EventCounterCollectionModule`- Recolhe [Balcões de Eventos.](eventcounters.md) Este módulo é uma nova funcionalidade e está disponível na Versão SDK 2.8.0 e superior.

Para configurar `TelemetryModule`qualquer predefinição, utilize o método `ConfigureTelemetryModule<T>` de extensão, `IServiceCollection`como mostra o seguinte exemplo.

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

A partir da versão 2.12.2, [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) contém uma opção fácil de desativar qualquer um dos módulos predefinidos.

### <a name="configuring-a-telemetry-channel"></a>Configurar um canal de telemetria

O canal `ServerTelemetryChannel`predefinido é . Pode anular como mostra o exemplo seguinte.

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

Se pretender desativar a telemetria condicional `TelemetryConfiguration` e dinamicamente, poderá resolver o `DisableTelemetry` exemplo com ASP.NET recipiente de injeção de dependência do Núcleo em qualquer lugar do seu código e colocar a bandeira nele.

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

Isto acima não impede que quaisquer módulos de recolha de automóveis recolham telemetria. Apenas o envio de telemetria para Application Insights é desativado com a abordagem acima referida. Se não for desejado um determinado módulo de recolha automática, o melhor é remover o módulo de [telemetria](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="does-application-insights-support-aspnet-core-3x"></a>Os Insights de Aplicação suportam ASP.NET Core 3.X?

Sim. Atualização para [Application Insights SDK para ASP.NET](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão Core 2.8.0 ou superior. Versões mais antigas do SDK não suportam ASP.NET Core 3.X.

Além disso, se estiver a utilizar instruções baseadas no Visual Studio a partir [daqui](#enable-application-insights-server-side-telemetry-visual-studio), atualize a versão mais recente do Visual Studio 2019 (16.3.0) a bordo. As versões anteriores do Visual Studio não suportam o embarque automático para ASP.NET aplicações Core 3.X.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Como posso rastrear a telemetria que não é recolhida automaticamente?

Obtenha uma `TelemetryClient` instância usando a injeção de construtor, e ligue para o método necessário `TrackXXX()` nele. Não recomendamos a `TelemetryClient` criação de novos casos numa aplicação ASP.NET Core. Uma instância `TelemetryClient` singleton já está `DependencyInjection` registada no `TelemetryConfiguration` contentor, que partilha com o resto da telemetria. A criação de uma nova `TelemetryClient` instância só é recomendada se precisar de uma configuração separada do resto da telemetria.

O exemplo que se segue mostra como rastrear a telemetria adicional de um controlador.

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

Para obter mais informações sobre relatórios de dados personalizados em Insights de Aplicação, consulte a [referência aAPI de métricas personalizadas](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/)de Application Insights .

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Alguns modelos de estúdio visual usaram o método de extensão UseApplicationInsights() no IWebHostBuilder para ativar os Insights de Aplicação. Este uso ainda é válido?

Embora o `UseApplicationInsights()` método de extensão ainda seja suportado, é marcado obsoleto na versão 2.8.0 da Aplicação Insights SDK. Será removido na próxima versão principal do SDK. A forma recomendada de ativar a telemetria De Insights de Aplicação é utilizando `AddApplicationInsightsTelemetry()` porque fornece sobrecargas para controlar alguma configuração. Além disso, em ASP.NET `services.AddApplicationInsightsTelemetry()` aplicações Core 3.X, é a única forma de ativar os conhecimentos da aplicação.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Estou a implementar a minha aplicação ASP.NET Core para aplicações web. Devo ainda ativar a extensão de Insights de Aplicação de Aplicações Web?

Se o SDK for instalado no momento da construção, como mostrado neste artigo, não precisa de ativar a extensão de Insights de [Aplicação](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) do portal do Serviço de Aplicações. Mesmo que a extensão esteja instalada, irá recuar quando detetar que o SDK já está adicionado à aplicação. Se ativar os Insights de Aplicação a partir da extensão, não tem de instalar e atualizar o SDK. Mas se ativar os Insights de Aplicação seguindo as instruções neste artigo, terá mais flexibilidade porque:

   * Aplicação Insights telemetria continuará a funcionar em:
       * Todos os sistemas operativos, incluindo Windows, Linux e Mac.
       * Todos os modos de publicação, incluindo autossuficiente ou dependente de enquadramento.
       * Todos os quadros-alvo, incluindo o quadro completo .NET.
       * Todas as opções de hospedagem, incluindo Web Apps, VMs, Linux, contentores, Serviço Azure Kubernetes e hospedagem não-Azure.
       * Todas as versões .NET Core, incluindo versões de pré-visualização.
   * Você pode ver telemetria localmente quando você está depurando do Estúdio Visual.
   * Pode rastrear telemetria personalizada adicional `TrackXXX()` utilizando a API.
   * Tem total controlo sobre a configuração.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Posso ativar a monitorização de Insights de Aplicação utilizando ferramentas como o Status Monitor?

Não. [O Monitor de Estado](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e o Monitor de [Estado v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) suportam atualmente ASP.NET apenas 4.x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Os Insights de Aplicação estão automaticamente ativados para a minha aplicação ASP.NET Core 2.0?

O `Microsoft.AspNetCore.All` metapacote 2.0 incluía o SDK de Insights de Aplicação (versão 2.1.0). Se executar a aplicação no Visual Studio debugger, visual Studio permite Insights de aplicação e mostra telemetria localmente no próprio IDE. A telemetria não foi enviada para o serviço Application Insights a menos que fosse especificada uma chave de instrumentação. Recomendamos seguir as instruções deste artigo para ativar os Insights de Aplicação, mesmo para aplicações de 2.0.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se eu executar a minha candidatura em Linux, todos os recursos são suportados?

Sim. O suporte de funcionalidades para o SDK é o mesmo em todas as plataformas, com as seguintes exceções:

* O SDK recolhe balcões de [eventos](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) no Linux porque os Contadores de [Desempenho](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters) só são suportados no Windows. A maioria das métricas são as mesmas.
* Mesmo `ServerTelemetryChannel` que esteja ativado por padrão, se a aplicação estiver em execução no Linux ou macOS, o canal não cria automaticamente uma pasta de armazenamento local para manter a telemetria temporariamente se houver problemas de rede. Devido a esta limitação, a telemetria perde-se quando existem problemas temporários de rede ou servidor. Para contornar este problema, configure uma pasta local para o canal:

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

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Este SDK é suportado para as novas aplicações de modelo de serviço de trabalho .NET Core 3.X?

Este SDK `HttpContext`requer , e, portanto, não funciona em quaisquer aplicações não HTTP, incluindo as aplicações .NET Core 3.X Worker Service. Consulte [este](worker-service.md) documento para permitir informações sobre aplicações nestas aplicações, utilizando o recém-lançado Microsoft.ApplicationInsights.WorkerService SDK.

## <a name="open-source-sdk"></a>SDK de código aberto

[Leia e contribua para o código](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates).

## <a name="video"></a>Vídeo

- Confira este vídeo externo passo a passo para configurar os Insights de [Aplicação com .NET Core e Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) do zero.
- Confira este vídeo externo passo a passo para configurar os Insights de [Aplicação com .NET Core e Visual Studio Code](https://youtu.be/ygGt84GDync) do zero.

## <a name="next-steps"></a>Passos seguintes

* [Explore os fluxos de utilizadores](../../azure-monitor/app/usage-flows.md) para entender como os utilizadores navegam através da sua aplicação.
* [Configure uma coleção instantânea](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) para ver o estado do código fonte e variáveis no momento em que uma exceção é lançada.
* [Utilize a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar os seus próprios eventos e métricas para uma visão detalhada do desempenho e utilização da sua aplicação.
* Utilize [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) para verificar a sua aplicação constantemente de todo o mundo.
* [Injeção de dependência no núcleo ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
