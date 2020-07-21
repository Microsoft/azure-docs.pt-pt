---
title: Azure Application Insights for ASP.NET Core applications [ Microsoft Docs
description: Monitorize ASP.NET aplicações web Core para disponibilidade, desempenho e utilização.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 1d5ce4fe2a3ceb3235b77916aa408c681f81b0de
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517234"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Insights de Aplicação para aplicações core ASP.NET

Este artigo descreve como ativar insights de aplicação para uma aplicação [core ASP.NET.](/aspnet/core) Quando completar as instruções deste artigo, a Application Insights recolherá pedidos, dependências, exceções, contadores de desempenho, batimentos cardíacos e registos da sua aplicação Core ASP.NET.

O exemplo que vamos usar aqui é uma [aplicação MVC](/aspnet/core/tutorials/first-mvc-app) que `netcoreapp3.0` visa. Pode aplicar estas instruções a todas as ASP.NET aplicações Core. Se estiver a utilizar o [Serviço de Trabalhadores,](/aspnet/core/fundamentals/host/hosted-services#worker-service-template)utilize as instruções [daqui](./worker-service.md).

## <a name="supported-scenarios"></a>Cenários suportados

O [SDK de Insights de Aplicação para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) pode monitorizar as suas aplicações independentemente de onde ou como funcionam. Se a sua aplicação estiver em execução e tiver conectividade de rede com a Azure, a telemetria pode ser recolhida. A monitorização de Insights de Aplicação é suportada em todos os lugares .NET Core é suportado. Coberturas de apoio:
* **Sistema operativo**: Windows, Linux ou Mac.
* **Método de hospedagem**: Em processo ou fora do processo.
* **Método de implantação**: Quadro dependente ou autossuficiente.
* **Servidor web**: IIS (Internet Information Server) ou Kestrel.
* **Plataforma de hospedagem**: As Aplicações Web apresentam o Azure App Service, Azure VM, Docker, Azure Kubernetes Service (AKS), e assim por diante.
* **.NET Core Runtime :** 1.XX, 2.XX ou 3.XX
* **IDE**: Estúdio Visual, Código VS ou linha de comando.

> [!NOTE]
> ASP.NET Core 3.X requer [Insights de Aplicação 2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) ou posterior.

## <a name="prerequisites"></a>Pré-requisitos

- Uma aplicação core ASP.NET funcional. Se precisar de criar uma aplicação Core ASP.NET, siga este [tutorial de ASP.NET Core](/aspnet/core/getting-started/).
- Uma chave de instrumentação válida de Insights de Aplicação. Esta chave é necessária para enviar qualquer telemetria para Insights de Aplicação. Se precisar de criar um novo recurso Application Insights para obter uma chave de instrumentação, consulte [Criar um recurso Application Insights](./create-new-resource.md).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Ativar a telemetria do lado do servidor do Application Insights (Estúdio Visual)

1. Abra o projeto no Visual Studio.

    > [!TIP]
    > Se quiser, pode configurar o controlo de origem para o seu projeto para que possa acompanhar todas as alterações que o Application Insights faz. Para ativar o controlo de origem, selecione **'Adicionar**ao Controlo  >  **de Origem'.**

2. Selecione **A**  >  **telemetria de insights de aplicação de**projetos .

3. **Selecione Começar**. O texto desta seleção pode variar, dependendo da sua versão do Visual Studio. Algumas versões anteriores usam um botão **Iniciar Livre.**

4. Selecione a sua subscrição. Em seguida, selecione **Registo de Recursos**  >  **Register**.

5. Depois de adicionar Application Insights ao seu projeto, verifique se está a utilizar a mais recente versão estável do SDK. Vá ao **Project**  >  **Manage NuGet Packages**  >  **Microsoft.ApplicationInsights.AspNetCore**. Se precisar, escolha **Update**.

     ![Screenshot mostrando onde selecionar o pacote De Insights de Aplicação para atualização](./media/asp-net-core/update-nuget-package.png)

6. Se seguiu a dica opcional e adicionou o seu projeto ao controlo de origem, vá para **Ver**  >  **Alterações do Explorador de**  >  **Equipas.** Em seguida, selecione cada ficheiro para ver uma visão difusa das alterações efetuadas pela telemetria Application Insights.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Ativar a telemetria do lado do servidor do Application Insights (sem Estúdio Visual)

1. Instale o [pacote SDK NuGet do Application Insights para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Recomendamos que utilize sempre a versão mais recente do estábulo. Encontre notas de lançamento completas para o SDK no [repo GitHub de código aberto](https://github.com/Microsoft/ApplicationInsights-dotnet/releases).

    A amostra de código que se segue mostra as alterações a adicionar ao ficheiro do seu `.csproj` projeto.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. Adicione `services.AddApplicationInsightsTelemetry();` ao método da sua `ConfigureServices()` `Startup` classe, como neste exemplo:

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

3. Configurar a chave de instrumentação.

    Embora possa fornecer a chave de instrumentação como argumento para `AddApplicationInsightsTelemetry` , recomendamos que especifique a chave de instrumentação na configuração. A amostra de código que se segue mostra como especificar uma chave de instrumentação em `appsettings.json` . Certifique-se de que `appsettings.json` é copiado para a pasta raiz da aplicação durante a publicação.

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

    * `APPINSIGHTS_INSTRUMENTATIONKEY`é normalmente usado em [Azure Web Apps,](./azure-web-apps.md?tabs=net)mas também pode ser usado em todos os lugares onde este SDK é suportado. (Se estiver a fazer uma monitorização de aplicações web sem código, este formato é necessário se não estiver a utilizar as cadeias de ligação.)

    Em vez de definir as teclas de instrumentação, pode agora também utilizar [cordas de ligação](./sdk-connection-string.md?tabs=net).

    > [!NOTE]
    > Uma chave de instrumentação especificada em código vence a variável `APPINSIGHTS_INSTRUMENTATIONKEY` ambiental, que vence outras opções.

## <a name="run-your-application"></a>Executar a aplicação

Faça o seu pedido e faça-lhe pedidos. A telemetria deve agora fluir para Insights de Aplicação. O Application Insights SDK recolhe automaticamente pedidos web de entrada na sua aplicação, juntamente com a seguinte telemetria também.

### <a name="live-metrics"></a>Live Metrics

[As Métricas Vivas](./live-stream.md) podem ser utilizadas para verificar rapidamente se a monitorização do Application Insights está configurada corretamente. Embora possa demorar alguns minutos até que a telemetria comece a aparecer no portal e analítico, as Métricas Ao Vivo mostrariam o uso do CPU do processo de execução em quase tempo real. Também pode mostrar outras telemetrias como Pedidos, Dependências, Vestígios, etc.

### <a name="ilogger-logs"></a>Troncos ILogger

Os registos emitidos através `ILogger` de severidade `Warning` ou maior são capturados automaticamente. Siga [os docs ILogger](ilogger.md#control-logging-level) para personalizar quais os níveis de registo capturados pela Application Insights.

### <a name="dependencies"></a>Dependências

A recolha de dependência é ativada por padrão. [Este](asp-net-dependencies.md#automatically-tracked-dependencies) artigo explica as dependências que são recolhidas automaticamente, e também contém passos para fazer o rastreio manual.

### <a name="performance-counters"></a>Contadores de desempenho

Suporte para [contadores de desempenho](./web-monitor-performance.md) em ASP.NET Core é limitado:

* As versões SDK 2.4.1 e, posteriormente, recolhem contadores de desempenho se a aplicação estiver a ser executada em Azure Web Apps (Windows).
* As versões SDK 2.7.1 e posteriormente recolhem contadores de desempenho se a aplicação estiver a ser executada no Windows e alvos `NETSTANDARD2.0` ou posteriormente.
* Para aplicações direcionadas para o Quadro .NET, todas as versões dos contadores de desempenho de suporte SDK.
* Versões SDK 2.8.0 e posterior suporte cpu/contador de memória em Linux. Nenhum outro contador é apoiado em Linux. A forma recomendada de obter contadores de sistema em Linux (e outros ambientes não-Windows) é usando [EventCounters](#eventcounter)

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule`é ativado por padrão, e irá recolher um conjunto padrão de contadores a partir de aplicações .NET Core 3.X. O tutorial [do EventCounter](eventcounters.md) lista o conjunto predefinido de contadores recolhidos. Também tem instruções para personalizar a lista.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Ativar a telemetria do lado do cliente para aplicações web

Os passos anteriores são suficientes para ajudá-lo a começar a recolher telemetria do lado do servidor. Se a sua aplicação tiver componentes do lado do cliente, siga os próximos passos para começar a recolher [telemetria de utilização](./usage-overview.md).

1. Em `_ViewImports.cshtml` , adicionar injeção:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. In `_Layout.cshtml` , insira `HtmlHelper` no final da `<head>` secção, mas antes de qualquer outro script. Se pretender reportar qualquer telemetria JavaScript personalizada da página, injete-a após este corte:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
Alternativamente, a utilização `FullScript` do `ScriptBody` is disponível a partir de SDK v2.14. Utilize isto se precisar de controlar a `<script>` etiqueta para definir uma Política de Segurança de Conteúdos:

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

Os `.cshtml` nomes de ficheiros referenciados anteriormente são de um modelo de aplicação MVC predefinido. Em última análise, se pretender ativar adequadamente a monitorização do lado do cliente para a sua aplicação, o snippet JavaScript deve aparecer na `<head>` secção de cada página da sua aplicação que pretende monitorizar. Pode realizar este objetivo para este modelo de aplicação adicionando o snippet JavaScript a `_Layout.cshtml` . 

Se o seu projeto não `_Layout.cshtml` incluir, ainda pode adicionar [monitorização do lado do cliente.](./website-monitoring.md) Pode fazê-lo adicionando o snippet JavaScript a um ficheiro equivalente que controla `<head>` todas as páginas dentro da sua aplicação. Ou pode adicionar o corte a várias páginas, mas esta solução é difícil de manter e geralmente não recomendamos.

## <a name="configure-the-application-insights-sdk"></a>Configure a Aplicação Insights SDK

Pode personalizar o SDK 'Insights de Aplicação' para ASP.NET Core alterar a configuração padrão. Os utilizadores do Application Insights ASP.NET SDK podem estar familiarizados com a alteração da configuração utilizando `ApplicationInsights.config` ou modificando `TelemetryConfiguration.Active` . Altera a configuração de forma diferente para ASP.NET Core. Adicione o ASP.NET Core SDK à aplicação e configuure-o utilizando ASP.NET injeção de [dependência](/aspnet/core/fundamentals/dependency-injection)incorporada do Núcleo . Faça quase todas as alterações de configuração no `ConfigureServices()` método da sua `Startup.cs` classe, a não ser que seja direcionado de outra forma. As seguintes secções oferecem mais informações.

> [!NOTE]
> Em ASP.NET aplicações Core, alterar a configuração modificando `TelemetryConfiguration.Active` não é suportado.

### <a name="using-applicationinsightsserviceoptions"></a>Utilização de AplicaçõesInsightsServiceOptions

Pode modificar algumas configurações comuns passando `ApplicationInsightsServiceOptions` para , como neste `AddApplicationInsightsTelemetry` exemplo:

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

|Definições | Descrição | Predefinição
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | Ativar/Desativar`PerformanceCounterCollectionModule` | true
|EnableRequestTrackingTelemetryModule   | Ativar/Desativar`RequestTrackingTelemetryModule` | true
|EnableEventCounterCollectionModule   | Ativar/Desativar`EventCounterCollectionModule` | true
|EnableDependencyTrackingTelemetryModule   | Ativar/Desativar`DependencyTrackingTelemetryModule` | true
|EnableAppServicesHeartbeatTelemetryModule  |  Ativar/Desativar`AppServicesHeartbeatTelemetryModule` | true
|EnableAzureInstanceMetadataTelemetryModule   |  Ativar/Desativar`AzureInstanceMetadataTelemetryModule` | true
|EnableQuickPulseMetricStream | Ativar/Desativar a funcionalidade LiveMetrics | true
|EnableAdaptiveSampling | Permitir/Desativar a amostragem adaptativa | true
|EnableHeartbeat | Ativar/Desativar a funcionalidade Heartbeats, que periodicamente (padrão de 15 min) envia uma métrica personalizada chamada 'HeartbeatState' com informações sobre o tempo de execução como .NET Version, Informações do Ambiente Azure, se aplicável, etc. | true
|AddautoCollectedMetricExtractor | Ativar/Desativar o extrator autoCollectosemétrico, que é um Processor de Telemetria que envia métricas pré-agregadas sobre Pedidos/Dependências antes da recolha de amostragem. | true
|PedidoCollectionOptions.TrackExceptions | Ativar/Desativar o rastreio de exceções não manipulado pelo módulo de recolha Do Pedido. | falso em NETSTANDARD2.0 (porque as exceções são rastreadas com ApplicationInsightsLoggerProvider), verdadeiro de outra forma.

Consulte as [definições `ApplicationInsightsServiceOptions` configuráveis](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) para a lista mais atualizada.

### <a name="sampling"></a>Amostragem

O Application Insights SDK for ASP.NET Core suporta a amostragem de taxa fixa e adaptativa. A amostragem adaptativa é ativada por defeito. 

Para obter mais informações, consulte [a amostragem adaptativa configure para aplicações ASP.NET Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Adicionar TelemetriaInitializers

Utilize [inicializadores de telemetria](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) quando quiser enriquecer a telemetria com informações adicionais.

Adicione qualquer novidade `TelemetryInitializer` ao `DependencyInjection` recipiente, como mostra o seguinte código. O SDK apanha automaticamente qualquer `TelemetryInitializer` um que seja adicionado ao `DependencyInjection` contentor.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>Remoção de TelemetriaInitializadores

Os inicializadores de telemetria estão presentes por padrão. Para remover os inicializadores de telemetria todos ou específicos, utilize o seguinte código de amostra *depois de* ligar `AddApplicationInsightsTelemetry()` .

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

Pode adicionar processadores de telemetria personalizados `TelemetryConfiguration` utilizando o método de extensão em `AddApplicationInsightsTelemetryProcessor` `IServiceCollection` . Utiliza processadores de telemetria em [cenários avançados de filtragem](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer). Use o seguinte exemplo.

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configurar ou remover telemetrias padrão

O Application Insights utiliza módulos de telemetria para recolher automaticamente telemetria útil sobre cargas de trabalho específicas sem exigir o rastreio manual pelo utilizador.

Os seguintes módulos de recolha automática são ativados por predefinição. Estes módulos são responsáveis pela recolha automática de telemetria. Pode desativá-los ou configurá-los para alterar o seu comportamento predefinido.

* `RequestTrackingTelemetryModule`- Recolhe PedidoTelemetry a partir de pedidos web de entrada.
* `DependencyTrackingTelemetryModule`- Recolhe [a DependencyTelemetry](./asp-net-dependencies.md) a partir de chamadas http e chamadas de sql de saída.
* `PerformanceCollectorModule`- Coleciona o Windows PerformanceCounters.
* `QuickPulseTelemetryModule`- Coleta telemetria para exibição no portal Live Metrics.
* `AppServicesHeartbeatTelemetryModule`- Coleciona batimentos cardíacos (que são enviados como métricas personalizadas), sobre o ambiente do Azure App Service onde a aplicação é hospedada.
* `AzureInstanceMetadataTelemetryModule`- Coleciona batimentos cardíacos (que são enviados como métricas personalizadas), sobre o ambiente Azure VM onde a aplicação é hospedada.
* `EventCounterCollectionModule`- Coleciona [Os EventCounters.](eventcounters.md) Este módulo é uma nova funcionalidade e está disponível na versão SDK 2.8.0 ou superior.

Para configurar qualquer predefinição, `TelemetryModule` utilize o método de extensão em , como mostra o exemplo `ConfigureTelemetryModule<T>` `IServiceCollection` seguinte.

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

O canal de [telemetria](./telemetry-channels.md) predefinido é `ServerTelemetryChannel` . Pode sobrepor-se como mostra o exemplo a seguir.

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

### <a name="disable-telemetry-dynamically"></a>Desative a telemetria dinamicamente

Se pretender desativar a telemetria de forma condicional e dinâmica, poderá resolver `TelemetryConfiguration` o caso com ASP.NET recipiente de injeção de dependência do Núcleo em qualquer lugar do seu código e definir `DisableTelemetry` a sua bandeira.

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

O acima referido não impede que quaisquer módulos de recolha de automóveis recolham telemetria. Apenas o envio de telemetria para Application Insights é desativado com a abordagem acima. Se não for desejado um determinado módulo de recolha de automóveis, o melhor é [remover o módulo de telemetria](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="does-application-insights-support-aspnet-core-3x"></a>O Application Insights suporta ASP.NET Core 3.X?

Yes. Atualização para [Insights de Aplicação SDK para ASP.NET](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão Core 2.8.0 ou superior. Versões mais antigas do SDK não suportam ASP.NET Core 3.X.

Além disso, se estiver a utilizar instruções baseadas no Visual Studio a partir [daqui,](#enable-application-insights-server-side-telemetry-visual-studio)atualize a versão mais recente do Visual Studio 2019 (16.3.0) a bordo. Versões anteriores do Visual Studio não suportam a bordo automática para ASP.NET aplicações Core 3.X.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Como posso rastrear a telemetria que não é recolhida automaticamente?

Obtenha uma instância de usar a injeção de `TelemetryClient` construtor e ligue para o método `TrackXXX()` necessário. Não recomendamos a criação de novos `TelemetryClient` casos numa aplicação ASP.NET Core. Uma instância singleton `TelemetryClient` de já está registada no `DependencyInjection` contentor, que partilha com o resto da `TelemetryConfiguration` telemetria. A criação de um novo `TelemetryClient` caso só é recomendada se precisar de uma configuração separada do resto da telemetria.

O exemplo a seguir mostra como rastrear a telemetria adicional de um controlador.

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

Para obter mais informações sobre relatórios de dados personalizados em Informações de [Aplicação, consulte a referência API de métricas personalizadas do Application Insights.](./api-custom-events-metrics.md) Uma abordagem semelhante pode ser usada para o envio de métricas personalizadas para Application Insights usando a [API GetMetric](./get-metric.md).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Alguns modelos de Estúdio Visual usaram o método de extensão UseApplicationInsights () no IWebHostBuilder para ativar insights de aplicações. Este uso ainda é válido?

Embora o método de extensão `UseApplicationInsights()` ainda esteja suportado, está marcado como obsoleto na versão 2.8.0 do Application Insights SDK. Será removido na próxima versão principal do SDK. A forma recomendada de ativar a telemetria Do Application Insights é utilizando `AddApplicationInsightsTelemetry()` porque fornece sobrecargas para controlar alguma configuração. Além disso, em ASP.NET aplicações Core 3.X, `services.AddApplicationInsightsTelemetry()` é a única forma de ativar insights de aplicações.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Estou a implementar a minha aplicação core ASP.NET para aplicações web. Devo ainda ativar a extensão de Application Insights a partir de Aplicações Web?

Se o SDK for instalado no tempo de construção, como mostrado neste artigo, não necessita de ativar a extensão de Insights de [Aplicação](./azure-web-apps.md) a partir do portal do Serviço de Aplicações. Mesmo que a extensão seja instalada, irá recuar quando detetar que o SDK já está adicionado à aplicação. Se ativar o Application Insights a partir da extensão, não tem de instalar e atualizar o SDK. Mas se ativar os Insights de Aplicação seguindo as instruções deste artigo, tem mais flexibilidade porque:

   * A telemetria Application Insights continuará a funcionar em:
       * Todos os sistemas operativos, incluindo Windows, Linux e Mac.
       * Todos os modos de publicação, incluindo independente ou dependente de quadro.
       * Todos os quadros-alvo, incluindo o quadro completo .NET.
       * Todas as opções de hospedagem, incluindo Web Apps, VMs, Linux, contentores, Serviço Azure Kubernetes e hospedagem não-Azure.
       * Todas as versões .NET Core, incluindo versões de pré-visualização.
   * Pode ver a telemetria localmente quando está a depurar do Visual Studio.
   * Pode rastrear a telemetria personalizada adicional utilizando a `TrackXXX()` API.
   * Tem controlo total sobre a configuração.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Posso ativar a monitorização de Insights de Aplicação utilizando ferramentas como o Status Monitor?

Não. [Atualmente, o Monitor](./monitor-performance-live-website-now.md) de Estado e [o Monitor de Estado v2](./status-monitor-v2-overview.md) suportam ASP.NET apenas 4.x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>O Application Insights está automaticamente ativado para a minha aplicação Core 2.0 ASP.NET?

A `Microsoft.AspNetCore.All` metapackage 2.0 incluía o Application Insights SDK (versão 2.1.0). Se executar a aplicação no depurgger Visual Studio, o Visual Studio permite insights de aplicação e mostra telemetria localmente no próprio IDE. A telemetria não foi enviada para o serviço application insights a menos que fosse especificada uma chave de instrumentação. Recomendamos seguir as instruções deste artigo para ativar o Application Insights, mesmo para aplicações 2.0.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se eu executar a minha candidatura no Linux, todas as funcionalidades são suportadas?

Yes. Suporte de funcionalidades para o SDK é o mesmo em todas as plataformas, com as seguintes exceções:

* O SDK recolhe [contadores de eventos](./eventcounters.md) no Linux porque [os Contadores de Desempenho](./performance-counters.md) só são suportados no Windows. A maioria das métricas são as mesmas.
* Apesar de `ServerTelemetryChannel` estar ativado por padrão, se a aplicação estiver em execução no Linux ou no MacOS, o canal não cria automaticamente uma pasta de armazenamento local para manter a telemetria temporariamente se houver problemas de rede. Devido a esta limitação, a telemetria perde-se quando existem problemas temporários de rede ou servidor. Para contornar esta questão, configuure uma pasta local para o canal:

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

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Este SDK é suportado para as novas aplicações de modelo de serviço de trabalhadores .NET Core 3.X?

Este SDK requer `HttpContext` , e, portanto, não funciona em nenhuma aplicação não-HTTP, incluindo as aplicações de Serviço de Trabalhador .NET Core 3.X. Consulte [este](worker-service.md) documento para permitir informações sobre aplicações nessas aplicações, utilizando o recém-lançado Microsoft.ApplicationInsights.WorkerService SDK.

## <a name="open-source-sdk"></a>SDK de código aberto

[Leia e contribua para o código.](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates)

## <a name="next-steps"></a>Próximos passos

* [Explore os fluxos dos utilizadores](../../azure-monitor/app/usage-flows.md) para entender como os utilizadores navegam através da sua aplicação.
* [Configure uma coleção instantânea](./snapshot-debugger.md) para ver o estado do código fonte e as variáveis no momento em que uma exceção é lançada.
* [Utilize a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar os seus próprios eventos e métricas para uma visão detalhada do desempenho e utilização da sua aplicação.
* Utilize [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) para verificar a sua aplicação constantemente de todo o mundo.
* [Injeção de dependência no núcleo ASP.NET](/aspnet/core/fundamentals/dependency-injection)
