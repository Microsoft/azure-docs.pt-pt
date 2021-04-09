---
title: Azure Application Insights for ASP.NET Core applications | Microsoft Docs
description: Monitorize ASP.NET aplicações web Core para disponibilidade, desempenho e utilização.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 04/30/2020
ms.openlocfilehash: be4cbe5f95fa6901ae4299662750c232d4700a8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711506"
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
* **.NET Core versão**: Todas as versões oficialmente [suportadas](https://dotnet.microsoft.com/download/dotnet-core) .NET Core.
* **IDE**: Estúdio Visual, Código VS ou linha de comando.

> [!NOTE]
> ASP.NET Core 3.1 requer [Insights de Aplicação 2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) ou posterior.

## <a name="prerequisites"></a>Pré-requisitos

- Uma aplicação core ASP.NET funcional. Se precisar de criar uma aplicação Core ASP.NET, siga este [tutorial de ASP.NET Core](/aspnet/core/getting-started/).
- Uma chave de instrumentação válida de Insights de Aplicação. Esta chave é necessária para enviar qualquer telemetria para Insights de Aplicação. Se precisar de criar um novo recurso Application Insights para obter uma chave de instrumentação, consulte [Criar um recurso Application Insights](./create-new-resource.md).

> [!IMPORTANT]
> As novas regiões de Azure **requerem** a utilização de cordas de ligação em vez de teclas de instrumentação. [A cadeia de ligação](./sdk-connection-string.md?tabs=net) identifica o recurso com o que pretende associar os seus dados de telemetria. Também permite modificar os pontos finais que o seu recurso utilizará como destino para a sua telemetria. Terá de copiar o fio de ligação e adicioná-lo ao código da sua aplicação ou a uma variável ambiental.


## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Ativar a telemetria do lado do servidor do Application Insights (Estúdio Visual)

Para o Estúdio Visual para Mac utilize a [orientação manual.](#enable-application-insights-server-side-telemetry-no-visual-studio) Apenas a versão Windows do Visual Studio suporta este procedimento.

1. Abra o projeto no Visual Studio.

    > [!TIP]
    > Se quiser, pode configurar o controlo de origem para o seu projeto para que possa acompanhar todas as alterações que o Application Insights faz. Para ativar o controlo de origem, selecione **'Adicionar** ao Controlo  >  **de Origem'.**

2. Selecione **A**  >  **telemetria de insights de aplicação de** projetos .

3. **Selecione Começar**. O texto desta seleção pode variar, dependendo da sua versão do Visual Studio. Algumas versões anteriores usam um botão **Iniciar Livre.**

4. Selecione a sua subscrição. Em seguida, selecione **Registo de Recursos**  >  .

5. Depois de adicionar Application Insights ao seu projeto, verifique se está a utilizar a mais recente versão estável do SDK. Vá ao **Project**  >  **Manage NuGet Packages**  >  **Microsoft.ApplicationInsights.AspNetCore**. Se precisar, escolha **Update**.

     ![Screenshot mostrando onde selecionar o pacote De Insights de Aplicação para atualização](./media/asp-net-core/update-nuget-package.png)

6. Se seguiu a dica opcional e adicionou o seu projeto ao controlo de origem, vá para **Ver**  >  **Alterações do Explorador de**  >  **Equipas.** Em seguida, selecione cada ficheiro para ver uma visão difusa das alterações efetuadas pela telemetria Application Insights.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Ativar a telemetria do lado do servidor do Application Insights (sem Estúdio Visual)

1. Instale o [pacote SDK NuGet do Application Insights para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Recomendamos que utilize sempre a versão mais recente do estábulo. Encontre notas de lançamento completas para o SDK no [repo GitHub de código aberto](https://github.com/Microsoft/ApplicationInsights-dotnet/releases).

    A amostra de código que se segue mostra as alterações a adicionar ao ficheiro do seu `.csproj` projeto.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.16.0" />
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

    * `APPINSIGHTS_INSTRUMENTATIONKEY` é normalmente usado em [Azure Web Apps,](./azure-web-apps.md?tabs=net)mas também pode ser usado em todos os lugares onde este SDK é suportado. (Se estiver a fazer uma monitorização de aplicações web sem código, este formato é necessário se não estiver a utilizar as cadeias de ligação.)

    Em vez de definir as teclas de instrumentação, pode agora também utilizar [cordas de ligação](./sdk-connection-string.md?tabs=net).

    > [!NOTE]
    > Uma chave de instrumentação especificada em código vence a variável `APPINSIGHTS_INSTRUMENTATIONKEY` ambiental, que vence outras opções.

### <a name="user-secrets-and-other-configuration-providers"></a>Segredos do utilizador e outros fornecedores de configuração

Se pretender armazenar a chave de instrumentação em ASP.NET Os segredos do utilizador Core ou recuperá-la de outro fornecedor de configuração, pode utilizar a sobrecarga com um `Microsoft.Extensions.Configuration.IConfiguration` parâmetro. Por exemplo, `services.AddApplicationInsightsTelemetry(Configuration);`.
A partir do Microsoft.ApplicationInsights.AspNetCore versão [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), a chamada `services.AddApplicationInsightsTelemetry()` irá ler automaticamente a chave de instrumentação a partir da `Microsoft.Extensions.Configuration.IConfiguration` aplicação. Não há necessidade de fornecer explicitamente o `IConfiguration` .

## <a name="run-your-application"></a>Executar a aplicação

Faça o seu pedido e faça-lhe pedidos. A telemetria deve agora fluir para Insights de Aplicação. O Application Insights SDK recolhe automaticamente pedidos web de entrada na sua aplicação, juntamente com a seguinte telemetria também.

### <a name="live-metrics"></a>Live Metrics

[As Métricas Vivas](./live-stream.md) podem ser utilizadas para verificar rapidamente se a monitorização do Application Insights está configurada corretamente. Embora possa demorar alguns minutos até que a telemetria comece a aparecer no portal e analítico, as Métricas Ao Vivo mostrariam o uso do CPU do processo de execução em quase tempo real. Também pode mostrar outras telemetrias como Pedidos, Dependências, Vestígios, etc.

### <a name="ilogger-logs"></a>Troncos ILogger

A configuração padrão recolhe `ILogger` registos de gravidade `Warning` e acima. Esta configuração pode ser [personalizada.](#how-do-i-customize-ilogger-logs-collection)

### <a name="dependencies"></a>Dependências

A recolha de dependência é ativada por padrão. [Este](asp-net-dependencies.md#automatically-tracked-dependencies) artigo explica as dependências que são recolhidas automaticamente, e também contém passos para fazer o rastreio manual.

### <a name="performance-counters"></a>Contadores de desempenho

Suporte para [contadores de desempenho](./performance-counters.md) em ASP.NET Core é limitado:

* As versões SDK 2.4.1 e, posteriormente, recolhem contadores de desempenho se a aplicação estiver a ser executada em Azure Web Apps (Windows).
* As versões SDK 2.7.1 e posteriormente recolhem contadores de desempenho se a aplicação estiver a ser executada no Windows e alvos `NETSTANDARD2.0` ou posteriormente.
* Para aplicações direcionadas para o Quadro .NET, todas as versões dos contadores de desempenho de suporte SDK.
* Versões SDK 2.8.0 e posterior suporte cpu/contador de memória em Linux. Nenhum outro contador é apoiado em Linux. A forma recomendada de obter contadores de sistema em Linux (e outros ambientes não-Windows) é usando [EventCounters](#eventcounter)

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` é ativado por defeito. O tutorial [eventCounter](eventcounters.md) tem instruções sobre a configuração da lista de contadores a recolher.

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

Pode personalizar o SDK 'Insights de Aplicação' para ASP.NET Core alterar a configuração padrão. Os utilizadores do Application Insights ASP.NET SDK podem estar familiarizados com a alteração da configuração utilizando `ApplicationInsights.config` ou modificando `TelemetryConfiguration.Active` . Para ASP.NET Core, quase todas as alterações de configuração são feitas no `ConfigureServices()` método da sua `Startup.cs` classe, a menos que seja dirigida de outra forma. As seguintes secções oferecem mais informações.

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

Lista completa de configurações em `ApplicationInsightsServiceOptions`

|Definições | Descrição | Predefinição
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | Ativar/Desativar `PerformanceCounterCollectionModule` | true
|EnableRequestTrackingTelemetryModule   | Ativar/Desativar `RequestTrackingTelemetryModule` | true
|EnableEventCounterCollectionModule   | Ativar/Desativar `EventCounterCollectionModule` | true
|EnableDependencyTrackingTelemetryModule   | Ativar/Desativar `DependencyTrackingTelemetryModule` | true
|EnableAppServicesHeartbeatTelemetryModule  |  Ativar/Desativar `AppServicesHeartbeatTelemetryModule` | true
|EnableAzureInstanceMetadataTelemetryModule   |  Ativar/Desativar `AzureInstanceMetadataTelemetryModule` | true
|EnableQuickPulseMetricStream | Ativar/Desativar a funcionalidade LiveMetrics | true
|EnableAdaptiveSampling | Permitir/Desativar a amostragem adaptativa | true
|EnableHeartbeat | Ativar/Desativar a funcionalidade Heartbeats, que periodicamente (padrão de 15 min) envia uma métrica personalizada chamada 'HeartbeatState' com informações sobre o tempo de execução como .NET Version, Informações do Ambiente Azure, se aplicável, etc. | true
|AddautoCollectedMetricExtractor | Ativar/Desativar o extrator autoCollectosemétrico, que é um Processor de Telemetria que envia métricas pré-agregadas sobre Pedidos/Dependências antes da recolha de amostragem. | true
|PedidoCollectionOptions.TrackExceptions | Ativar/Desativar o rastreio de exceções não manipulado pelo módulo de recolha Do Pedido. | falso em NETSTANDARD2.0 (porque as exceções são rastreadas com ApplicationInsightsLoggerProvider), verdadeiro de outra forma.
|EnableDiagnosticsTelemetryModule | Ativar/Desativar `DiagnosticsTelemetryModule` . Desativar isto fará com que as seguintes definições sejam ignoradas; `EnableHeartbeat`, `EnableAzureInstanceMetadataTelemetryModule`, `EnableAppServicesHeartbeatTelemetryModule` | true

Consulte as [definições `ApplicationInsightsServiceOptions` configuráveis](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) para a lista mais atualizada.

### <a name="configuration-recommendation-for-microsoftapplicationinsightsaspnetcore-sdk-2150--above"></a>Recomendação de configuração para Microsoft.ApplicationInsights.AspNetCore SDK 2.15.0 & acima

A partir da versão [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0)do Microsoft.ApplicationInsights.AspNetCore SDK, a recomendação é configurar todas as definições disponíveis em `ApplicationInsightsServiceOptions` , incluindo instrumentationkey utilizando a instância de aplicações. `IConfiguration` As definições devem estar na secção "AplicaçõesInsights", como mostra o exemplo seguinte. A secção seguinte a partir de appsettings.jsna chave de instrumentação configura, e também desativa a recolha adaptativa de amostragem e contador de desempenho.

```json
{
    "ApplicationInsights": {
    "InstrumentationKey": "putinstrumentationkeyhere",
    "EnableAdaptiveSampling": false,
    "EnablePerformanceCounterCollectionModule": false
    }
}
```

Se `services.AddApplicationInsightsTelemetry(aiOptions)` for utilizado, substitui as definições de `Microsoft.Extensions.Configuration.IConfiguration` .

### <a name="sampling"></a>Amostragem

O Application Insights SDK for ASP.NET Core suporta a amostragem de taxa fixa e adaptativa. A amostragem adaptativa é ativada por defeito.

Para obter mais informações, consulte [a amostragem adaptativa configure para aplicações ASP.NET Core](./sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Adicionar TelemetriaInitializers

Utilize [inicializadores de telemetria](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) quando quiser enriquecer a telemetria com informações adicionais.

Adicione qualquer novidade `TelemetryInitializer` ao `DependencyInjection` recipiente, como mostra o seguinte código. O SDK apanha automaticamente qualquer `TelemetryInitializer` um que seja adicionado ao `DependencyInjection` contentor.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

> [!NOTE]
> `services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();` funciona para os iniciais simples. Para outros, é necessário o seguinte: `services.AddSingleton(new MyCustomTelemetryInitializer() { fieldName = "myfieldName" });`
    
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

* `RequestTrackingTelemetryModule` - Recolhe PedidoTelemetry a partir de pedidos web de entrada.
* `DependencyTrackingTelemetryModule` - Recolhe [a DependencyTelemetry](./asp-net-dependencies.md) a partir de chamadas http e chamadas de sql de saída.
* `PerformanceCollectorModule` - Coleciona o Windows PerformanceCounters.
* `QuickPulseTelemetryModule` - Coleta telemetria para exibição no portal Live Metrics.
* `AppServicesHeartbeatTelemetryModule` - Coleciona batimentos cardíacos (que são enviados como métricas personalizadas), sobre o ambiente do Azure App Service onde a aplicação é hospedada.
* `AzureInstanceMetadataTelemetryModule` - Coleciona batimentos cardíacos (que são enviados como métricas personalizadas), sobre o ambiente Azure VM onde a aplicação é hospedada.
* `EventCounterCollectionModule` - Coleciona [Os EventCounters.](eventcounters.md) Este módulo é uma nova funcionalidade e está disponível na versão SDK 2.8.0 ou superior.

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

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="does-application-insights-support-aspnet-core-3x"></a>O Application Insights suporta ASP.NET Core 3.X?

Sim. Atualização para [Insights de Aplicação SDK para ASP.NET](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão Core 2.8.0 ou superior. Versões mais antigas do SDK não suportam ASP.NET Core 3.X.

Além disso, se estiver a utilizar instruções baseadas no Visual Studio a partir [daqui,](#enable-application-insights-server-side-telemetry-visual-studio)atualize a versão mais recente do Visual Studio 2019 (16.3.0) a bordo. Versões anteriores do Visual Studio não suportam a bordo automática para ASP.NET aplicações Core 3.X.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Como posso rastrear a telemetria que não é recolhida automaticamente?

Obtenha uma instância de usar a injeção de `TelemetryClient` construtor e ligue para o método `TrackXXX()` necessário. Não recomendamos a criação de novos `TelemetryClient` ou `TelemetryConfiguration` casos numa aplicação core ASP.NET. Uma instância singleton `TelemetryClient` de já está registada no `DependencyInjection` contentor, que partilha com o resto da `TelemetryConfiguration` telemetria. A criação de um novo `TelemetryClient` caso só é recomendada se precisar de uma configuração separada do resto da telemetria.

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

### <a name="how-do-i-customize-ilogger-logs-collection"></a>Como posso personalizar a coleção de registos ILogger?

Por predefinição, apenas os registos de gravidade `Warning` e acima são automaticamente capturados. Para alterar este comportamento, substitua explicitamente a configuração de registo registador para o `ApplicationInsights` fornecedor, conforme mostrado abaixo.
A configuração seguinte permite que as Aplicaçõesinsights capturem todos os registos de gravidade `Information` e acima.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Information"
      }
    }
  }
}
```

É importante notar que o seguinte não fará com que o fornecedor applicationInsights capture `Information` registos. Isto porque o SDK adiciona um filtro de registo predefinido, instruindo `ApplicationInsights` a captura apenas `Warning` e acima. Por isso, é necessária uma substituição explícita para aplicaçõesInsights.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

Leia mais sobre [a configuração do ILogger](ilogger.md#control-logging-level).

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

N.º [Atualmente, o Monitor](./monitor-performance-live-website-now.md) de Estado e [o Monitor de Estado v2](./status-monitor-v2-overview.md) suportam ASP.NET apenas 4.x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se eu executar a minha candidatura no Linux, todas as funcionalidades são suportadas?

Sim. Suporte de funcionalidades para o SDK é o mesmo em todas as plataformas, com as seguintes exceções:

* O SDK recolhe [contadores de eventos](./eventcounters.md) no Linux porque [os Contadores de Desempenho](./performance-counters.md) só são suportados no Windows. A maioria das métricas são as mesmas.
* Apesar de `ServerTelemetryChannel` estar ativado por padrão, se a aplicação estiver em execução no Linux ou no macOS, o canal não cria automaticamente uma pasta de armazenamento local para manter a telemetria temporariamente se houver problemas de rede. Devido a esta limitação, a telemetria perde-se quando existem problemas temporários de rede ou servidor. Para contornar esta questão, configuure uma pasta local para o canal:

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

Esta limitação não é aplicável a partir de [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0) e versões mais recentes.

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Este SDK é suportado para as novas aplicações de modelo de serviço de trabalhadores .NET Core 3.X?

Este SDK requer `HttpContext` , e, portanto, não funciona em nenhuma aplicação não-HTTP, incluindo as aplicações de Serviço de Trabalhador .NET Core 3.X. Consulte [este](worker-service.md) documento para permitir informações sobre aplicações nessas aplicações, utilizando o recém-lançado Microsoft.ApplicationInsights.WorkerService SDK.

## <a name="open-source-sdk"></a>SDK de código aberto

* [Leia e contribua para o código.](https://github.com/microsoft/ApplicationInsights-dotnet)

Para obter as últimas atualizações e correções de erro [consulte as notas de lançamento](./release-notes.md).

## <a name="next-steps"></a>Passos seguintes

* [Explore os fluxos dos utilizadores](./usage-flows.md) para entender como os utilizadores navegam através da sua aplicação.
* [Configure uma coleção instantânea](./snapshot-debugger.md) para ver o estado do código fonte e as variáveis no momento em que uma exceção é lançada.
* [Utilize a API](./api-custom-events-metrics.md) para enviar os seus próprios eventos e métricas para uma visão detalhada do desempenho e utilização da sua aplicação.
* Utilize [testes de disponibilidade](./monitor-web-app-availability.md) para verificar a sua aplicação constantemente de todo o mundo.
* [Injeção de Dependências no ASP.NET Core](/aspnet/core/fundamentals/dependency-injection)
