---
title: Ativar o Snapshot Debugger para aplicações .NET em Azure Service Fabric, Cloud Service e Virtual Machines [ Microsoft Docs
description: Ativar o Snapshot Debugger para aplicações .NET em Azure Service Fabric, Cloud Service e Virtual Machines
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 4bccc2922cf20262149ef54fbe2a1a821d9551ab
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673506"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Ativar o Snapshot Debugger para aplicações .NET em Azure Service Fabric, Cloud Service e Virtual Machines

Se a sua aplicação principal ASP.NET ou ASP.NET for executada no Azure App Service, é altamente recomendado [ativar o Snapshot Debugger através da página do portal Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). No entanto, se a sua aplicação necessitar de uma configuração personalizada do Snapshot Debugger ou de uma versão de pré-visualização do núcleo .NET, então esta instrução deve ser seguida para ***além*** das instruções para [permitir através da página do portal Doe insights de aplicação](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).

Se a sua aplicação for executada em Azure Service Fabric, Cloud Service, Virtual Machines ou máquinas no local, devem ser utilizadas as seguintes instruções. 

## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configurar a recolha de instantâneos para aplicações ASP.NET

1. [Ativar o Application Insights na sua aplicação web,](./asp-net.md)se ainda não o fez.

2. Inclua o pacote [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet na sua aplicação.

3. Se necessário, personalizou a configuração Snapshot Debugger adicionada à [ApplicationInsights.config](./configuration-with-applicationinsights-config.md). A configuração padrão do Snapshot Debugger está praticamente vazia e todas as definições são opcionais. Aqui está um exemplo que mostra uma configuração equivalente à configuração padrão:

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

4. As imagens instantâneas são recolhidas apenas em exceções que são reportadas ao Application Insights. Em alguns casos (por exemplo, versões mais antigas da plataforma .NET), poderá ser necessário [configurar a coleção de exceções](./asp-net-exceptions.md#exceptions) para ver exceções com instantâneos no portal.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Configurar a recolha instantânea para aplicações que utilizem ASP.NET Core 2.0 ou superior

1. [Ativar o Application Insights na sua aplicação web Core ASP.NET,](./asp-net-core.md)se ainda não o fez.

    > [!NOTE]
    > Certifique-se de que a sua aplicação faz referência à versão 2.1.1, ou mais recente, do pacote Microsoft.ApplicationInsights.AspNetCore.

2. Inclua o pacote [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet na sua aplicação.

3. Modifique a classe da sua aplicação `Startup` para adicionar e configurar o processador de telemetria do Snapshot Collector.
    1. Se [o Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet for utilizado versão 1.3.5 ou superior, adicione as seguintes declarações utilizando `Startup.cs` .

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Adicione o seguinte no final do método ConfigureServices na `Startup` classe em `Startup.cs` .

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Se [o Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet for utilizado versão 1.3.4 ou abaixo, adicione as seguintes declarações utilizando `Startup.cs` .

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```

       Adicione a seguinte `SnapshotCollectorTelemetryProcessorFactory` classe à `Startup` aula.

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
        Adicione os `SnapshotCollectorConfiguration` serviços e `SnapshotCollectorTelemetryProcessorFactory` serviços ao pipeline de arranque:

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

4. Se necessário, personalizou a configuração snapshot Debugger adicionando uma secção snapshotCollectorConfiguration para appsettings.js. Todas as definições na configuração Snapshot Debugger são opcionais. Aqui está um exemplo que mostra uma configuração equivalente à configuração padrão:

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

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurar a recolha instantânea para outras aplicações .NET

1. Se a sua aplicação ainda não estiver instrumentada com Insights de Aplicação, [inicie-se ativando insights de aplicação e definindo a chave de instrumentação](./windows-desktop.md).

2. Adicione o pacote [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet na sua aplicação.

3. As imagens instantâneas são recolhidas apenas em exceções que são reportadas ao Application Insights. Pode ser necessário modificar o seu código para os denunciar. O código de tratamento de exceções depende da estrutura da sua aplicação, mas um exemplo é abaixo:
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

- Gere tráfego para a sua aplicação que pode desencadear uma exceção. Em seguida, aguarde 10 a 15 minutos para que as imagens sejam enviadas para a instância De Insights de Aplicação.
- Veja [as fotos](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) no portal Azure.
- Para obter ajuda para resolver problemas com o Snapshot Debugger, consulte [snapshot Debugger resolução de problemas](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

