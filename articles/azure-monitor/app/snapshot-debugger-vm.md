---
title: Habilitar Depurador de Instantâneos para aplicativos .NET no Service Fabric do Azure, serviço de nuvem e máquinas virtuais | Microsoft Docs
description: Habilitar Depurador de Instantâneos para aplicativos .NET no Azure Service Fabric, serviço de nuvem e máquinas virtuais
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: bfung
ms.openlocfilehash: 5a6cf763ae16b55806df2acaf2e03fd8c13d1e76
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359286"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Habilitar Depurador de Instantâneos para aplicativos .NET no Azure Service Fabric, serviço de nuvem e máquinas virtuais

Se o aplicativo ASP.NET ou ASP.NET Core for executado no Azure App Service, é altamente recomendável [habilitar depurador de instantâneos por meio da página do portal do Application insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). No entanto, se seu aplicativo exigir uma configuração de Depurador de Instantâneos personalizada ou uma versão de visualização do .NET Core, essa instrução deverá ser seguida, ***além*** das instruções para [habilitar por meio do portal de Application insights página](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).

Se seu aplicativo for executado no Azure Service Fabric, serviço de nuvem, máquinas virtuais ou máquinas locais, as instruções a seguir deverão ser usadas. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configurar a recolha de instantâneos para aplicativos ASP.NET

1. [Ativar o Application Insights na sua aplicação web](../../azure-monitor/app/asp-net.md), se ainda não fez isso ainda.

2. Incluir o [snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pacote NuGet na sua aplicação.

3. Se necessário, personalize a configuração de Depurador de Instantâneos adicionada a [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). A configuração padrão de Depurador de Instantâneos está quase sempre vazia e todas as configurações são opcionais. Veja um exemplo que mostra uma configuração equivalente à configuração padrão:

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Os instantâneos são coletados apenas em exceções que são enviadas para o Application Insights. Em alguns casos (por exemplo, as versões mais antigas da plataforma .NET), talvez seja preciso [configurar a recolha de exceção](../../azure-monitor/app/asp-net-exceptions.md#exceptions) para ver as exceções com instantâneos no portal.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Configurar a coleta de instantâneos para aplicativos usando o ASP.NET Core 2,0 ou superior

1. [Ativar o Application Insights na sua aplicação web do ASP.NET Core](../../azure-monitor/app/asp-net-core.md), se ainda não fez isso ainda.

    > [!NOTE]
    > Ser-se de que seu aplicativo faz referência a versão 2.1.1 ou mais recente do pacote Microsoft.ApplicationInsights.AspNetCore.

2. Incluir o [snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pacote NuGet na sua aplicação.

3. Modificar a sua aplicação `Startup` classe para adicionar e configurar o processador de telemetria do Recoletor de instantâneos.
    1. Se [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet Package versão 1.3.5 ou superior for usado, adicione as seguintes instruções using ao `Startup.cs`.

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Adicione o seguinte ao final do método configureservices na `Startup` classe em. `Startup.cs`

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Se [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet Package versão 1.3.4 ou abaixo for usado, adicione as seguintes instruções using ao `Startup.cs`.

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```
    
       Adicione as seguintes `SnapshotCollectorTelemetryProcessorFactory` classe `Startup` classe.
    
       ```csharp
       class Startup
       {
           private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
           {
               private readonly IServiceProvider _serviceProvider;
    
               public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
                   _serviceProvider = serviceProvider;
    
               public ITelemetryProcessor Create(ITelemetryProcessor next)
               {
                   var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
                   return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
               }
           }
           ...
        ```
        Adicionar a `SnapshotCollectorConfiguration` e `SnapshotCollectorTelemetryProcessorFactory` serviços para o pipeline de arranque:
    
        ```csharp
           // This method gets called by the runtime. Use this method to add services to the container.
           public void ConfigureServices(IServiceCollection services)
           {
               // Configure SnapshotCollector from application settings
               services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));
    
               // Add SnapshotCollector telemetry processor.
               services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));
    
               // TODO: Add other services your application needs here.
           }
       }
       ```

4. Se necessário, personalize a configuração de Depurador de Instantâneos adicionando uma seção SnapshotCollectorConfiguration a appSettings. JSON. Todas as configurações na configuração de Depurador de Instantâneos são opcionais. Veja um exemplo que mostra uma configuração equivalente à configuração padrão:

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurar a recolha de instantâneos para outras aplicações de .NET

1. Se a sua aplicação já não é instrumentada com o Application Insights, comece por [ativar o Application Insights e definir a chave de instrumentação](../../azure-monitor/app/windows-desktop.md).

2. Adicionar a [snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pacote NuGet na sua aplicação.

3. Os instantâneos são coletados apenas em exceções que são enviadas para o Application Insights. Terá de modificar o código para relatá-los. A código de manipulação de exceção depende da estrutura do seu aplicativo, mas um exemplo é abaixo:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>Passos Seguintes

- Gere o tráfego para o aplicativo que pode disparar uma exceção. Em seguida, aguarde de 10 a 15 minutos para que os instantâneos sejam enviados para a instância de Application Insights.
- Consulte [instantâneos](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) na portal do Azure.
- Para obter ajuda com a solução de problemas de Depurador de Instantâneos, consulte [depurador de instantâneos solução de problemas](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).
