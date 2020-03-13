---
title: Explore os registos de rastreio .NET em Insights de Aplicação
description: Registos de pesquisa gerados por Trace, NLog ou Log4Net.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 273d5a2f4e1155541e159332312bdaa68aa175d7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276273"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Explore os registos de rastreio de .NET/.NET Core e Python em Insights de Aplicação

Envie registos de rastreio de diagnóstico para a sua aplicação ASP.NET/ASP.NET Core a partir de ILogger, NLog, log4Net ou System.Diagnostics.Trace to [Azure Application Insights][start]. Para aplicações Python, envie registos de rastreio de diagnóstico utilizando o AzureLogHandler no OpenCensus Python para o Monitor Azure. Pode então explorá-los e revistá-los. Esses registos são fundidos com os outros ficheiros de registo da sua aplicação, para que possa identificar vestígios que estejam associados a cada pedido do utilizador e correlacionar com outros eventos e relatórios de exceção.

> [!NOTE]
> Precisa do módulo de captura de registos? É um adaptador útil para madeireiros de terceiros. Mas se ainda não estiver a usar O NLog, log4Net ou System.Diagnostics.Trace, considere apenas chamar a [**Aplicação Insights TrackTrace()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) diretamente.
>
>
## <a name="install-logging-on-your-app"></a>Instale o registo na sua aplicação
Instale a sua estrutura de registo escolhida no seu projeto, o que deverá resultar numa entrada em app.config ou web.config.

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

## <a name="configure-application-insights-to-collect-logs"></a>Configure insights de aplicação para recolher registos
[Adicione insights de aplicação ao seu projeto](../../azure-monitor/app/asp-net.md) se ainda não o fez. Verá uma opção para incluir o colecionador de registos.

Ou clique no seu projeto no Solution Explorer para **configurar insights de aplicação**. Selecione a opção de recolha de **vestígios Configure.**

> [!NOTE]
> Sem menu De Insights de Aplicação ou opção de colecionador de registos? Tente [resolução de problemas.](#troubleshooting)

## <a name="manual-installation"></a>Instalação manual
Utilize este método se o seu tipo de projeto não for suportado pelo instalador Application Insights (por exemplo, um projeto de ambiente de trabalho windows).

1. Se planeia utilizar log4Net ou NLog, instale-o no seu projeto.
2. No Solution Explorer, clique no seu projeto e selecione **Gerir pacotes NuGet**.
3. Procure "Application Insights".
4. Selecione um dos seguintes pacotes:

   - Para iLogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Para NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Para Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Para system.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

O pacote NuGet instala os conjuntos necessários e modifica web.config ou app.config se for aplicável.

## <a name="ilogger"></a>ILogger

Por exemplo, utilizando a implementação do Application Insights ILogger com aplicações de consola e ASP.NET Core, consulte [ApplicationInsightsLoggerProvider para registos .NET Core ILogger](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Insira chamadas de registo de diagnóstico
Se utilizar o System.Diagnostics.Trace, uma chamada típica seria:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se preferir log4net ou NLog, utilize:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Utilizar eventos EventSource
Pode configurar os [eventos System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) a serem enviados para Application Insights como vestígios. Em primeiro lugar, instale o pacote `Microsoft.ApplicationInsights.EventSourceListener` NuGet. Em seguida, edite a secção `TelemetryModules` do ficheiro [ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada fonte, pode definir os seguintes parâmetros:
 * **O nome** especifica o nome do EventSource para recolher.
 * **O nível** especifica o nível de registo a recolher: *Crítico,* *Erro,* *Informacional,* *LogAlways,* *Verbose,* ou *Aviso*.
 * **As palavras-chave** (opcional) especificam o valor inteiro das combinações de palavras-chave a utilizar.

## <a name="use-diagnosticsource-events"></a>Use eventos DiagnosticSource
Pode configurar os [eventos System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) a serem enviados para Insights de Aplicação como vestígios. Em primeiro lugar, instale o pacote [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet. Em seguida, edite a secção "TelemettryModules" do ficheiro [ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Para cada DiagnosticSource pretende rastrear, adicione uma entrada com o nome do atributo do **nome** do seu DiagnosticSource.

## <a name="use-etw-events"></a>Utilizar eventos ETW
Pode configurar eventos de Rastreio de Eventos para Windows (ETW) a serem enviados para Application Insights como vestígios. Em primeiro lugar, instale o pacote `Microsoft.ApplicationInsights.EtwCollector` NuGet. Em seguida, edite a secção "TelemettryModules" do ficheiro [ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

> [!NOTE] 
> Os eventos ETW só podem ser recolhidos se o processo que acolhe o SDK for executado sob uma identidade que seja membro de Utilizadores ou Administradores de Registo de Desempenho.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada fonte, pode definir os seguintes parâmetros:
 * **ProviderName** é o nome do fornecedor ETW para recolher.
 * **O ProviderGuid** especifica o GUID do fornecedor ETW para cobrar. Pode ser usado em vez de `ProviderName`.
 * **O nível** define o nível de exploração a cobrar. Pode ser *Crítico*, *Erro,* *Informacional,* *LogAlways,* *Verbose*ou *Aviso*.
 * **As palavras-chave** (opcional) fixam o valor inteiro das combinações de palavras-chave a utilizar.

## <a name="use-the-trace-api-directly"></a>Utilize diretamente a API trace
Pode ligar diretamente para o rastreio de Insights de Aplicação da API. Os adaptadores de exploração de madeira utilizam esta API.

Por exemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Uma vantagem do TrackTrace é que pode colocar dados relativamente longos na mensagem. Por exemplo, pode codificar os dados do POST.

Também pode adicionar um nível de gravidade à sua mensagem. E, tal como outras telemetrias, pode adicionar valores de propriedade para ajudar a filtrar ou procurar diferentes conjuntos de vestígios. Por exemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Isto permitir-lhe-ia filtrar facilmente em [Procurar][diagnostic] todas as mensagens de um determinado nível de gravidade que se relacionassem com uma determinada base de dados.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler para OpenCensus Python
O Carregador de Registos Do Monitor Azure permite-lhe exportar registos Python para o Monitor Azure.

Instrumente a sua aplicação com o [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md) para o Monitor Azure.

Este exemplo mostra como enviar um registo de nível de aviso para o Monitor Azure.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Explore os seus registos
Execute a sua aplicação em modo dedepuraou-a ao vivo.

No painel de visão geral da sua aplicação no [portal Insights da aplicação,][portal]selecione [Search][diagnostic].

Pode, por exemplo:

* Filtrar em vestígios de registo ou em itens com propriedades específicas.
* Inspecione um item específico em detalhe.
* Encontre outros dados de registo do sistema relacionados com o mesmo pedido do utilizador (tem o mesmo OperationId).
* Guarde a configuração de uma página como favorita.

> [!NOTE]
>Se a sua aplicação enviar muitos dados e estiver a utilizar o SDK de Insights de Aplicação para ASP.NET versão 2.0.0-beta3 ou posterior, a função de *amostragem adaptativa* pode funcionar e enviar apenas uma parte da sua telemetria. [Saiba mais sobre a amostragem.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="how-do-i-do-this-for-java"></a>Como faço isto pela Java?
Utilize os [adaptadores](../../azure-monitor/app/java-trace-logs.md)de registo Java .

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Não há opção De insights de aplicação no menu de contexto do projeto
* Certifique-se de que as Ferramentas de Análise do Desenvolvedor estão instaladas na máquina de desenvolvimento. Na Visual Studio **Tools** > **Extensões e Atualizações,** procure ferramentas de análise de **desenvolvedores.** Se não estiver no separador **Instalado,** abra o separador **Online** e instale-o.
* Este pode ser um tipo de projeto que a Devloper Analytics Tools não suporta. Utilize [a instalação manual](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Não há opção de adaptador de log na ferramenta de configuração
* Instale primeiro a estrutura de exploração madeireira.
* Se estiver a utilizar o System.Diagnostics.Trace, certifique-se de que o tem [configurado em *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Certifique-se de que tem a versão mais recente do Application Insights. No Estúdio Visual, vá a **Tools** > **Extensões e Atualizações**e abra o separador **Atualizações.** Se estiver em **questão,** selecione-as para atualizá-la.

### <a name="emptykey"></a>Recebo a mensagem de erro "Chave de Instrumentação não pode estar vazia"
Provavelmente instalou o pacote nuget do adaptador de madeira sem instalar o Application Insights. No Solution Explorer, clique à direita *ApplicationInsights.config*, e selecione **Update Application Insights**. Será solicitado a iniciar sessão no Azure e criar um recurso Application Insights ou reutilizar um existente. Isto deve resolver o problema.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Posso ver vestígios, mas não outros eventos na pesquisa de diagnóstico.
Pode demorar um pouco para todos os eventos e pedidos para passar pelo oleoduto.

### <a name="limits"></a>Quantos dados são retidos?
Vários fatores afetam a quantidade de dados que são retidos. Para mais informações, consulte a secção de [limites](../../azure-monitor/app/api-custom-events-metrics.md#limits) da página métrica do evento do cliente.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Não vejo algumas entradas de registo que esperava.
Se a sua aplicação enviar quantidades volumosas de dados e estiver a utilizar o SDK de Insights de Aplicação para ASP.NET versão 2.0.0-beta3 ou posterior, a função de amostragem adaptativa pode funcionar e enviar apenas uma parte da sua telemetria. [Saiba mais sobre a amostragem.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Passos seguintes

* [Diagnosticar falhas e exceções no ASP.NET][exceptions]
* [Saiba mais sobre pesquisa][diagnostic]
* [Configurar testes de disponibilidade e resposta][availability]
* [Resolução de problemas][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
