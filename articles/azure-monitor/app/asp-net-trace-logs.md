---
title: Explore os logs de rastreamento do .NET no Application Insights
description: Pesquisar registos gerados com o Log4Net, NLog ou rastreio.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mbullwin
ms.openlocfilehash: 8c722eb0db3022620ba03e02dd2ae00f97a78f28
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552151"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Explore Core.NET/.NET registos de rastreio no Application Insights

Se usar ILogger, NLog, log4Net ou Trace para rastreamento de diagnóstico em seu aplicativo ASP.NET/ASP.NET Core, pode ter os registos enviados para o [do Azure Application Insights][start], em que pode explorar e procurá-los. Os registos serão intercalados com a outra telemetria provenientes de seu aplicativo, para que possa identificar os rastreios associados com cada pedido de utilizador de manutenção e correlacioná-los com outros eventos e os relatórios de exceções.

> [!NOTE]
> Precisa de módulo de registo de captura? É um adaptador útil para 3rd party loggers, mas se já não estiver a utilizar o NLog, o log4Net ou Trace, considere chamar apenas [tracktrace () do Application Insights](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) diretamente.
>
>

## <a name="install-logging-on-your-app"></a>Instalar a iniciar sessão na sua aplicação
Instale sua arquitetura de registo escolhida no seu projeto. Isso deve resultar numa entrada no App. config ou Web. config.

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
**[Adicionar o Application Insights ao seu projeto](../../azure-monitor/app/asp-net.md)**  se ainda não tiver feito. Verá uma opção para incluir o recoletor de registos.

Ou **configurar o Application Insights** clicando com o seu projeto no Explorador de soluções. Selecione a opção para **configurar a recolha de rastreio**.

*Nenhum menu ou registo recoletor opção do Application Insights?* Tente [resolução de problemas](#troubleshooting).

## <a name="manual-installation"></a>Instalação manual
Utilize este método se o tipo de projeto não é suportado pelo instalador do Application Insights (por exemplo um Windows projeto para Desktops).

1. Se planeja usar o log4Net ou NLog, instale-o em seu projeto.
2. No Solution Explorer, clique no projeto e escolha **gerir pacotes NuGet**.
3. Procurar “Application Insights”
4. Selecione um dos seguintes pacotes:

   - Para ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Para o NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Para o Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Para o System. Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

O pacote NuGet instala as assemblagens necessárias e, quando aplicável modifica o Web. config ou App. config.

## <a name="ilogger"></a>ILogger

Para exemplos de como utilizar o ILogger de informações da aplicação implementação com aplicativos de Console e ASP.NET Core, veja esta [artigo](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Inserir chamadas de registo de diagnóstico
Se usar Trace, uma chamada típica seria:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se preferir o log4net ou NLog:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>O uso de eventos de EventSource
Pode configurar [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) eventos a serem enviados para o Application Insights como rastreios. Primeiro, instale o `Microsoft.ApplicationInsights.EventSourceListener` pacote NuGet. Em seguida, edite `TelemetryModules` secção do [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) ficheiro.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada origem, pode definir os seguintes parâmetros:
 * `Name` Especifica o nome do EventSource para recolher.
 * `Level` Especifica o nível de registo para recolher. Pode ser um dos `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Opcional) Especifica o valor de número inteiro de combinações de palavras-chave a utilizar.

## <a name="using-diagnosticsource-events"></a>O uso de eventos de DiagnosticSource
Pode configurar [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) eventos a serem enviados para o Application Insights como rastreios. Primeiro, instale o [ `Microsoft.ApplicationInsights.DiagnosticSourceListener` ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) pacote NuGet. Em seguida, edite a `TelemetryModules` secção do [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) ficheiro.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Para cada DiagnosticSource que pretende analisar, adicione uma entrada com o `Name` atributo definido como o nome do seu DiagnosticSource.

## <a name="using-etw-events"></a>O uso de eventos do ETW
Pode configurar eventos ETW a serem enviados para o Application Insights como rastreios. Primeiro, instale o `Microsoft.ApplicationInsights.EtwCollector` pacote NuGet. Em seguida, edite `TelemetryModules` secção do [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) ficheiro.

> [!NOTE] 
> Apenas podem ser recolhidos eventos do ETW, se o processo que hospeda o SDK está a ser executado sob uma identidade que é um membro de "Utilizadores de registo de desempenho" ou administradores.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada origem, pode definir os seguintes parâmetros:
 * `ProviderName` é o nome do fornecedor ETW para recolher.
 * `ProviderGuid` Especifica o GUID do fornecedor ETW para recolher, pode ser utilizado em vez de `ProviderName`.
 * `Level` Define o nível de registo para recolher. Pode ser um dos `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Opcional) define o valor de número inteiro de combinações de palavra-chave a utilizar.

## <a name="using-the-trace-api-directly"></a>Usando diretamente o rastreio da API
Pode chamar o rastreio do Application Insights API diretamente. Os adaptadores de registo, utilize esta API.

Por exemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Uma vantagem TrackTrace é que pode colocar os dados relativamente longos na mensagem. Por exemplo, pode codificar dados de POSTAGEM lá.

Além disso, pode adicionar um nível de gravidade a sua mensagem. E, como outra telemetria, pode adicionar valores de propriedade que pode utilizar para ajudar a filtrar ou de pesquisa para diferentes conjuntos de rastreamentos. Por exemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Isso possibilitará que, no [pesquisa][diagnostic], para filtrar facilmente todas as mensagens de um nível de gravidade específico relacionadas à determinada base de dados.

## <a name="explore-your-logs"></a>Explore os seus registos
Executar a sua aplicação, em modo de depuração ou implementá-la em direto.

No painel de descrição geral da sua aplicação no [portal do Application Insights][portal], escolha [pesquisa][diagnostic].

Pode, por exemplo:

* Filtrar em rastreios de registos ou em itens com as propriedades específicas
* Inspecione um item específico em detalhes.
* Encontrar outra telemetria relacionados com o mesmo pedido de utilizador (ou seja, com o mesmo OperationId)
* Guardar a configuração desta página como um favorito

> [!NOTE]
> **Amostragem.** Se a aplicação enviar uma grande quantidade de dados e estiver a utilizar o Application Insights SDK para o ASP.NET versão 2.0.0-beta3 ou posterior, a funcionalidade de amostragem adaptável pode operar e enviar apenas uma percentagem da sua telemetria. [Saiba mais sobre a amostragem.](../../azure-monitor/app/sampling.md)
>
>

## <a name="next-steps"></a>Passos Seguintes
[Diagnosticar falhas e exceções no ASP.NET][exceptions]

[Saiba mais sobre a pesquisa][diagnostic].

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="how-do-i-do-this-for-java"></a>Como posso fazer isso para Java?
Utilize o [adaptadores de log Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Não existe nenhuma opção do Application Insights no menu de contexto do projeto
* Certifique-se de que ferramentas do Application Insights estão instaladas nesta máquina de desenvolvimento. No menu Ferramentas do Visual Studio, extensões e atualizações, procure o Application Insights Tools. Se não estiver no separador instalado, abra o separador Online e instalá-lo.
* Isso pode ser um tipo de projeto não é suportado pelas ferramentas do Application Insights. Uso [instalação manual](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>Nenhuma opção do adaptador de registo na ferramenta de configuração
* Tem de instalar primeiro a arquitetura de registo.
* Se estiver a utilizar Trace, certifique-se de que [configurada no `web.config` ](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Tem a versão mais recente do Application Insights? No Visual Studio **ferramentas** menu, escolha **extensões e atualizações**e abra a **atualizações** separador. Se as ferramentas de análise de programador estão lá, clique em atualizá-la.

### <a name="emptykey"></a>Recebo uma mensagem de erro "chave de instrumentação não pode estar vazio"
Parece que instalou o pacote de Nuget de placa de Registro em log sem instalar o Application Insights.

No Solution Explorer, clique com botão direito `ApplicationInsights.config` e escolha **atualização Application Insights**. Obterá uma caixa de diálogo que convida que inicie sessão no Azure e cria a um recurso do Application Insights, ou reutilize um já existente. Que deve corrigi-lo.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Posso ver rastreios na pesquisa de diagnóstico, mas não os outros eventos
Por vezes, pode demorar algum tempo para todos os eventos e os pedidos para obter através do pipeline.

### <a name="limits"></a>A quantidade de dados é retido?
Vários fatores afetam a quantidade de dados retidos. Consulte a [limites](../../azure-monitor/app/api-custom-events-metrics.md#limits) secção da página de métricas de eventos de cliente para obter mais informações. 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>Não estou a ver algumas das entradas de registo que esperava
Se a aplicação enviar uma grande quantidade de dados e estiver a utilizar o Application Insights SDK para o ASP.NET versão 2.0.0-beta3 ou posterior, a funcionalidade de amostragem adaptável pode operar e enviar apenas uma percentagem da sua telemetria. [Saiba mais sobre a amostragem.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Passos seguintes
* [Configurar a disponibilidade e testes de capacidade de resposta][availability]
* [Resolução de problemas][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
