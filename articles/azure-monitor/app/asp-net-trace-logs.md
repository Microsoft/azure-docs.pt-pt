---
title: Explore os logs de rastreamento do .NET no Application Insights
description: Pesquisar registos gerados pelo Log4Net, NLog ou rastreio.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbullwin
ms.openlocfilehash: d366f363b7bd1d5306d598c9b38258eb78076b7c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65472056"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Explore Core.NET/.NET registos de rastreio no Application Insights

Enviar registos de rastreio de diagnóstico para a sua aplicação ASP.NET/ASP.NET Core a partir ILogger, NLog, log4Net ou Trace para [do Azure Application Insights][start]. Em seguida, pode explorar e procurá-los. Esses logs são mesclados com os outros ficheiros de registo da sua aplicação, para que possa identificar os rastreios que estão associados a cada pedido de utilizador e correlação-los com outros eventos e os relatórios de exceções.

> [!NOTE]
> É necessário o módulo de captura de log? É um adaptador útil para agentes Coletores de terceiros. Mas se ainda não usa Trace, log4Net ou NLog, considere chamar apenas [ **tracktrace () do Application Insights** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) diretamente.
>
>
## <a name="install-logging-on-your-app"></a>Instalar a iniciar sessão na sua aplicação
Instale sua arquitetura de registo escolhida no seu projeto, o que deve resultar numa entrada no App. config ou Web. config.

```XML
    <configuration>
      <system.diagnostics>
    <trace autoflush="true" indentsize="0">
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Configurar o Application Insights para recolher registos
[Adicionar o Application Insights ao seu projeto](../../azure-monitor/app/asp-net.md) se ainda não tiver feito. Verá uma opção para incluir o recoletor de registos.

Ou clique com o botão direito do rato no Explorador de soluções para **configurar o Application Insights**. Selecione o **configurar a recolha de rastreio** opção.

> [!NOTE]
> Nenhum menu ou registo recoletor opção do Application Insights? Tente [resolução de problemas](#troubleshooting).

## <a name="manual-installation"></a>Instalação manual
Utilize este método se o tipo de projeto não é suportado pelo instalador do Application Insights (por exemplo um Windows projeto para Desktops).

1. Se planeja usar o log4Net ou NLog, instale-o em seu projeto.
2. No Solution Explorer, clique no projeto e selecione **gerir pacotes NuGet**.
3. Procurar "Application Insights."
4. Selecione um dos seguintes pacotes:

   - Para ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Para o NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Para o Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Para o System. Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

O pacote NuGet instala as assemblagens necessárias e modifica o Web. config ou App. config se de que se aplica.

## <a name="ilogger"></a>ILogger

Para obter exemplos de como utilizar a implementação do Application Insights ILogger com aplicativos de console e ASP.NET Core, veja [ApplicationInsightsLoggerProvider para .NET Core ILogger regista](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Inserir chamadas de registo de diagnóstico
Se usar Trace, uma chamada típica seria:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se preferir o log4net ou NLog, utilize:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Usar eventos de EventSource
Pode configurar [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) eventos a serem enviados para o Application Insights como rastreios. Primeiro, instale o `Microsoft.ApplicationInsights.EventSourceListener` pacote NuGet. Em seguida, edite a `TelemetryModules` secção do [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) ficheiro.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada origem, pode definir os seguintes parâmetros:
 * **Nome** Especifica o nome do EventSource para recolher.
 * **Nível** Especifica o nível de registo para recolher: *Crítico*, *erro*, *informativa*, *LogAlways*, *verboso*, ou *aviso*.
 * **Palavras-chave** (opcional) especificar o valor de número inteiro de combinações de palavra-chave a utilizar.

## <a name="use-diagnosticsource-events"></a>Usar eventos de DiagnosticSource
Pode configurar [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) eventos a serem enviados para o Application Insights como rastreios. Primeiro, instale o [ `Microsoft.ApplicationInsights.DiagnosticSourceListener` ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) pacote NuGet. Em seguida, edite a secção "TelemetryModules" a [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) ficheiro.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Para cada DiagnosticSource que pretende analisar, adicione uma entrada com o **nome** atributo definido como o nome do seu DiagnosticSource.

## <a name="use-etw-events"></a>Usar eventos do ETW
Pode configurar eventos de rastreio de eventos para Windows (ETW) a serem enviados para o Application Insights como rastreios. Primeiro, instale o `Microsoft.ApplicationInsights.EtwCollector` pacote NuGet. Em seguida, edite a secção "TelemetryModules" a [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) ficheiro.

> [!NOTE] 
> Apenas podem ser recolhidos eventos do ETW, se o processo que hospeda o SDK é executado sob uma identidade que seja membro dos administradores ou utilizadores de registo de desempenho.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada origem, pode definir os seguintes parâmetros:
 * **ProviderName** é o nome do fornecedor ETW para recolher.
 * **ProviderGuid** Especifica o GUID do fornecedor ETW para recolher. Ele pode ser usado em vez de `ProviderName`.
 * **Nível** define o nível de registo para recolher. Pode ser *crítico*, *erro*, *informativo*, *LogAlways*, *verboso*, ou *Aviso*.
 * **Palavras-chave** (opcional) defina o valor de número inteiro de combinações de palavra-chave a utilizar.

## <a name="use-the-trace-api-directly"></a>Usar diretamente o rastreio da API
Pode chamar o rastreio do Application Insights API diretamente. Os adaptadores de registo, utilize esta API.

Por exemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Uma vantagem TrackTrace é que pode colocar os dados relativamente longos na mensagem. Por exemplo, pode codificar dados de POSTAGEM lá.

Também pode adicionar um nível de gravidade a sua mensagem. E, como outra telemetria, pode adicionar valores de propriedade para o ajudar a filtrar ou de pesquisa para diferentes conjuntos de rastreamentos. Por exemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Isso permitiria que facilmente filtrar na [pesquisa] [ diagnostic] todas as mensagens de um nível de gravidade específico que se relacionam com a base de dados específica.

## <a name="explore-your-logs"></a>Explore os seus registos
Executar a sua aplicação no modo de depuração ou implementá-la em direto.

No painel de descrição geral da sua aplicação no [portal do Application Insights][portal], selecione [pesquisa][diagnostic].

Pode, por exemplo:

* Filtrar em rastreios de registos ou em itens com as propriedades específicas.
* Inspecione um item específico em detalhes.
* Encontre outros dados de registo do sistema que está relacionado com o mesmo pedido de utilizador (tem o mesmo OperationId).
* Guarde a configuração de uma página como um favorito.

> [!NOTE]
>Se a sua aplicação envia uma grande quantidade de dados e estiver a utilizar o Application Insights SDK para ASP.NET versão 2.0.0-Beta3 ou posterior, o *amostragem adaptável* funcionalidade pode operar e enviar apenas uma parte da sua telemetria. [Saiba mais sobre a amostragem.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="how-do-i-do-this-for-java"></a>Como posso fazer isso para Java?
Utilize o [adaptadores de log Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Não existe nenhuma opção do Application Insights no menu de contexto do projeto
* Certifique-se de que o do Developer Analytics Tools está instalado na máquina de desenvolvimento. No Visual Studio **ferramentas** > **extensões e atualizações**, procure **Developer Analytics Tools**. Se não estiver no **instalada** separador, abra o **Online** separador e instalá-lo.
* Isso pode ser um tipo de projeto que ferramentas de análise de Devloper não suporta. Uso [instalação manual](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Não existe nenhuma opção do adaptador de registo na ferramenta de configuração
* Instale primeiro a arquitetura de registo.
* Se estiver a utilizar Trace, certifique-se de que tenha [configurada no *Web. config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Certifique-se de que tem a versão mais recente do Application Insights. No Visual Studio, aceda a **ferramentas** > **extensões e atualizações**e abra o **atualizações** separador. Se **Developer Analytics Tools** está lá, selecione-o para atualizá-lo.

### <a name="emptykey"></a>Posso obter a mensagem de erro "chave de instrumentação não pode estar vazio"
Provavelmente, instalado o pacote de Nuget de placa de Registro em log sem instalar o Application Insights. No Solution Explorer, clique com botão direito *applicationinsights. config*e selecione **atualização Application Insights**. Será solicitado a iniciar sessão no Azure e criar um recurso do Application Insights ou reutilizar um já existente. Que deve resolver o problema.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Posso ver rastreios, mas não para outros eventos na pesquisa de diagnóstico
Pode demorar algum tempo para todos os eventos e os pedidos para obter através do pipeline.

### <a name="limits"></a>A quantidade de dados é retido?
Vários fatores afetam a quantidade de dados que são retidos. Para obter mais informações, consulte a [limites](../../azure-monitor/app/api-custom-events-metrics.md#limits) secção da página de métricas de eventos de cliente.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Não vejo algumas entradas de log que eu esperava
Se a sua aplicação enviar grandes quantidades de dados e estiver a utilizar o Application Insights SDK para ASP.NET versão 2.0.0-Beta3 ou posterior, a funcionalidade de amostragem adaptável pode operar e enviar apenas uma parte da sua telemetria. [Saiba mais sobre a amostragem.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Passos seguintes

* [Diagnosticar falhas e exceções no ASP.NET][exceptions]
* [Saiba mais sobre a pesquisa][diagnostic]
* [Configurar a disponibilidade e testes de capacidade de resposta][availability]
* [Resolução de problemas][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md