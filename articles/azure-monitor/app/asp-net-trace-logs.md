---
title: Explore os logs de rastreamento do .NET no Application Insights
description: Pesquisar logs gerados por Trace, NLog ou Log4Net.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/08/2019
ms.openlocfilehash: 33dc415e06b7f49f75697abb05248750444fea7c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432633"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Explore os logs de rastreamento do .NET/.NET Core e do Python no Application Insights

Envie logs de rastreamento de diagnóstico para seu aplicativo ASP.NET/ASP.NET Core de ILogger, NLog, log4Net ou System. Diagnostics. Trace para [aplicativo Azure insights][start]. Para aplicativos Python, envie logs de rastreamento de diagnóstico usando AzureLogHandler em OpenCensus Python para Azure Monitor. Em seguida, você pode explorá-los e pesquisá-los. Esses logs são mesclados com os outros arquivos de log do seu aplicativo, para que você possa identificar os rastreamentos associados a cada solicitação de usuário e correlacioná-los com outros eventos e relatórios de exceção.

> [!NOTE]
> Você precisa do módulo de captura de log? É um adaptador útil para agentes de terceiros. Mas se você ainda não estiver usando NLog, log4Net ou System. Diagnostics. Trace, considere apenas chamar [**Application insights TrackTrace ()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) diretamente.
>
>
## <a name="install-logging-on-your-app"></a>Instalar o log em seu aplicativo
Instale sua estrutura de registro em log escolhida em seu projeto, o que deve resultar em uma entrada em app. config ou Web. config.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Configurar Application Insights para coletar logs
[Adicione Application insights ao seu projeto](../../azure-monitor/app/asp-net.md) , caso ainda não tenha feito isso. Você verá uma opção para incluir o coletor de logs.

Ou clique com o botão direito do mouse em seu projeto no Gerenciador de Soluções para **configurar Application insights**. Selecione a opção **Configurar coleta de rastreamento** .

> [!NOTE]
> Não há Application Insights de menu ou opção de coletor de logs? Experimente a [solução de problemas](#troubleshooting).

## <a name="manual-installation"></a>Instalação manual
Use esse método se o tipo de projeto não for suportado pelo instalador do Application Insights (por exemplo, um projeto de área de trabalho do Windows).

1. Se você planeja usar log4Net ou NLog, instale-o em seu projeto.
2. Em Gerenciador de Soluções, clique com o botão direito do mouse em seu projeto e selecione **gerenciar pacotes NuGet**.
3. Pesquise "Application Insights".
4. Selecione um dos seguintes pacotes:

   - Para ILogger: [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Para NLog: [Microsoft. ApplicationInsights. NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Para Log4Net: [Microsoft. ApplicationInsights. Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Para System. Diagnostics: [Microsoft. ApplicationInsights. TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

O pacote NuGet instala os assemblies necessários e modifica o Web. config ou o app. config se isso for aplicável.

## <a name="ilogger"></a>ILogger

Para obter exemplos de como usar a implementação do Application Insights ILogger com aplicativos de console e ASP.NET Core, consulte [ApplicationInsightsLoggerProvider for .NET Core ILogger logs](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Inserir chamadas de log de diagnóstico
Se você usar System. Diagnostics. Trace, uma chamada típica seria:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se você preferir log4net ou NLog, use:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Usar eventos EventSource
Você pode configurar eventos [System. Diagnostics. Tracing. EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) para serem enviados a Application insights como rastreamentos. Primeiro, instale o `Microsoft.ApplicationInsights.EventSourceListener` pacote NuGet. Em seguida, edite a seção `TelemetryModules` do arquivo [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) .

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada fonte, você pode definir os seguintes parâmetros:
 * **Nome** especifica o nome do EventSource a ser coletado.
 * **Nível** especifica o nível de log a ser coletado: *crítico*, *erro*, *informativo*, *LogAlways*, *detalhado*ou *aviso*.
 * **Palavras** -chave (opcional) especifique o valor inteiro das combinações de palavras-chaves a serem usadas.

## <a name="use-diagnosticsource-events"></a>Usar eventos de diagnóstico
Você pode configurar eventos [System. Diagnostics. diagnosticname](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) para serem enviados a Application insights como rastreamentos. Primeiro, instale o [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) pacote NuGet. Em seguida, edite a seção "TelemetryModules" do arquivo [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) .

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Para cada diagnóstico que você deseja rastrear, adicione uma entrada com o atributo de **nome** definido como o nome de seu diagnóstico.

## <a name="use-etw-events"></a>Usar eventos ETW
Você pode configurar eventos ETW (rastreamento de eventos para Windows) a serem enviados para Application Insights como rastreamentos. Primeiro, instale o `Microsoft.ApplicationInsights.EtwCollector` pacote NuGet. Em seguida, edite a seção "TelemetryModules" do arquivo [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) .

> [!NOTE] 
> Os eventos ETW só podem ser coletados se o processo que hospeda o SDK for executado sob uma identidade que seja membro de administradores ou usuários de log de desempenho.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada fonte, você pode definir os seguintes parâmetros:
 * **ProviderName** é o nome do provedor de ETW a ser coletado.
 * **ProviderGuid** especifica o GUID do provedor de ETW a ser coletado. Ele pode ser usado em vez de `ProviderName`.
 * **Nível** define o nível de log a ser coletado. Ele pode ser *crítico*, *erro*, *informativo*, *LogAlways*, *detalhado*ou *aviso*.
 * **Palavras** -chave (opcional) defina o valor inteiro das combinações de palavras-chaves a serem usadas.

## <a name="use-the-trace-api-directly"></a>Usar a API de rastreamento diretamente
Você pode chamar a API de rastreamento do Application Insights diretamente. Os adaptadores de log usam essa API.

Por exemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Uma vantagem do TrackTrace é que você pode colocar dados relativamente longos na mensagem. Por exemplo, você pode codificar postar dados lá.

Você também pode adicionar um nível de severidade à sua mensagem. E, assim como outras telemetrias, você pode adicionar valores de propriedade para ajudar a filtrar ou pesquisar conjuntos diferentes de rastreamentos. Por exemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Isso permitirá que você filtre facilmente em [Pesquisar][diagnostic] todas as mensagens de um nível de severidade específico relacionado a um determinado banco de dados.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler para OpenCensus Python
O manipulador de log Azure Monitor permite exportar logs do Python para Azure Monitor.

Instrumente seu aplicativo com o [SDK do OpenCensus Python](../../azure-monitor/app/opencensus-python.md) para Azure monitor.

Este exemplo mostra como enviar um log de nível de aviso para Azure Monitor.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Explorar seus logs
Execute seu aplicativo no modo de depuração ou implante-o em tempo real.

No painel Visão geral do seu aplicativo no [portal de Application insights][portal], selecione [Pesquisar][diagnostic].

Você pode, por exemplo:

* Filtrar em rastreamentos de log ou em itens com propriedades específicas.
* Inspecione um item específico em detalhes.
* Encontre outros dados de log do sistema relacionados à mesma solicitação de usuário (tem a mesma operationId).
* Salve a configuração de uma página como um favorito.

> [!NOTE]
>Se seu aplicativo enviar muitos dados e você estiver usando o SDK do Application Insights para a versão ASP.NET 2.0.0-Beta3 ou posterior, o recurso de *amostragem adaptável* poderá operar e enviar apenas uma parte da sua telemetria. [Saiba mais sobre a amostragem.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="how-do-i-do-this-for-java"></a>Como fazer fazer isso para Java?
Use os [adaptadores de log Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Não há nenhuma opção de Application Insights no menu de contexto do projeto
* Verifique se o Developer Analytics Tools está instalado no computador de desenvolvimento. Em **ferramentas** do Visual Studio > **extensões e atualizações**, procure **Developer Analytics Tools**. Se não estiver na guia **instalado** , abra a guia **online** e instale-a.
* Esse pode ser um tipo de projeto para o qual as ferramentas de análise de devloper não dão suporte. Use a [instalação manual](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Não há nenhuma opção de adaptador de log na ferramenta de configuração
* Instale a estrutura de registro em log primeiro.
* Se você estiver usando System. Diagnostics. Trace, certifique-se de que você o tenha [configurado em *Web. config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Verifique se você tem a versão mais recente do Application Insights. No Visual Studio, acesse **ferramentas** > **extensões e atualizações**e abra a guia **atualizações** . Se **Developer Analytics Tools** estiver lá, selecione-o para atualizá-lo.

### <a name="emptykey"></a>Obtenho a mensagem de erro "a chave de instrumentação não pode estar vazia"
Você provavelmente instalou o pacote NuGet do adaptador de log sem instalar Application Insights. No Gerenciador de Soluções, clique com o botão direito do mouse em *ApplicationInsights. config*e selecione **Atualizar Application insights**. Você será solicitado a entrar no Azure e criar um recurso Application Insights ou reutilizar um existente. Isso deve corrigir o problema.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Posso ver rastreamentos, mas não outros eventos na pesquisa de diagnóstico
Pode levar algum tempo para que todos os eventos e solicitações passem pelo pipeline.

### <a name="limits"></a>Qual é a quantidade de dados retida?
Vários fatores afetam a quantidade de dados retidos. Para obter mais informações, consulte a seção [limites](../../azure-monitor/app/api-custom-events-metrics.md#limits) da página métricas de evento do cliente.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Não vejo algumas entradas de log que eu esperava
Se seu aplicativo enviar quantidades volumosos de dados e você estiver usando o SDK Application Insights para ASP.NET versão 2.0.0-Beta3 ou posterior, o recurso de amostragem adaptável poderá operar e enviar apenas uma parte da sua telemetria. [Saiba mais sobre a amostragem.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Passos seguintes

* [Diagnosticar falhas e exceções no ASP.NET][exceptions]
* [Saiba mais sobre a pesquisa][diagnostic]
* [Configurar testes de disponibilidade e capacidade de resposta][availability]
* [Resolução de Problemas][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
