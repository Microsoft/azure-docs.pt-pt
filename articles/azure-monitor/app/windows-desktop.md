---
title: Monitorização de utilização e desempenho de aplicações de ambiente de trabalho do Windows
description: Analise a utilização e o desempenho da sua aplicação de ambiente de trabalho do Windows com o Application Insights.
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: eb9e0fc480098478a3a68265ac85e0d5450e27fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537394"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Monitorização de utilização e desempenho de aplicações de Ambiente de Trabalho do Windows Clássico

As aplicações alojadas no local, no Azure e noutras clouds podem tirar todo o partido do Application Insights. A única limitação é a necessidade de [permitir a comunicação](../../azure-monitor/app/ip-addresses.md) ao serviço Application Insights. Para monitorizar aplicações da Plataforma Universal do Windows (UWP), recomendamos o [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Para enviar a telemetria para o Application Insights a partir de uma aplicação do Windows Clássico
1. No [portal do Azure](https://portal.azure.com), [crie um recurso do Application Insights](../../azure-monitor/app/create-new-resource.md ). Para o tipo de aplicação, escolha a aplicação ASP.NET.
2. Faça uma cópia da Chave de Instrumentação. Encontre a chave na lista pendente Essentials do novo recurso que acabou de criar. 
3. No Visual Studio, edite os pacotes NuGet do seu projeto da aplicação e adicione Microsoft.ApplicationInsights.WindowsServer. (Ou escolha Microsoft.ApplicationInsights se apenas pretender a API bare, sem os módulos de coleção de telemetria standard.)
4. Defina a chave de instrumentação no seu código:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *a sua chave* `";`
   
    ou no Applicationinsights (se tiver instalado um dos pacotes de telemetria standard):
   
    `<InstrumentationKey>`*sua chave*`</InstrumentationKey>` 
   
    Se utilizar o ApplicationInsights.config, certifique-se de que as propriedades no Explorador de Soluções estão definidas para **Ação de Compilação = Conteúdo, Copiar para o Diretório de Saída = Copiar**.
5. [Utilize a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar telemetria.
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

        protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
        {
            e.Cancel = true;

            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="override-storage-of-computer-name"></a>Anular o armazenamento do nome do computador

Por predefinição, este SDK recolherá e armazenará o nome do computador do sistema que emite telemetria. Para anular a recolha, precisa de usar um Initializer de telemetria:

**Escreva TelemettryInitializer personalizado como abaixo.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here, you can pass an empty string if needed.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```
Instantie o inicializador no `Program.cs` `Main()` método abaixo, definindo a chave de instrumentação:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
        }
```

## <a name="next-steps"></a>Passos seguintes
* [Criar um dashboard](../../azure-monitor/app/overview-dashboard.md)
* [Pesquisa de Diagnóstico](../../azure-monitor/app/diagnostic-search.md)
* [Explorar métricas](../../azure-monitor/platform/metrics-charts.md)
* [Escrever consultas da Análise](../../azure-monitor/app/analytics.md)

