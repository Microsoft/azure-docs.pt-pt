---
title: O Azure Application Insights para aplicações de consola | Documentos da Microsoft
description: Monitorizar aplicações web de disponibilidade, desempenho e utilização.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/30/2019
ms.reviewer: lmolkova
ms.author: mbullwin
ms.openlocfilehash: 602cd9696271931babad9aa962638c5b646c80ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60901854"
---
# <a name="application-insights-for-net-console-applications"></a>Aplicações de consola do Application Insights para .NET
[O Application Insights](../../azure-monitor/app/app-insights-overview.md) permite-lhe monitorizar a sua aplicação web de disponibilidade, desempenho e utilização.

Precisa de uma subscrição com [Microsoft Azure](https://azure.com). Inicie sessão com uma conta Microsoft, que poderá ter para o Windows, Xbox Live ou outros serviços cloud da Microsoft. Sua equipe pode ter uma subscrição organizacional do Azure: peça ao proprietário para adicionar à mesma com a sua conta Microsoft.

## <a name="getting-started"></a>Introdução

* No [portal do Azure](https://portal.azure.com), [crie um recurso do Application Insights](../../azure-monitor/app/create-new-resource.md ). Para o tipo de aplicação, escolha **gerais**.
* Faça uma cópia da Chave de Instrumentação. Localizar a chave no **Essentials** pendente do novo recurso que criou. 
* Instalar a versão mais recente [applicationinsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) pacote.
* Definir a chave de instrumentação no código antes de qualquer telemetria de controlo (ou variável de ambiente APPINSIGHTS_INSTRUMENTATIONKEY conjunto). Depois disso, será capaz de controlar a telemetria e vê-lo no portal do Azure manualmente

```csharp
TelemetryConfiguration.Active.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient();
telemetryClient.TrackTrace("Hello World!");
```

* Instalar a versão mais recente do [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) pacote - controla automaticamente HTTP, o SQL ou outras chamadas de dependência externa.

Pode inicializar e configurar o Application Insights a partir do código ou com `ApplicationInsights.config` ficheiro. Certifique-se de inicialização acontece mais cedo possível. 

> [!NOTE]
> Instruções que faça referência a **applicationinsights. config** só são aplicáveis aos aplicativos que visam o .NET Framework e não se aplicam às aplicações de .NET Core.

### <a name="using-config-file"></a>Utilizar o ficheiro de configuração

Por predefinição, o SDK do Application Insights procura por `ApplicationInsights.config` ficheiros no diretório de trabalho quando `TelemetryConfiguration` está a ser criada

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Também pode especificar o caminho para o ficheiro de configuração.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Para obter mais informações, consulte [referência de ficheiro de configuração](configuration-with-applicationinsights-config.md).

Pode obter um exemplo completo do ficheiro de configuração ao instalar a versão mais recente do [Windowsserver](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) pacote. Aqui está o **mínima** configuração para a coleção de dependência, que é equivalente ao exemplo de código.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Configurar a recolha de telemetria a partir do código

* Durante a inicialização do aplicativo criar e configurar `DependencyTrackingTelemetryModule` instância - ele tem de ser singleton e têm de ser preservado para a vida útil do aplicativo.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Adicionar inicializadores de telemetria comuns

```csharp
// stamps telemetry with correlation identifiers
TelemetryConfiguration.Active.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());

// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
TelemetryConfiguration.Active.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

* Para a aplicação do Windows do .NET Framework, também pode instalar e inicializar o módulo de recoletor de contador de desempenho, tal como descrito [aqui](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)

#### <a name="full-example"></a>Exemplo completo

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.Active;

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient();
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking so wait a bit
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Passos Seguintes
* [Monitorizar dependências](../../azure-monitor/app/asp-net-dependencies.md) para ver se REST, SQL ou outros recursos externos estão a atrasar.
* [Utilizar a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar os seus próprios eventos e métricas para uma visão mais detalhada do desempenho e a utilização da sua aplicação.
