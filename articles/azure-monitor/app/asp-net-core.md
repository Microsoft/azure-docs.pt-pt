---
title: Insights de aplicação azure para aplicações ASP.NET Core Microsoft Docs
description: Monitorize ASP.NET aplicações web Core para disponibilidade, desempenho e utilização.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 5028d95ef784b0d309880d0d05371cd42f627d7d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276312"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Insights de aplicação para aplicações ASP.NET Core

Este artigo descreve como ativar os Insights de Aplicação para uma aplicação [ASP.NET Core.](https://docs.microsoft.com/aspnet/core) Quando completar as instruções deste artigo, a Application Insights irá recolher pedidos, dependências, exceções, contadores de desempenho, batimentos cardíacos e registos da sua aplicação ASP.NET Core.

O exemplo que usaremos aqui é uma [aplicação MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) que visa `netcoreapp2.2`. Pode aplicar estas instruções a todas as aplicações ASP.NET Core.

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
> Se estiver a utilizar ASP.NET Core 3.0 juntamente com os Insights de Aplicação, utilize a versão [2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) ou superior. Esta é a única versão que suporta ASP.NET Core 3.0.

## <a name="prerequisites"></a>Pré-requisitos

- Uma aplicação central ASP.NET funcional. Se precisar de criar uma aplicação ASP.NET Core, siga este [tutorial ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started/).
- Uma chave de instrumentação de insights de aplicação válida. Esta chave é necessária para enviar qualquer telemetria para Application Insights. Se precisar de criar um novo recurso Application Insights para obter uma chave de instrumentação, consulte [Criar um recurso Deinsights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)de Aplicação .

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Ativar a telemetria do lado do servidor do servidor Insights (Estúdio Visual)

1. Abra o seu projeto no Estúdio Visual.

    > [!TIP]
    > Se quiser, pode configurar o controlo de origem do seu projeto para que possa acompanhar todas as alterações que a Application Insights faz. Para ativar o controlo de fonte, selecione **File** > **Adicionar ao Controlo de Origem**.

2. Selecione **Project** > **Adicionar Insights de Aplicação Telemetria**.

3. Selecione **Começar**. O texto desta seleção pode variar, dependendo da sua versão do Estúdio Visual. Algumas versões anteriores usam um botão **Start Free.**

4. Selecione a sua subscrição. Em seguida, selecione **Recursos** > **Registar**.

5. Depois de adicionar Insights de Aplicação ao seu projeto, verifique para confirmar que está a utilizar a mais recente versão estável do SDK. Vá ao **Project** > **Manage NuGet Packages** > **Microsoft.ApplicationInsights.AspNetCore**. Se precisar, escolha **Atualizar**.

     ![Screenshot mostrando onde selecionar o pacote Deinsights de Aplicação para atualização](./media/asp-net-core/update-nuget-package.png)

6. Se seguiu a dica opcional e adicionou o seu projeto ao controlo de fontes, vá ao **View** > **Team Explorer** > **Changes**. Em seguida, selecione cada ficheiro para ver uma visão difusa das alterações efetuadas pela telemetria Application Insights.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Ativar a telemetria do lado do servidor De Insights (sem Estúdio Visual)

1. Instale o [pacote NuGet SDK SDK Para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Recomendamos que use sempre a versão mais recente e estável. Encontre notas de lançamento completas para o SDK no [repo gitHub de código aberto](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    A amostra de código que se segue mostra as alterações a adicionar ao ficheiro `.csproj` do seu projeto.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
        </ItemGroup>
    ```

2. Adicione `services.AddApplicationInsightsTelemetry();` ao método `ConfigureServices()` na sua aula de `Startup`, como neste exemplo:

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

    Embora possa fornecer a chave de instrumentação como argumento para `AddApplicationInsightsTelemetry`, recomendamos que especifique a chave de instrumentação na configuração. A amostra de código que se segue mostra como especificar uma chave de instrumentação em `appsettings.json`. Certifique-se de que `appsettings.json` é copiado para a pasta raiz da aplicação durante a publicação.

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

    Normalmente, `APPINSIGHTS_INSTRUMENTATIONKEY` especifica a chave de instrumentação para aplicações implementadas nas Aplicações Web do Azure.

    > [!NOTE]
    > Uma chave de instrumentação especificada no código ganha sobre a variável ambiental `APPINSIGHTS_INSTRUMENTATIONKEY`, que ganha sobre outras opções.

## <a name="run-your-application"></a>Executar a aplicação

Faça o seu pedido e faça-lhe pedidos. A telemetria deve agora fluir para a Aplicação Insights. O Application Insights SDK recolhe automaticamente pedidos web de entrada para a sua aplicação, juntamente com a seguinte telemetria também.

### <a name="live-metrics"></a>Métricas Ao Vivo

[As Métricas Vivas](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) podem ser usadas para verificar rapidamente se a monitorização de Insights de Aplicação está configurada corretamente. Embora possa demorar alguns minutos até que a telemetria comece a aparecer no portal e na análise, a Live Metrics mostraria o uso do CPU do processo de execução em quase tempo real. Também pode mostrar outras telemetrias como Pedidos, Dependências, Vestígios, etc.

### <a name="ilogger-logs"></a>Registos ILogger

Os registos emitidos através de `ILogger` de gravidade `Warning` ou superior são automaticamente capturados. Siga os [docs iLogger](ilogger.md#control-logging-level) para personalizar quais os níveis de registo capturados pela Application Insights.

### <a name="dependencies"></a>Dependências

A recolha de dependência é ativada por defeito. [Este](asp-net-dependencies.md#automatically-tracked-dependencies) artigo explica as dependências que são recolhidas automaticamente, e também contêm passos para fazer o rastreio manual.

### <a name="performance-counters"></a>Contadores de desempenho

O suporte para contadores de [desempenho](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) em ASP.NET Core é limitado:

* As versões SDK 2.4.1 e posteriormente recolhem contadores de desempenho se a aplicação estiver em execução em Aplicações Web Do Azure (Windows).
* As versões SDK 2.7.1 e posteriormente recolhem contadores de desempenho se a aplicação estiver a funcionar no Windows e os alvos `NETSTANDARD2.0` ou posteriormente.
* Para aplicações direcionadas para o .NET Framework, todas as versões dos contadores de desempenho de suporte SDK.
* Versões SDK 2.8.0 e posteriormente suporte cpu/contador de memória em Linux. Nenhum outro contador é apoiado em Linux. A forma recomendada de obter contadores de sistemas em Linux (e outros ambientes não Windows) é usando [EventCounters](#eventcounter)

### <a name="eventcounter"></a>Contador de Eventos

`EventCounterCollectionModule` está ativado por padrão, e irá recolher um conjunto padrão de contadores de aplicações .NET Core 3.0. O tutorial [EventCounter](eventcounters.md) lista o conjunto padrão de contadores recolhidos. Também tem instruções sobre personalizar a lista.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Ativar a telemetria do lado do cliente para aplicações web

Os passos anteriores são suficientes para ajudá-lo a começar a recolher a telemetria do lado do servidor. Se a sua aplicação tiver componentes do lado do cliente, siga os próximos passos para começar a recolher [telemetria](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)de utilização .

1. Em `_ViewImports.cshtml`, adicione a injeção:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. Em `_Layout.cshtml`, insira `HtmlHelper` no final da secção `<head>`, mas antes de qualquer outro script. Se pretender reportar qualquer telemetria JavaScript personalizada da página, injete-a depois deste corte:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
Em alternativa, utilizar o `FullScript` o `ScriptBody` está disponível a partir de SDK v2.14. Utilize isto se precisar de controlar a etiqueta `<script>` para definir uma Política de Segurança de Conteúdo:

    ```cshtml
        <script> // apply custom changes to this script tag.
            @Html.Raw(JavaScriptSnippet.ScriptBody)
        </script>
    ```

Os nomes de ficheiros `.cshtml` referenciados anteriormente são de um modelo de aplicação padrão do MVC. Em última análise, se pretender ativar adequadamente a monitorização do lado do cliente para a sua aplicação, o corte JavaScript deve aparecer na secção `<head>` de cada página da sua aplicação que pretende monitorizar. Pode atingir este objetivo para este modelo de aplicação adicionando o snippet JavaScript a `_Layout.cshtml`. 

Se o seu projeto não incluir `_Layout.cshtml`, ainda pode adicionar [monitorização do lado do cliente.](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring) Pode fazê-lo adicionando o corte JavaScript a um ficheiro equivalente que controla o `<head>` de todas as páginas dentro da sua aplicação. Ou pode adicionar o corte a várias páginas, mas esta solução é difícil de manter e geralmente não o recomendamos.

## <a name="configure-the-application-insights-sdk"></a>Configure o SDK de Insights de Aplicação

Pode personalizar o SDK de Insights de Aplicação para ASP.NET Core para alterar a configuração predefinida. Os utilizadores do Programa insights de aplicação ASP.NET SDK podem estar familiarizados com a alteração da configuração utilizando `ApplicationInsights.config` ou modificando `TelemetryConfiguration.Active`. Muda a configuração de forma diferente para ASP.NET Core. Adicione o ASP.NET Core SDK à aplicação e configure-o utilizando ASP.NET injeção de [dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)incorporada do Núcleo . Faça quase todas as alterações de configuração no método `ConfigureServices()` da sua classe `Startup.cs`, a menos que seja dirigido de outra forma. As seguintes secções oferecem mais informações.

> [!NOTE]
> Em ASP.NET aplicações Core, alterar a configuração modificando `TelemetryConfiguration.Active` não é suportado.

### <a name="using-applicationinsightsserviceoptions"></a>Utilização de Opções de Serviço sintetizá-lo

Pode modificar algumas configurações comuns passando `ApplicationInsightsServiceOptions` para `AddApplicationInsightsTelemetry`, como neste exemplo:

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

Lista completa de definições em `ApplicationInsightsServiceOptions`

|Definição | Descrição | Predefinição
|---------------|-------|-------
|EnableQuickPulseMetricStream | Ativar/desativar a funcionalidade LiveMetrics | true
|EnableAdaptiveSampling | Ativar/desativar a amostragem adaptativa | true
|EnableHeartbeat | Ativar/Desativar a funcionalidade Heartbeats, que periodicamente (padrão de 15 min) envia uma métrica personalizada chamada 'HeartBeatState' com informações sobre o tempo de execução como .NET Version, Azure Environment information, se aplicável, etc. | true
|Extrator métrico addautocollected | Ativar/Desativar o extrator de métricas automáticas, que é um Processador de Telemetria que envia métricas pré-agregadas sobre Pedidos/Dependências antes da amostragem. | true
|RequestCollectionOptions.TrackExceptions | Ativar/desativar o relato do rastreio de exceção não manipulado pelo módulo de recolha de pedidos. | falso em NETSTANDARD2.0 (porque as exceções são rastreadas com ApplicationInsightsLoggerProvider), verdadeiramente de outra forma.

Consulte as [definições configuráveis em `ApplicationInsightsServiceOptions`](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) para obter a lista mais atualizada.

### <a name="sampling"></a>Amostragem

O SDK de Insights de Aplicação para ASP.NET Core suporta amostras de taxa fixa e adaptável. A amostragem adaptativa é ativada por defeito. 

Para mais informações, consulte a [amostragem adaptável da Configuração para ASP.NET aplicações Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Adicionar Ininicializadores de Telemetria

Use iniciais de [telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) quando quiser definir propriedades globais que são enviadas com toda a telemetria.

Adicione qualquer nova `TelemetryInitializer` ao recipiente `DependencyInjection`, conforme mostrado no seguinte código. O SDK recolhe automaticamente qualquer `TelemetryInitializer` que seja adicionada ao recipiente `DependencyInjection`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>Remoção de TelemettryInitializers

Os iniciais de telemetria estão presentes por defeito. Para remover todos ou rubricas específicos de telemetria, utilize o seguinte código de amostra *depois* de ligar para `AddApplicationInsightsTelemetry()`.

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

Pode adicionar processadores de telemetria personalizados a `TelemetryConfiguration` utilizando o método de extensão `AddApplicationInsightsTelemetryProcessor` em `IServiceCollection`. Utiliza processadores de telemetria em [cenários de filtragem avançados.](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) Use o seguinte exemplo.

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

* `RequestTrackingTelemetryModule` - Recolhe SolicitTelemetria a partir de pedidos web de entrada.
* `DependencyTrackingTelemetryModule` - Recolhe dependênciaTelemetria de chamadas de http de saída e chamadas sql.
* `PerformanceCollectorModule` - Recolhe Balcões de Desempenho do Windows.
* `QuickPulseTelemetryModule` - Recolhe telemetria para exibição no portal Live Metrics.
* `AppServicesHeartbeatTelemetryModule` - Recolhe batimentos cardíacos (que são enviados como métricas personalizadas), sobre o ambiente do Serviço de Aplicações Azure onde a aplicação é hospedada.
* `AzureInstanceMetadataTelemetryModule` - Recolhe batimentos cardíacos (que são enviados como métricas personalizadas), sobre o ambiente Azure VM onde a aplicação é hospedada.
* `EventCounterCollectionModule` - Recolhe [Balcões de Eventos.](eventcounters.md) Este módulo é uma nova funcionalidade e está disponível na Versão SDK 2.8.0 e superior.

Para configurar qualquer `TelemetryModule`predefinido, utilize o método de extensão `ConfigureTelemetryModule<T>` `IServiceCollection`, como se pode ver no exemplo seguinte.

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

### <a name="configuring-a-telemetry-channel"></a>Configurar um canal de telemetria

O canal predefinido é `ServerTelemetryChannel`. Pode anular como mostra o exemplo seguinte.

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

Se pretender desativar a telemetria condicional e dinamicamente, poderá resolver `TelemetryConfiguration` instância com ASP.NET recipiente de injeção de dependência do Núcleo em qualquer lugar do seu código e colocar `DisableTelemetry` bandeira nele.

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

### <a name="does-application-insights-support-aspnet-core-30"></a>Os Insights de Aplicação suportam ASP.NET Core 3.0?

Sim. Atualização para [Application Insights SDK para ASP.NET](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão Core 2.8.0 ou superior. Versões mais antigas do SDK não suportam ASP.NET Core 3.0.

Além disso, se estiver a utilizar instruções baseadas no Visual Studio a partir [daqui](#enable-application-insights-server-side-telemetry-visual-studio), atualize a versão mais recente do Visual Studio 2019 (16.3.0) a bordo. As versões anteriores do Visual Studio não suportam o embarque automático para ASP.NET aplicações Core 3.0.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Como posso rastrear a telemetria que não é recolhida automaticamente?

Obtenha uma instância de `TelemetryClient` usando a injeção de construtor, e chame o método `TrackXXX()` necessário nele. Não recomendamos a criação de novos `TelemetryClient` instâncias numa aplicação ASP.NET Core. Um caso singleton de `TelemetryClient` já está registado no contentor `DependencyInjection`, que partilha `TelemetryConfiguration` com o resto da telemetria. A criação de um novo `TelemetryClient` instância só é recomendada se precisar de uma configuração separada do resto da telemetria.

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

Embora o método de extensão `UseApplicationInsights()` ainda seja suportado, é marcado obsoleto na versão 2.8.0 da Aplicação Insights SDK. Será removido na próxima versão principal do SDK. A forma recomendada de ativar a telemetria De Insights de Aplicação é utilizando `AddApplicationInsightsTelemetry()` porque fornece sobrecargas para controlar alguma configuração. Além disso, em ASP.NET aplicações Core 3.0, `services.AddApplicationInsightsTelemetry()` é a única forma de permitir informações sobre aplicações.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Estou a implementar a minha aplicação ASP.NET Core para aplicações web. Devo ainda ativar a extensão de Insights de Aplicação de Aplicações Web?

Se o SDK for instalado no momento da construção, como mostrado neste artigo, não precisa de ativar a extensão de Insights de [Aplicação](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) do portal do Serviço de Aplicações. Mesmo que a extensão esteja instalada, irá recuar quando detetar que o SDK já está adicionado à aplicação. Se ativar os Insights de Aplicação a partir da extensão, não tem de instalar e atualizar o SDK. Mas se ativar os Insights de Aplicação seguindo as instruções neste artigo, terá mais flexibilidade porque:

   * Aplicação Insights telemetria continuará a funcionar em:
       * Todos os sistemas operativos, incluindo Windows, Linux e Mac.
       * Todos os modos de publicação, incluindo autossuficiente ou dependente de enquadramento.
       * Todos os quadros-alvo, incluindo o quadro completo .NET.
       * Todas as opções de hospedagem, incluindo Web Apps, VMs, Linux, contentores, Serviço Azure Kubernetes e hospedagem não-Azure.
       * Todas as versões .NET Core, incluindo versões de pré-visualização.
   * Você pode ver telemetria localmente quando você está depurando do Estúdio Visual.
   * Pode rastrear telemetria personalizada adicional utilizando a API `TrackXXX()`.
   * Tem total controlo sobre a configuração.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Posso ativar a monitorização de Insights de Aplicação utilizando ferramentas como o Status Monitor?

Não. [O Monitor de Estado](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e o Monitor de [Estado v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) suportam atualmente ASP.NET apenas 4.x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Os Insights de Aplicação estão automaticamente ativados para a minha aplicação ASP.NET Core 2.0?

O metapacote `Microsoft.AspNetCore.All` 2.0 incluía o Application Insights SDK (versão 2.1.0). Se executar a aplicação no Visual Studio debugger, visual Studio permite Insights de aplicação e mostra telemetria localmente no próprio IDE. A telemetria não foi enviada para o serviço Application Insights a menos que fosse especificada uma chave de instrumentação. Recomendamos seguir as instruções deste artigo para ativar os Insights de Aplicação, mesmo para aplicações de 2.0.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se eu executar a minha candidatura em Linux, todos os recursos são suportados?

Sim. O suporte de funcionalidades para o SDK é o mesmo em todas as plataformas, com as seguintes exceções:

* O SDK recolhe balcões de [eventos](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) no Linux porque os Contadores de [Desempenho](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters) só são suportados no Windows. A maioria das métricas são as mesmas.
* Mesmo `ServerTelemetryChannel` esteja ativado por padrão, se a aplicação estiver a funcionar em Linux ou MacOS, o canal não cria automaticamente uma pasta de armazenamento local para manter a telemetria temporariamente se houver problemas de rede. Devido a esta limitação, a telemetria perde-se quando existem problemas temporários de rede ou servidor. Para contornar este problema, configure uma pasta local para o canal:

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

### <a name="is-this-sdk-supported-for-the-new-net-core-30-worker-service-template-applications"></a>Este SDK é suportado para as novas aplicações de modelo de serviço de trabalho .NET Core 3.0?

Este SDK requer `HttpContext`, e por isso não funciona em quaisquer aplicações não HTTP, incluindo as aplicações .NET Core 3.0 Worker Service. Consulte [este](worker-service.md) documento para permitir informações sobre aplicações nestas aplicações, utilizando o recém-lançado Microsoft.ApplicationInsights.WorkerService SDK.

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
