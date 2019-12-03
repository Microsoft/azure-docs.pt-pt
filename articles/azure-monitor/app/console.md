---
title: Aplicativo Azure insights para aplicativos de console | Microsoft Docs
description: Monitore aplicativos Web para disponibilidade, desempenho e uso.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/02/2019
ms.reviewer: lmolkova
ms.openlocfilehash: 9e198d3ea24383a532c5fbc3bfdcb1d1d7e49a92
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689043"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights para aplicativos de console .NET

[Application insights](../../azure-monitor/app/app-insights-overview.md) permite monitorar o seu aplicativo Web quanto à disponibilidade, ao desempenho e ao uso.

Você precisa de uma assinatura com [Microsoft Azure](https://azure.com). Entre com uma conta Microsoft, que você pode ter para Windows, Xbox Live ou outros serviços de nuvem da Microsoft. Sua equipe pode ter uma assinatura organizacional para o Azure: peça ao proprietário para adicioná-lo usando seu conta Microsoft.

> [!NOTE]
> Há um novo SDK Application Insights chamado [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) , que pode ser usado para habilitar Application insights para qualquer aplicativo de console. É recomendável usar este pacote e as instruções associadas [aqui](../../azure-monitor/app/worker-service.md). Esse pacote tem como destino [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)e, portanto, pode ser usado no .net Core 2,0 ou superior e .NET Framework 4.7.2 ou superior.

## <a name="getting-started"></a>Introdução

* No [portal do Azure](https://portal.azure.com), [crie um recurso do Application Insights](../../azure-monitor/app/create-new-resource.md). Para tipo de aplicativo, escolha **geral**.
* Faça uma cópia da Chave de Instrumentação. Localize a chave na lista suspensa **Essentials** do novo recurso que você criou. 
* Instale [o pacote Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) mais recente.
* Defina a chave de instrumentação em seu código antes de acompanhar qualquer telemetria (ou definir APPINSIGHTS_INSTRUMENTATIONKEY variável de ambiente). Depois disso, você deve ser capaz de rastrear manualmente a telemetria e vê-la na portal do Azure

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

* Instale a versão mais recente do pacote [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) -ele RASTREIA automaticamente http, SQL ou outras chamadas de dependência externas.

Você pode inicializar e configurar Application Insights a partir do código ou usando `ApplicationInsights.config` arquivo. Verifique se a inicialização ocorre o mais cedo possível. 

> [!NOTE]
> As instruções referentes a **ApplicationInsights. config** só são aplicáveis a aplicativos direcionados para o .NET Framework e não se aplicam a aplicativos .NET Core.

### <a name="using-config-file"></a>Usando o arquivo de configuração
Por padrão, o SDK do Application Insights procura `ApplicationInsights.config` arquivo no diretório de trabalho quando `TelemetryConfiguration` está sendo criado

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Você também pode especificar o caminho para o arquivo de configuração.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Para obter mais informações, consulte [referência do arquivo de configuração](configuration-with-applicationinsights-config.md).

Você pode obter um exemplo completo do arquivo de configuração instalando a versão mais recente do pacote [Microsoft. ApplicationInsights. WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) . Aqui está a configuração **mínima** para a coleção de dependências que é equivalente ao exemplo de código.

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

### <a name="configuring-telemetry-collection-from-code"></a>Configurando a coleta de telemetria do código
> [!NOTE]
> Não há suporte para a leitura do arquivo de configuração no .NET Core. Você pode considerar o uso [do SDK do Application insights para ASP.NET Core](../../azure-monitor/app/asp-net-core.md)

* Durante a inicialização do aplicativo, crie e configure `DependencyTrackingTelemetryModule` instância-ela deve ser singleton e deve ser preservada para o tempo de vida do aplicativo.

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
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Se você criou a configuração com o construtor de `TelemetryConfiguration()` simples, também precisará habilitar o suporte de correlação. **Ele não será necessário** se você ler a configuração do arquivo, usado `TelemetryConfiguration.CreateDefault()` ou `TelemetryConfiguration.Active`.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Talvez você também queira instalar e inicializar o módulo coletor de contador de desempenho conforme descrito [aqui](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)


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
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
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
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Passos seguintes
* [Monitore as dependências](../../azure-monitor/app/asp-net-dependencies.md) para ver se REST, SQL ou outros recursos externos estão diminuindo seu desempenho.
* [Use a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar seus próprios eventos e métricas para uma exibição mais detalhada do desempenho e do uso do seu aplicativo.
