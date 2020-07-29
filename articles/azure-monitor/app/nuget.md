---
title: Pacotes Azure Monitor Application Insights NuGet
description: Listas de pacotes nugets de aplicativos Azure Monitor insights para ASP.NET, ASP.NET Core, Python
ms.topic: reference
ms.date: 10/16/2018
ms.openlocfilehash: 27a3d89b4a64de159535d346641c21616833e21b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87309958"
---
# <a name="application-insights-nuget-packages"></a>Pacotes NuGet de Insights de Aplicações

Abaixo está a lista atual de pacotes nuget de lançamento estável para insights de aplicações.

## <a name="common-packages-for-aspnet"></a>Pacotes comuns para ASP.NET

| Nome do Pacote | Versão estável | Descrição | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.12.0 | Fornece funcionalidade central para transmissão de todos os tipos de telemetria de Insights de Aplicação e é um pacote dependente para todos os outros pacotes de Insights de Aplicações | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Permite a interceção de chamadas de métodos | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Application Insights Dependency Collector para aplicações .NET. Este é um pacote dependente para pacotes específicos da plataforma Application Insights e fornece a recolha automática de telemetria de dependência. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | O Contador de Desempenho de Aplicações Insights Permite-lhe enviar dados recolhidos por Contadores de Desempenho para Insights de Aplicação. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.12.0 | Insights de Aplicação para aplicações web .NET | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | O pacote NuGet do Servidor do Windows Insights fornece uma recolha automática de telemetria de insights de aplicações para aplicações .NET. Este pacote pode ser usado como um pacote dependente para pacotes específicos da plataforma Application Insights ou como um pacote autónomo para aplicações .NET que não são cobertos por pacotes específicos da plataforma (como para funções de trabalhador .NET). | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.12.0 | Fornece um canal de telemetria para o Application Insights Windows Server SDK que irá preservar a telemetria em cenários offline. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Pacotes comuns para ASP.NET Core

| Nome do Pacote | Versão estável | Descrição | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Insights de aplicação para aplicações web core ASP.NET. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.12.0 | Este pacote fornece funcionalidade central para a transmissão de todos os tipos de telemetria de Insights de Aplicação e é um pacote dependente para todos os outros pacotes de Insights de Aplicação | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Application Insights Dependency Collector para aplicações .NET. Este é um pacote dependente para pacotes específicos da plataforma Application Insights e fornece a recolha automática de telemetria de dependência. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | O Contador de Desempenho de Aplicações Insights Permite-lhe enviar dados recolhidos por Contadores de Desempenho para Insights de Aplicação. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | O pacote NuGet do Servidor do Windows Insights fornece uma recolha automática de telemetria de insights de aplicações para aplicações .NET. Este pacote pode ser usado como um pacote dependente para pacotes específicos da plataforma Application Insights ou como um pacote autónomo para aplicações .NET que não são cobertos por pacotes específicos da plataforma (como para funções de trabalhador .NET). | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.12.0 | Fornece um canal de telemetria para o Application Insights Windows Server SDK que irá preservar a telemetria em cenários offline. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>Pacotes comuns para Python usando OpenCensus
| Nome do Pacote | Versão estável | Descrição | Download |
|-------------------------------|-----------------------|------------|----|
| opencensus-ext-azure | 1.0.0 | Insights de aplicação para aplicações Python no âmbito do Azure Monitor via OpenCensus. | [Pacote de Descarregamento](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus-ext-django | 0.7.2 | Este pacote proporciona integração com a biblioteca [Python django.](https://pypi.org/project/django/) | [Pacote de Descarregamento](https://pypi.org/project/opencensus-ext-django/) |
| opencensus-ext-balão | 0.7.3 | Este pacote proporciona integração com a biblioteca [de frascos](https://pypi.org/project/flask/) Python. | [Pacote de Descarregamento](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus-ext-httplib | 0.7.2 | Este pacote proporciona integração com a biblioteca Python [http.client](https://docs.python.org/3/library/http.client.html) para Python3 e [httplib](https://docs.python.org/2/library/httplib.html) para Python2. | [Pacote de Descarregamento](https://pypi.org/project/opencensus-ext-httplib/) |
| opencensus-ext-logging | 0.1.0 | Este pacote enriquece registos de registos com dados de vestígios. | [Pacote de Descarregamento](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus-ext-mysql | 0.1.2 | Este pacote proporciona integração com a biblioteca [de conector de mísq-python.](https://pypi.org/project/mysql-connector/) | [Pacote de Descarregamento](https://pypi.org/project/opencensus-ext-mysql/) |
| opencensus-ext-postgresql | 0.1.2 | Este pacote proporciona integração com a biblioteca python [psycopg2.](https://pypi.org/project/psycopg2/) | [Pacote de Descarregamento](https://pypi.org/project/opencensus-ext-postgresql/) |
| opencensus-ext-pymongo | 0.7.1 | Este pacote proporciona integração com a biblioteca Python [pymongo.](https://pypi.org/project/pymongo/) | [Pacote de Descarregamento](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus-ext-pymysql | 0.1.2 | Este pacote proporciona integração com a biblioteca Python [PyMySQL.](https://pypi.org/project/PyMySQL/) | [Pacote de Descarregamento](https://pypi.org/project/opencensus-ext-pymysql/) |
| opencensus-ext-pirâmide | 0.7.1 | Este pacote proporciona integração com a biblioteca da [pirâmide](https://pypi.org/project/pyramid/) Python. | [Pacote de Descarregamento](https://pypi.org/project/opencensus-ext-pyramid/) |
| opencensus-ext-pedidos | 0.7.2 | Este pacote proporciona integração com a biblioteca python [solicita.](https://pypi.org/project/requests/) | [Pacote de Descarregamento](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus-ext-sqlalchemy | 0.1.2 | Este pacote proporciona integração com a biblioteca Python [SQLAlchemy.](https://pypi.org/project/SQLAlchemy/) | [Pacote de Descarregamento](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Ouvintes/colecionadores/appenders

| Nome do Pacote | Versão estável | Descrição | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Permite o encaminhamento de eventos do DiagnosticSource para o Application Insights. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | O EventSourceListener da Application Insights permite o envio de dados de eventos EventSource para Insights de Aplicação. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Insights de Aplicação EtwCollector permite o envio de dados de Event Tracing for Windows (ETW) para Application Insights. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | Um TraceListener personalizado que permite enviar mensagens de registo de rastreio para Insights de Aplicação. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Um appender personalizado que lhe permite enviar mensagens de registo Log4Net para Insights de Aplicação. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  um alvo personalizado que lhe permite enviar mensagens de registo NLog para Insights de Aplicação. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Monitoriza as exceções na sua aplicação e recolhe automaticamente instantâneos para análise offline. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Nome do Pacote | Versão estável | Descrição | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Este pacote proporciona decoração automática de telemetria com o contexto de tecido de serviço em que a aplicação está em execução. Não utilize este NuGet para aplicações de Tecido de Serviço Nativo. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Módulo De Insights de Aplicação para aplicações de tecido de serviço. Utilize este NuGet apenas para aplicações de Tecido de Serviço Nativo. Para aplicações em execução em contentores, utilize o pacote Microsoft.ApplicationInsights.ServiceFabric. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Monitor de estado

| Nome do Pacote | Versão estável | Descrição | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Permite a recolha de dados em tempo de execução para aplicações x64 | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Permite a recolha de dados em tempo de execução para aplicações x86. | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Estes pacotes constituem parte da funcionalidade principal da monitorização do tempo de funcionamento no [Status Monitor](./monitor-performance-live-website-now.md). Não precisa de descarregar diretamente estes pacotes, basta utilizar o instalador status Monitor. Se quiser entender mais sobre como estes pacotes funcionam sob o capot este post de [blog](https://apmtips.com/posts/2016-11-18-how-application-insights-status-monitor-not-monitors-dependencies/) de um dos nossos desenvolvedores é um bom começo.

## <a name="additional-packages"></a>Pacotes adicionais

| Nome do Pacote | Versão estável | Descrição | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | Esta extensão permite a monitorização do Application Insights num Serviço de Aplicações Azure. Versão SDK 2.6.1. Instruções: Adicione as definições de aplicação 'APPINSIGHTS_INSTRUMENTATIONKEY' com o seu ikey e reinicie o webapp para fazer um efeito.| [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injetor | 2.6.7 | Este pacote contém ficheiros necessários para a injeção de Insights de Aplicação sem código | [Pacote de Descarregamento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Passos seguintes

- Monitor [ASP.NET Core](./asp-net-core.md).
- Perfil ASP.NET aplicações web Core [Azure Linux](./profiler-aspnetcore-linux.md).
- Debug ASP.NET [fotos.](./snapshot-debugger.md)

