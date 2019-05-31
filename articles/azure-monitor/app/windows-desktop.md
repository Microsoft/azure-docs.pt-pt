---
title: Monitorização de utilização e desempenho de aplicações de ambiente de trabalho do Windows
description: Analise a utilização e o desempenho da sua aplicação de ambiente de trabalho do Windows com o Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: mbullwin
ms.openlocfilehash: 567163a5d5ce37eeffb5ef2bc6f9adb7c5b027ec
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255729"
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
   
    `<InstrumentationKey>`*a sua chave*`</InstrumentationKey>` 
   
    Se utilizar o ApplicationInsights.config, certifique-se de que as propriedades no Explorador de Soluções estão definidas para **Ação de Compilação = Conteúdo, Copiar para o Diretório de Saída = Copiar**.
5. [Utilize a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar telemetria.
6. Executar a sua aplicação e ver a telemetria no recurso que criou no portal do Azure.

## <a name="telemetry"></a>Código de exemplo
```csharp

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Passos Seguintes
* [Criar um dashboard](../../azure-monitor/app/overview-dashboard.md)
* [Pesquisa de Diagnóstico](../../azure-monitor/app/diagnostic-search.md)
* [Explorar métricas](../../azure-monitor/app/metrics-explorer.md)
* [Escrever consultas da Análise](../../azure-monitor/app/analytics.md)

