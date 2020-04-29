---
title: Pacotes NuGet de Insights de Aplicação Do Monitor Azure Monitor
description: Listas de pacotes NuGet de insights de aplicação do Monitor Azure para ASP.NET, ASP.NET Core, Python
ms.topic: reference
ms.date: 10/16/2018
ms.openlocfilehash: 5675c0fad61f1b75aa7e93db02246eed43967ec2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670003"
---
# <a name="application-insights-nuget-packages"></a>Pacotes NuGet insights de aplicação

Abaixo está a lista atual de pacotes NuGet de lançamento estável para insights de aplicação.

## <a name="common-packages-for-aspnet"></a>Pacotes comuns para ASP.NET

| Nome do Pacote | Versão Estável | Descrição | Transferência |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.12.0 | Fornece funcionalidade principal para transmissão de todos os tipos de telemetria de Insights de Aplicação e é um pacote dependente para todos os outros pacotes de Insights de Aplicação | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Permite a interceção de chamadas de métodos | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Application Insights Dependency Collector para aplicações .NET. Este é um pacote dependente para pacotes específicos da plataforma Application Insights e fornece uma recolha automática de telemetria de dependência. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Aplicação Insights Performance Counters O Colecionador permite-lhe enviar dados recolhidos por Contadores de Desempenho para Insights de Aplicação. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.applicationInsights.Web | 2.12.0 | Insights de aplicação para aplicações web .NET | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | O pacote NuGet do Windows Server Do Windows Fornece uma recolha automática de insights de aplicação para aplicações .NET. Este pacote pode ser usado como um pacote dependente para pacotes específicos da plataforma Application Insights ou como um pacote autónomo para aplicações .NET que não são cobertos por pacotes específicos da plataforma (como para funções de trabalhador .NET). | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemettryChannel | 2.12.0 | Fornece um canal de telemetria para Application Insights Windows Server SDK que preservará a telemetria em cenários offline. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Pacotes comuns para ASP.NET Core

| Nome do Pacote | Versão Estável | Descrição | Transferência |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Informações de aplicação para ASP.NET aplicações web Core. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.12.0 | Este pacote fornece uma funcionalidade central para a transmissão de todos os tipos de telemetria de Insights de Aplicação e é um pacote dependente para todos os outros pacotes de Insights de Aplicação | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Application Insights Dependency Collector para aplicações .NET. Este é um pacote dependente para pacotes específicos da plataforma Application Insights e fornece uma recolha automática de telemetria de dependência. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Aplicação Insights Performance Counters O Colecionador permite-lhe enviar dados recolhidos por Contadores de Desempenho para Insights de Aplicação. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | O pacote NuGet do Windows Server Do Windows Fornece uma recolha automática de insights de aplicação para aplicações .NET. Este pacote pode ser usado como um pacote dependente para pacotes específicos da plataforma Application Insights ou como um pacote autónomo para aplicações .NET que não são cobertos por pacotes específicos da plataforma (como para funções de trabalhador .NET). | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemettryChannel | 2.12.0 | Fornece um canal de telemetria para Application Insights Windows Server SDK que preservará a telemetria em cenários offline. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>Pacotes comuns para Python usando OpenCensus
| Nome do Pacote | Versão Estável | Descrição | Transferência |
|-------------------------------|-----------------------|------------|----|
| opencensus-ext-azure | 1.0.0 | Informações de aplicação para aplicações Python no Monitor Azure via OpenCensus. | [Pacote de descarregamento](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus-ext-django | 0.7.2 | Este pacote proporciona integração com a biblioteca Python [django.](https://pypi.org/project/django/) | [Pacote de descarregamento](https://pypi.org/project/opencensus-ext-django/) |
| abrir census-ext-flask | 0.7.3 | Este pacote proporciona integração com a biblioteca [de frascos](https://pypi.org/project/flask/) Python. | [Pacote de descarregamento](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus-ext-httplib | 0.7.2 | Este pacote proporciona integração com a biblioteca Python [http.client](https://docs.python.org/3/library/http.client.html) library para Python3 e [httplib](https://docs.python.org/2/library/httplib.html) para Python2. | [Pacote de descarregamento](https://pypi.org/project/opencensus-ext-httplib/) |
| abrir recenseamento-ext-logging | 0.1.0 | Este pacote enriquece registos de registos com dados de vestígios. | [Pacote de descarregamento](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus-ext-mysql | 0.1.2 | Este pacote proporciona integração com a biblioteca de [conector misql Python.](https://pypi.org/project/mysql-connector/) | [Pacote de descarregamento](https://pypi.org/project/opencensus-ext-mysql/) |
| abrir census-ext-postgresql | 0.1.2 | Este pacote proporciona integração com a biblioteca [Python psycopg2.](https://pypi.org/project/psycopg2/) | [Pacote de descarregamento](https://pypi.org/project/opencensus-ext-postgresql/) |
| abrir census-ext-pymongo | 0.7.1 | Este pacote proporciona integração com a biblioteca [python pymongo.](https://pypi.org/project/pymongo/) | [Pacote de descarregamento](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus-ext-pymysql | 0.1.2 | Este pacote proporciona integração com a biblioteca Python [PyMySQL.](https://pypi.org/project/PyMySQL/) | [Pacote de descarregamento](https://pypi.org/project/opencensus-ext-pymysql/) |
| abrir census-ext-pirâmide | 0.7.1 | Este pacote proporciona integração com a biblioteca [da pirâmide](https://pypi.org/project/pyramid/) python. | [Pacote de descarregamento](https://pypi.org/project/opencensus-ext-pyramid/) |
| abrir pedidos de ext-ext | 0.7.2 | Este pacote proporciona integração com a biblioteca [de pedidos](https://pypi.org/project/requests/) python. | [Pacote de descarregamento](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus-ext-sqlalchemy | 0.1.2 | Este pacote proporciona integração com a biblioteca [Python SQLAlchemy.](https://pypi.org/project/SQLAlchemy/) | [Pacote de descarregamento](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Ouvintes/colecionadores/apendicantes

| Nome do Pacote | Versão Estável | Descrição | Transferência |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Permite retransmitir eventos do DiagnosticSource para os Insights de Aplicação. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Os Insights de Aplicação EventSourceListener permitem o envio de dados de eventos eventSource para Insights de Aplicação. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Os Insights de Aplicação EtwCollector permitem o envio de dados do Event Tracing para Windows (ETW) para Insights de Aplicação. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | Um TraceListener personalizado que lhe permite enviar mensagens de registo de rastreio para Insights de Aplicação. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Um adtido personalizado que lhe permite enviar mensagens de log Log4Net para Insights de Aplicação. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  um alvo personalizado que lhe permite enviar mensagens de log NLog para Insights de Aplicação. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Monitoriza exceções na sua aplicação e recolhe automaticamente instantâneos para análise offline. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Nome do Pacote | Versão Estável | Descrição | Transferência |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Este pacote proporciona decoração automática de telemetria com o contexto de tecido de serviço em que a aplicação está a decorrer. Não utilize este NuGet para aplicações de Tecido de Serviço Nativo. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Módulo De Insights de Aplicação para aplicações de tecido de serviço. Utilize este NuGet apenas para aplicações de Tecido de Serviço Nativo. Para aplicações em execução em contentores, utilize o pacote Microsoft.ApplicationInsights.ServiceFabric. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Monitor de estado

| Nome do Pacote | Versão Estável | Descrição | Transferência |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Permite a recolha de dados em tempo de execução para aplicações x64 | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Permite a recolha de dados em tempo de execução para aplicações x86. | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Estes pacotes compõem parte da funcionalidade central da monitorização do tempo de funcionamento no Monitor de [Estado](../../azure-monitor/app/monitor-performance-live-website-now.md). Não precisa de descarregar estes pacotes diretamente, basta utilizar o instalador do Monitor de Estado. Se quiser entender mais sobre como estes pacotes funcionam sob o capot este [blog post](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) de um dos nossos desenvolvedores é um bom começo.

## <a name="additional-packages"></a>Pacotes adicionais

| Nome do Pacote | Versão Estável | Descrição | Transferência |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | Esta extensão permite a monitorização de Insights de Aplicação num Serviço de Aplicações Azure. Versão SDK 2.6.1. Instruções: Adicione as definições de aplicação 'APPINSIGHTS_INSTRUMENTATIONKEY' com o seu ikey e reinicie a webapp para efetuar um efeito.| [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injetor | 2.6.7 | Este pacote contém ficheiros necessários para a injeção de Insights de Aplicação sem código | [Pacote de descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Passos seguintes

- MonitorASP.NET [Core](../../azure-monitor/app/asp-net-core.md).
- Profile ASP.NET web apps Core [Azure Linux.](../../azure-monitor/app/profiler-aspnetcore-linux.md)
- Depurar ASP.NET [instantâneos.](../../azure-monitor/app/snapshot-debugger.md)
