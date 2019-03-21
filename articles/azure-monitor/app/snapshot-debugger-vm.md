---
title: Ativar o depurador de instantâneos para aplicações de .NET no Azure Service Fabric, serviço Cloud e máquinas virtuais | Documentos da Microsoft
description: Ativar o depurador de instantâneos para aplicações de .NET no Azure Service Fabric, serviço Cloud e máquinas virtuais
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
ms.author: brahmnes
ms.openlocfilehash: 4041bee71a41cee06243d53de128bcceecda5618
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2019
ms.locfileid: "58001820"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Ativar o depurador de instantâneos para aplicações de .NET no Azure Service Fabric, serviço Cloud e máquinas virtuais

Se o ASP.NET ou ASP.NET core a execução da aplicação no serviço de aplicações do Azure, as instruções abaixo também podem ser utilizadas. A menos que seu aplicativo requer uma configuração personalizada de depurador de instantâneos, é altamente recomendado [ativar o Snapshot Debugger por meio da página do portal do Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Se a sua aplicação é executada no Azure Service Fabric, serviço Cloud, máquinas virtuais ou máquinas no local, as instruções seguintes devem ser utilizadas. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configurar a recolha de instantâneos para aplicativos ASP.NET

1. [Ativar o Application Insights na sua aplicação web](../../azure-monitor/app/asp-net.md), se ainda não fez isso ainda.

2. Incluir o [snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pacote NuGet na sua aplicação.

3. Se for necessário, personalizou a configuração de depurador de instantâneos adicionada ao [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). A configuração predefinida do depurador de instantâneos é maioritariamente vazia e todas as definições são opcionais. Eis um exemplo que mostra uma configuração equivalente à configuração padrão:

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


## <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Configurar a recolha de instantâneos para aplicativos do ASP.NET Core 2.0

1. [Ativar o Application Insights na sua aplicação web do ASP.NET Core](../../azure-monitor/app/asp-net-core.md), se ainda não fez isso ainda.

    > [!NOTE]
    > Ser-se de que seu aplicativo faz referência a versão 2.1.1 ou mais recente do pacote Microsoft.ApplicationInsights.AspNetCore.

2. Incluir o [snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pacote NuGet na sua aplicação.

3. Modificar a sua aplicação `Startup` classe para adicionar e configurar o processador de telemetria do Recoletor de instantâneos.

    Adicione o seguinte utilizar as instruções `Startup.cs`

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

4. Se for necessário, a Snapshot Debugger configuração personalizada ao adicionar uma secção de SnapshotCollectorConfiguration para appSettings. Todas as definições na configuração do depurador de instantâneos são opcionais. Eis um exemplo que mostra uma configuração equivalente à configuração padrão:

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

- Gere tráfego para a aplicação que possa disparar uma exceção. Em seguida, aguarde 10 a 15 minutos para instantâneos sejam enviados para a instância do Application Insights.
- Ver [instantâneos](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json) no portal do Azure.
- Para obter ajuda com a resolução de problemas do Profiler, consulte [resolução de problemas do Snapshot Debugger](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).
