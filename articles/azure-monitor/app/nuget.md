---
title: Azure Application Insights - a coleção automática de dependência | Documentos da Microsoft
description: O Application Insights automaticamente a recolha e visualize dependências
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/16/2018
ms.author: mbullwin
ms.openlocfilehash: 3ad2f4788a765366066023724772f5432d0d56eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699210"
---
# <a name="application-insights-nuget-packages"></a>Pacotes de NuGet de informações da aplicação

Segue-se a lista atual de versão estável pacotes NuGet para o Application Insights.

## <a name="common-packages-for-aspnet"></a>Pacotes comuns para o ASP.NET

| Nome do pacote | Versão estável | Descrição | Transferência |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.8.0 | Fornece a funcionalidade principal para a transmissão de todos os tipos de telemetria de informações da aplicação e é um pacote dependente para todos os outros pacotes do Application Insights | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Permite que a interceptação de chamadas de método | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Recoletor de dependência de informações de aplicação para aplicações .NET. Este é um pacote dependente para pacotes específicos da plataforma do Application Insights e fornece uma coleção automática de telemetria de dependência. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Recoletor de contadores de desempenho do Application Insights permite-lhe enviar dados recolhidos pelo contadores de desempenho para o Application Insights. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.8.0 | Aplicações web do Application Insights para .NET | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Pacote NuGet de servidor do Windows de informações de aplicação fornece uma coleção automática de telemetria do application insights para aplicações .NET. Este pacote pode ser utilizado como um pacote dependente para pacotes específicos da plataforma do Application Insights ou como um pacote autônomo para aplicativos .NET que não são abrangidos por pacotes para plataformas específicas (por exemplo, para funções de trabalho do .NET). | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Fornece um canal de telemetria para o Windows Server SDK do Application Insights que preservará telemetria em cenários offline. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Pacotes comuns para o ASP.NET Core

| Nome do pacote | Versão estável | Descrição | Transferência |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Aplicações web do Application Insights para ASP.NET Core. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.8.0 | Este pacote fornece a funcionalidade principal para a transmissão de todos os tipos de telemetria de informações da aplicação e é um pacote dependente para todos os outros pacotes do Application Insights | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Recoletor de dependência de informações de aplicação para aplicações .NET. Este é um pacote dependente para pacotes específicos da plataforma do Application Insights e fornece uma coleção automática de telemetria de dependência. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Recoletor de contadores de desempenho do Application Insights permite-lhe enviar dados recolhidos pelo contadores de desempenho para o Application Insights. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Pacote NuGet de servidor do Windows de informações de aplicação fornece uma coleção automática de telemetria do application insights para aplicações .NET. Este pacote pode ser utilizado como um pacote dependente para pacotes específicos da plataforma do Application Insights ou como um pacote autônomo para aplicativos .NET que não são abrangidos por pacotes para plataformas específicas (por exemplo, para funções de trabalho do .NET). | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Fornece um canal de telemetria para o Windows Server SDK do Application Insights que preservará telemetria em cenários offline. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="listenerscollectorsappenders"></a>Serviços de escuta/recoletores/appenders

| Nome do pacote | Versão estável | Descrição | Transferência |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Permite que o reencaminhamento de eventos de DiagnosticSource para o Application Insights. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Permite-Application Insights EventSourceListener a enviar dados de eventos de EventSource para o Application Insights. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Permite-Application Insights EtwCollector a enviar dados de rastreio de eventos para Windows (ETW) para o Application Insights. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | Um TraceListener personalizado, permitindo-lhe enviar mensagens de registo de rastreio para o Application Insights. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Um appender personalizado, permitindo-lhe enviar mensagens de registo do Log4Net ao Application Insights. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  um destino personalizado, permitindo-lhe enviar mensagens de log NLog ao Application Insights. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Monitoriza as exceções na sua aplicação e recolhe automaticamente instantâneos para a análise offline. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Nome do pacote | Versão estável | Descrição | Transferência |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Este pacote fornece automática decoração de telemetria com o contexto de recursos de infraestrutura do serviço a aplicação está em execução numa. Não utilize este NuGet para aplicativos nativos Service Fabric. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Módulo de Application Insights para aplicações do service fabric. Utilize este NuGet apenas para aplicações nativas Service Fabric. Para aplicativos executados em contentores, utilize o pacote Microsoft.ApplicationInsights.ServiceFabric. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Monitor de estado

| Nome do pacote | Versão estável | Descrição | Transferência |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Permite a recolha de dados de tempo de execução para x64 aplicativos | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Permite a recolha de dados de tempo de execução para x86 aplicativos. | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Esses pacotes constituem a parte da funcionalidade principal da monitorização de tempo de execução no [Monitor de estado](../../azure-monitor/app/monitor-performance-live-website-now.md). Não precisa baixar esses pacotes diretamente, basta utilizar o instalador de Monitor de estado. Se quiser saber mais sobre como estes pacotes de trabalho nos bastidores isso [mensagem de blogue](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) de um dos nossos desenvolvedores é um bom começo.

## <a name="additional-packages"></a>Pacotes adicionais

| Nome do pacote | Versão estável | Descrição | Transferência |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | Esta extensão ativa a monitorização do Application Insights no serviço de aplicações do Azure. SDK versão 2.6.1. Instruções: Adicionar as definições da aplicação "APPINSIGHTS_INSTRUMENTATIONKEY" com a ikey e reinicie a aplicação Web para ter um efeito.| [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Este pacote contém os ficheiros necessários para a injeção do Application Insights sem código | [Transferir pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Passos Seguintes

- Monitor [ASP.NET Core](../../azure-monitor/app/asp-net-core.md).
- Perfil ASP.NET Core [aplicações web do Linux do Azure](../../azure-monitor/app/profiler-aspnetcore-linux.md).
- Depurar ASP.NET [instantâneos](../../azure-monitor/app/snapshot-debugger.md).