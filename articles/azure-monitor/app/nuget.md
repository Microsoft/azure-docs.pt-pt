---
title: Aplicativo Azure insights – coleção de dependências automática | Microsoft Docs
description: Application Insights coletar e Visualizar automaticamente as dependências
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/16/2018
ms.openlocfilehash: 1d3259c32de7f937d4e5ac8e21c8cab60b9cc635
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819168"
---
# <a name="application-insights-nuget-packages"></a>Application Insights pacotes NuGet

Abaixo está a lista atual de pacotes NuGet de liberação estável para Application Insights.

## <a name="common-packages-for-aspnet"></a>Pacotes comuns para ASP.NET

| Nome do pacote | Versão estável | Descrição | Transferir |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights | 2.8.0 | Fornece a funcionalidade principal para a transmissão de todos os tipos de Application Insights Telemetry e é um pacote dependente para todos os outros pacotes de Application Insights | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft. ApplicationInsights. Agent. Intercept | 2.4.0 | Habilita a interceptação de chamadas de método | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft. ApplicationInsights. DependencyCollector | 2.8.0 | Application Insights coletor de dependência para aplicativos .NET. Este é um pacote dependente para Application Insights pacotes específicos da plataforma e fornece a coleta automática de telemetria de dependência. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft. ApplicationInsights. PerfCounterCollector | 2.8.0 | Application Insights coletor de contadores de desempenho permite que você envie dados coletados pelos contadores de desempenho para Application Insights. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft. ApplicationInsights. Web | 2.8.0 | Application Insights para aplicativos Web do .NET | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft. ApplicationInsights. WindowsServer | 2.8.0 | Application Insights pacote NuGet do Windows Server fornece a coleção automática de telemetria do Application insights para aplicativos .NET. Esse pacote pode ser usado como um pacote dependente para Application Insights pacotes específicos da plataforma ou como um pacote autônomo para aplicativos .NET que não são cobertos por pacotes específicos da plataforma (como as funções de trabalho do .NET). | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel | 2.8.0 | Fornece um canal de telemetria para Application Insights SDK do Windows Server que preservará a telemetria em cenários offline. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Pacotes comuns para ASP.NET Core

| Nome do pacote | Versão estável | Descrição | Transferir |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. AspNetCore | 2.5.0 | Application Insights para ASP.NET Core aplicativos Web. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft. ApplicationInsights | 2.8.0 | Esse pacote fornece a funcionalidade básica para a transmissão de todos os tipos de Application Insights Telemetry e é um pacote dependente para todos os outros pacotes de Application Insights | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft. ApplicationInsights. DependencyCollector | 2.8.0 | Application Insights coletor de dependência para aplicativos .NET. Este é um pacote dependente para Application Insights pacotes específicos da plataforma e fornece a coleta automática de telemetria de dependência. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft. ApplicationInsights. PerfCounterCollector | 2.8.0 | Application Insights coletor de contadores de desempenho permite que você envie dados coletados pelos contadores de desempenho para Application Insights. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft. ApplicationInsights. WindowsServer | 2.8.0 | Application Insights pacote NuGet do Windows Server fornece a coleção automática de telemetria do Application insights para aplicativos .NET. Esse pacote pode ser usado como um pacote dependente para Application Insights pacotes específicos da plataforma ou como um pacote autônomo para aplicativos .NET que não são cobertos por pacotes específicos da plataforma (como as funções de trabalho do .NET). | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel | 2.8.0 | Fornece um canal de telemetria para Application Insights SDK do Windows Server que preservará a telemetria em cenários offline. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>Pacotes comuns para Python usando OpenCensus
| Nome do pacote | Versão estável | Descrição | Transferir |
|-------------------------------|-----------------------|------------|----|
| opencensus-ext-Azure | 1.0.0 | Application Insights para aplicativos Python em Azure Monitor via OpenCensus. | [Baixar pacote](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus-ext-Django | 0.7.2 | Este pacote fornece integração com a Biblioteca Python [Django](https://pypi.org/project/django/) . | [Baixar pacote](https://pypi.org/project/opencensus-ext-django/) |
| opencensus-ext-Flask | 0.7.3 | Este pacote fornece integração com a Biblioteca Python [Flask](https://pypi.org/project/flask/) . | [Baixar pacote](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus-ext-httplib | 0.7.2 | Este pacote fornece integração com a biblioteca de [http. cliente](https://docs.python.org/3/library/http.client.html) do Python para Python3 e [httplib](https://docs.python.org/2/library/httplib.html) para Python2. | [Baixar pacote](https://pypi.org/project/opencensus-ext-httplib/) |
| registro em log opencensus-ext | 0.1.0 | Este pacote enriquece os registros de log com os dados de rastreamento. | [Baixar pacote](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus-ext-MySQL | 0.1.2 | Este pacote fornece integração com a biblioteca do [conector MySQL](https://pypi.org/project/mysql-connector/) do Python. | [Baixar pacote](https://pypi.org/project/opencensus-ext-mysql/) |
| opencensus-ext-PostgreSQL | 0.1.2 | Este pacote fornece integração com a Biblioteca Python [psycopg2](https://pypi.org/project/psycopg2/) . | [Baixar pacote](https://pypi.org/project/opencensus-ext-postgresql/) |
| opencensus-ext-pymongo | 0.7.1 | Este pacote fornece integração com a Biblioteca Python [pymongo](https://pypi.org/project/pymongo/) . | [Baixar pacote](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus-ext-pymysql | 0.1.2 | Este pacote fornece integração com a Biblioteca Python [PyMySQL](https://pypi.org/project/PyMySQL/) . | [Baixar pacote](https://pypi.org/project/opencensus-ext-pymysql/) |
| opencensus-ext-pirâmide | 0.7.1 | Este pacote fornece integração com a biblioteca de [pirâmides](https://pypi.org/project/pyramid/) do Python. | [Baixar pacote](https://pypi.org/project/opencensus-ext-pyramid/) |
| opencensus-ext-solicitações | 0.7.2 | Este pacote fornece integração com a biblioteca de [solicitações](https://pypi.org/project/requests/) do Python. | [Baixar pacote](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus-ext-SQLAlchemy | 0.1.2 | Este pacote fornece integração com a Biblioteca Python [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) . | [Baixar pacote](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Ouvintes/coletores/appenders

| Nome do pacote | Versão estável | Descrição | Transferir |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. DiagnosticSourceListener | 2.7.2 |  Permite encaminhar eventos de Diagnosticname para Application Insights. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft. ApplicationInsights. EventSourceListener | 2.7.2 | Application Insights EventSourceListener permite enviar dados de eventos EventSource para Application Insights. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft. ApplicationInsights. EtwCollector | 2.7.2 | Application Insights EtwCollector permite enviar dados do ETW (rastreamento de eventos para Windows) para Application Insights. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft. ApplicationInsights. TraceListener | 2.7.2 | Um TraceListener personalizado permitindo que você envie mensagens de log de rastreamento para Application Insights. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft. ApplicationInsights. Log4NetAppender | 2.7.2 | Um anexador personalizado que permite enviar mensagens de log Log4Net para Application Insights. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft. ApplicationInsights. NLogTarget | 2.7.2 |  um destino personalizado que permite que você envie mensagens de log NLog para Application Insights. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft. ApplicationInsights. SnapshotCollector | 1.3.1 | Monitora exceções em seu aplicativo e coleta automaticamente instantâneos para análise offline. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Nome do pacote | Versão estável | Descrição | Transferir |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. infabric | 2.2.0 | Esse pacote fornece uma decoração automática de telemetria com o contexto do Service Fabric no qual o aplicativo está sendo executado. Não use este NuGet para aplicativos Service Fabric nativos. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft. ApplicationInsights. Fabric. Native | 2.2.0 | Módulo Application Insights para aplicativos do Service Fabric. Use este NuGet somente para aplicativos Service Fabric nativos. Para aplicativos executados em contêineres, use o pacote Microsoft. ApplicationInsights. perfabric. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Monitor de status

| Nome do pacote | Versão estável | Descrição | Transferir |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. Agent_x64 | 2.2.1 |  Habilita a coleta de dados de tempo de execução para aplicativos x64 | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft. ApplicationInsights. Agent_x86 | 2.2.1 |  Habilita a coleta de dados de tempo de execução para aplicativos x86. | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Esses pacotes fazem parte da funcionalidade principal do monitoramento de tempo de execução no [status monitor](../../azure-monitor/app/monitor-performance-live-website-now.md). Você não precisa baixar esses pacotes diretamente, basta usar o instalador do Status Monitor. Se você quiser entender mais sobre como esses pacotes funcionam nos bastidores, essa [postagem de blog](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) de um de nossos desenvolvedores é um bom começo.

## <a name="additional-packages"></a>Pacotes adicionais

| Nome do pacote | Versão estável | Descrição | Transferir |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. AzureWebSites | 2.6.5 | Essa extensão permite o monitoramento de Application Insights em um serviço Azure App. Versão do SDK 2.6.1. Instruções: Adicione as configurações do aplicativo ' APPINSIGHTS_INSTRUMENTATIONKEY ' com seu iKey e reinicie o webapp para ter um efeito.| [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft. ApplicationInsights. Injetador | 2.6.7 | Este pacote contém arquivos necessários para injeção de Application Insights de código | [Baixar pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Passos seguintes

- [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)de monitor.
- Perfil ASP.NET Core [aplicativos Web Linux do Azure](../../azure-monitor/app/profiler-aspnetcore-linux.md).
- Depurar [instantâneos](../../azure-monitor/app/snapshot-debugger.md)do ASP.net.