---
title: Azure Application Insights para aplicações de consolas / Microsoft Docs
description: Monitorize as aplicações web para disponibilidade, desempenho e utilização.
ms.topic: conceptual
ms.date: 05/21/2020
ms.custom: devx-track-csharp
ms.reviewer: lmolkova
ms.openlocfilehash: 98f1ea8a4994515b437841174ae95563ea53ed88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88933346"
---
# <a name="application-insights-for-net-console-applications"></a>Insights de aplicação para aplicações de consola .NET

[O Application Insights](./app-insights-overview.md) permite-lhe monitorizar a sua aplicação web para disponibilidade, desempenho e utilização.

Precisa de uma subscrição com [o Microsoft Azure](https://azure.com). Faça sômis com uma conta Microsoft, que poderá ter para Windows, Xbox Live ou outros serviços na nuvem da Microsoft. A sua equipa poderá ter uma subscrição organizacional do Azure: peça ao proprietário que lhe adicione usando a sua conta Microsoft.

> [!NOTE]
> É *altamente recomendado* utilizar o pacote [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) e instruções associadas a partir [daqui](./worker-service.md) para quaisquer aplicações de consola. Este pacote destina-se [`NetStandard2.0`](/dotnet/standard/net-standard) a ser utilizado em .NET Core 2.1 ou superior, e .NET Framework 4.7.2 ou superior.

## <a name="getting-started"></a>Introdução

* No [portal do Azure](https://portal.azure.com), [crie um recurso do Application Insights](./create-new-resource.md). Para o tipo de aplicação, escolha **General.**
* Faça uma cópia da Chave de Instrumentação. Encontre a chave no **essencial** para deixar cair o novo recurso que criou.
* Instale o mais recente pacote [Microsoft.ApplicationInsights.](https://www.nuget.org/packages/Microsoft.ApplicationInsights)
* Desaprote a tecla de instrumentação no seu código antes de rastrear qualquer telemetria (ou definir APPINSIGHTS_INSTRUMENTATIONKEY variável ambiente). Depois disso, você deve ser capaz de rastrear manualmente a telemetria e vê-la no portal Azure

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> A telemetria não é enviada instantaneamente. Os artigos de telemetria são embalados e enviados pela ApplicationInsights SDK. Nas aplicações Consola, que sai logo após os métodos de `Track()` chamada, a telemetria não pode ser enviada a menos que `Flush()` seja feita antes da saída da `Sleep` / `Delay` aplicação, como mostrado em todo o [exemplo](#full-example) mais tarde neste artigo. `Sleep` não é necessário se estiver a utilizar `InMemoryChannel` . Existe uma questão ativa quanto à necessidade para a `Sleep` qual é rastreada aqui: [ApplicationInsights-dotnet/issues/407](https://github.com/microsoft/ApplicationInsights-dotnet/issues/407)


* Instale a versão mais recente do pacote [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) - rastreia automaticamente HTTP, SQL ou algumas outras chamadas de dependência externa.

Pode inicializar e configurar insights de aplicação a partir do código ou usando o `ApplicationInsights.config` ficheiro. Certifique-se de que a inicialização acontece o mais cedo possível. 

> [!NOTE]
> As instruções referentes a **ApplicationInsights.config** são aplicáveis apenas a aplicações que visam o Quadro .NET e não se aplicam a aplicações .NET Core.

### <a name="using-config-file"></a>Usando o ficheiro config

Por padrão, a Application Insights SDK procura `ApplicationInsights.config` ficheiro no diretório de trabalho quando `TelemetryConfiguration` está a ser criado

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Também pode especificar o caminho para o ficheiro config.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Para obter mais informações, consulte [a referência de ficheiro de configuração](configuration-with-applicationinsights-config.md).

Pode obter um exemplo completo do ficheiro config instalando a versão mais recente do pacote [Microsoft.ApplicationInsights.WindowsServer.](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) Aqui está a configuração **mínima** para a recolha de dependência que é equivalente ao exemplo de código.

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

### <a name="configuring-telemetry-collection-from-code"></a>Configuração da coleção de telemetria a partir do código
> [!NOTE]
> Ler ficheiro config não é suportado em .NET Core. Pode considerar a utilização de [Insights de Aplicação SDK para ASP.NET Core](./asp-net-core.md)

* Durante o arranque da aplicação criar e configurar `DependencyTrackingTelemetryModule` a instância - deve ser singleton e deve ser preservado para o período de vida útil da aplicação.

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

* Adicione inicializadores de telemetria comuns

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Se criou a configuração com `TelemetryConfiguration()` um construtor simples, tem de ativar o suporte de correlação adicionalmente. **Não é necessário** se ler a configuração a partir de ficheiro, usado `TelemetryConfiguration.CreateDefault()` ou `TelemetryConfiguration.Active` .

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Também pode querer instalar e inicializar o módulo de coletor de contador de desempenho, conforme descrito [aqui](https://apmtips.com/posts/2017-02-13-enable-application-insights-live-metrics-from-code/)


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

            // flush is not blocking when not using InMemoryChannel so wait a bit. There is an active issue regarding the need for `Sleep`/`Delay`
            // which is tracked here: https://github.com/microsoft/ApplicationInsights-dotnet/issues/407
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
* [Monitorize as dependências](./asp-net-dependencies.md) para ver se REST, SQL ou outros recursos externos estão a atrasá-lo.
* [Utilize a API](./api-custom-events-metrics.md) para enviar os seus próprios eventos e métricas para uma visão mais detalhada do desempenho e utilização da sua aplicação.

