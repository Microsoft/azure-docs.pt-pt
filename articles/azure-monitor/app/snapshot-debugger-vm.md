---
title: Enable Snapshot Debugger para .NET apps em Tecido de Serviço Azure, Serviço de Nuvem e Máquinas Virtuais [ Microsoft Docs
description: Ativar snapshot Debugger para .NET apps em Tecido de Serviço Azure, Serviço de Nuvem e Máquinas Virtuais
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 194a2da23c8fb405c492df8f6ee173cc97fde4ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671351"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Ativar snapshot Debugger para .NET apps em Tecido de Serviço Azure, Serviço de Nuvem e Máquinas Virtuais

Se a sua aplicação ASP.NET ou ASP.NET for executado no Serviço de Aplicações Azure, é altamente recomendado [ativar o Snapshot Debugger através da página do portal Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). No entanto, se a sua aplicação necessitar de uma configuração personalizada do Snapshot Debugger, ou de uma versão de pré-visualização do núcleo .NET, então esta instrução deve ser seguida para ***além*** das instruções para [permitir através da página do portal Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).

Se a sua aplicação for recorrida em tecido de serviço Azure, serviço de nuvem, máquinas virtuais ou máquinas no local, devem ser utilizadas as seguintes instruções. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configure a recolha de instantâneos para aplicações ASP.NET

1. Ative os Insights de [Aplicação na sua aplicação web,](../../azure-monitor/app/asp-net.md)se ainda não o fez.

2. Inclua o pacote [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet na sua aplicação.

3. Se necessário, personalizou a configuração Snapshot Debugger adicionada ao [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). A configuração padrão snapshot Debugger é quase sempre vazia e todas as definições são opcionais. Aqui está um exemplo que mostra uma configuração equivalente à configuração predefinida:

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

4. Os instantâneos são recolhidos apenas em exceções que são reportadas ao Application Insights. Em alguns casos (por exemplo, versões mais antigas da plataforma .NET), poderá ser necessário configurar a recolha de [exceções](../../azure-monitor/app/asp-net-exceptions.md#exceptions) para ver exceções com instantâneos no portal.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Configure a recolha de instantâneos para aplicações utilizando ASP.NET Core 2.0 ou superior

1. [Ative os Insights de Aplicação na sua ASP.NET aplicação web Core,](../../azure-monitor/app/asp-net-core.md)se ainda não o fez.

    > [!NOTE]
    > Certifique-se de que a sua aplicação refere a versão 2.1.1, ou mais recente, do pacote Microsoft.ApplicationInsights.AspNetCore.

2. Inclua o pacote [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet na sua aplicação.

3. Modifique a `Startup` classe da sua aplicação para adicionar e configurar o processador de telemetria do Snapshot Collector.
    1. Se a [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet versão 1.3.5 ou superior for `Startup.cs`utilizada, adicione as seguintes declarações utilizando a .

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Adicione o seguinte no final do método `Startup` ConfigureServices na classe em `Startup.cs`.

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Se o [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet versão 1.3.4 ou abaixo for `Startup.cs`utilizada, adicione as seguintes declarações usando a .

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```
    
       Adicione a `SnapshotCollectorTelemetryProcessorFactory` seguinte `Startup` aula à aula.
    
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
        Adicione `SnapshotCollectorConfiguration` os `SnapshotCollectorTelemetryProcessorFactory` e serviços ao pipeline de arranque:
    
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

4. Se necessário, personalizou a configuração Snapshot Debugger adicionando uma secção de Configuração SnapshotCollector à appsettings.json. Todas as definições na configuração Snapshot Debugger são opcionais. Aqui está um exemplo que mostra uma configuração equivalente à configuração predefinida:

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

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Configure a recolha de instantâneos para outras aplicações .NET

1. Se a sua aplicação ainda não estiver instrumentada com Insights de Aplicação, inicie-se por ativar insights de [aplicação e definir a chave](../../azure-monitor/app/windows-desktop.md)de instrumentação .

2. Adicione o pacote [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet na sua aplicação.

3. Os instantâneos são recolhidos apenas em exceções que são reportadas ao Application Insights. Pode ser necessário modificar o seu código para os denunciar. O código de tratamento de exceções depende da estrutura da sua aplicação, mas um exemplo está abaixo:
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

## <a name="next-steps"></a>Passos seguintes

- Gere tráfego para a sua aplicação que pode desencadear uma exceção. Em seguida, aguarde 10 a 15 minutos para que as fotos sejam enviadas para a instância Deinsights de Aplicação.
- Veja [as fotos](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) no portal Azure.
- Para ajuda na resolução de problemas de problemas, consulte [a resolução de problemas do Snapshot Debugger.](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)
