---
title: Monitorização de utilização e desempenho de aplicações de ambiente de trabalho do Windows
description: Analise a utilização e o desempenho da sua aplicação de ambiente de trabalho do Windows com o Application Insights.
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 1aa1e8a9e7ccbbc90a961ebf47224f59f8a9e9fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91827872"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Monitorização de utilização e desempenho de aplicações de Ambiente de Trabalho do Windows Clássico

As aplicações alojadas no local, no Azure e noutras clouds podem tirar todo o partido do Application Insights. A única limitação é a necessidade de [permitir a comunicação](./ip-addresses.md) ao serviço Application Insights. Para monitorizar aplicações da Plataforma Universal do Windows (UWP), recomendamos o [Visual Studio App Center](../learn/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Para enviar a telemetria para o Application Insights a partir de uma aplicação do Windows Clássico
1. No [portal do Azure](https://portal.azure.com), [crie um recurso do Application Insights](./create-new-resource.md). 
2. Faça uma cópia da Chave de Instrumentação.
3. No Visual Studio, edite os pacotes NuGet do seu projeto da aplicação e adicione Microsoft.ApplicationInsights.WindowsServer. (Ou escolha Microsoft.ApplicationInsights se apenas quiser a API base, sem os módulos de recolha padrão de telemetria.)
4. Defina a chave de instrumentação no seu código:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*a sua chave*`";`
   
    ou no Applicationinsights (se tiver instalado um dos pacotes de telemetria standard):
   
    `<InstrumentationKey>`*a sua chave*`</InstrumentationKey>` 
   
    Se utilizar o ApplicationInsights.config, certifique-se de que as propriedades no Explorador de Soluções estão definidas para **Ação de Compilação = Conteúdo, Copiar para o Diretório de Saída = Copiar**.
5. [Utilize a API](./api-custom-events-metrics.md) para enviar telemetria.
6. Execute a sua aplicação e veja a telemetria no recurso que criou no portal Azure.

## <a name="example-code"></a><a name="telemetry"></a>Código de exemplo

```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnFormClosing(System.Windows.Forms.FormClosingEventArgs e)
        {
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnFormClosing(e);
        }
        
        ...
```

## <a name="override-storage-of-computer-name"></a>Sobrepor o armazenamento do nome do computador

Por predefinição, este SDK recolherá e armazenará o nome do computador do sistema que emite telemetria.

O nome do computador é usado pelo nível de preços da Application Insights [Legacy Enterprise (Per Node)](./pricing.md#legacy-enterprise-per-node-pricing-tier) para efeitos de faturação interna. Por predefinição, se utilizar um inicializador de telemetria para `telemetry.Context.Cloud.RoleInstance` substituir, será enviada uma propriedade separada `ai.internal.nodeName` que ainda conterá o valor do nome do computador. Este valor não será armazenado com a telemetria Application Insights, mas é utilizado internamente para permitir uma retrocompatibilidade com o modelo de faturação baseado em nó legado.

Se estiver no [nível de preços da Legacy Enterprise (Per Node)](./pricing.md#legacy-enterprise-per-node-pricing-tier) e simplesmente precisar de substituir o armazenamento do nome do computador, utilize um inicializador de telemetria:

**Escreva telemetriaInitializador personalizado como abaixo.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // Set custom role name here. Providing an empty string will result
                // in the computer name still be sent via this property.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

Instantiizar o inicializador no `Program.cs` `Main()` método abaixo definindo a chave de instrumentação:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
            //...
        }
```

## <a name="override-transmission-of-computer-name"></a>Sobrepor a transmissão do nome do computador

Se não estiver no [nível de preços da Legacy Enterprise (Per Node)](./pricing.md#legacy-enterprise-per-node-pricing-tier) e desejar impedir completamente o envio de qualquer telemetria que contenha o nome do computador, tem de utilizar um processador de telemetria.

### <a name="telemetry-processor"></a>Processador de telemetria

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;


namespace WindowsFormsApp2
{
    public class CustomTelemetryProcessor : ITelemetryProcessor
    {
        private readonly ITelemetryProcessor _next;

        public CustomTelemetryProcessor(ITelemetryProcessor next)
        {
            _next = next;
        }

        public void Process(ITelemetry item)
        {
            if (item != null)
            {
                item.Context.Cloud.RoleInstance = string.Empty;
            }

            _next.Process(item);
        }
    }
}
```

Instantiizar o processador de telemetria no `Program.cs` `Main()` método abaixo definindo a chave de instrumentação:

```csharp
using Microsoft.ApplicationInsights.Extensibility;

namespace WindowsFormsApp2
{
    static class Program
    {
        static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.Use((next) => new CustomTelemetryProcessor(next));
            builder.Build();
            //...
        }
    }
}

```

> [!NOTE]
> Embora possa utilizar tecnicamente um processador de telemetria como descrito acima, mesmo que esteja no [nível de preços da Legacy Enterprise (Per Node),](./pricing.md#legacy-enterprise-per-node-pricing-tier)isso resultará no potencial de faturação excessiva devido à incapacidade de distinguir adequadamente os nós para o preço por nó.

## <a name="next-steps"></a>Passos seguintes
* [Criar um dashboard](./overview-dashboard.md)
* [Pesquisa de Diagnóstico](./diagnostic-search.md)
* [Explorar métricas](../platform/metrics-charts.md)
* [Escrever consultas da Análise](../log-query/log-query-overview.md)

