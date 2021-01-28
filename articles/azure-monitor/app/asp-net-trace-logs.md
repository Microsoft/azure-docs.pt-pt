---
title: Explore registos de traços .NET em Insights de Aplicação
description: Registos de pesquisa gerados por Trace, NLog ou Log4Net.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/08/2019
ms.openlocfilehash: 083ddbd06561550f89e414d6c679cdc6433fa338
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937551"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Explore registos de traços .NET/.NET e Python em Insights de Aplicação

Envie registos de rastreio de diagnóstico para a sua aplicação core ASP.NET/ASP.NET a partir de ILogger, NLog, log4Net ou System.Diagnostics.Trace to [Azure Application Insights][start]. Para aplicações Python, envie registos de rastreio de diagnóstico utilizando AzureLogHandler em OpenCensus Python para Azure Monitor. Em seguida, pode explorá-los e revistá-los. Esses registos são fundidos com os outros ficheiros de registo da sua aplicação, para que possa identificar vestígios que estejam associados a cada pedido do utilizador e correlacioná-los com outros eventos e relatórios de exceção.

> [!NOTE]
> Precisa do módulo de captação de registos? É um adaptador útil para madeireiros de terceiros. Mas se ainda não estiver a utilizar NLog, log4Net ou System.Diagnostics.Trace, considere apenas ligar diretamente para [**o TrackTrace de Insights de Aplicação.).**](./api-custom-events-metrics.md#tracktrace)
>
>
## <a name="install-logging-on-your-app"></a>Instale o registo registado na sua aplicação
Instale a estrutura de registo escolhida no seu projeto, o que deverá resultar numa entrada em app.config ou web.config.

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

## <a name="configure-application-insights-to-collect-logs"></a>Configurar insights de aplicações para recolher registos
[Adicione Insights de Aplicação ao seu projeto](./asp-net.md) se ainda não o fez. Verá uma opção para incluir o colecionador de registos.

Ou clique com o botão direito no seu projeto no Solution Explorer para **configurar insights de aplicações.** Selecione a opção **de recolha de vestígios configurar.**

> [!NOTE]
> Sem menu Application Insights ou opção de colecionador de registos? Tente [resolver problemas.](#troubleshooting)

## <a name="manual-installation"></a>Instalação manual
Utilize este método se o seu tipo de projeto não for suportado pelo instalador Application Insights (por exemplo, um projeto de ambiente de trabalho windows).

1. Se pretender utilizar o log4Net ou o NLog, instale-o no seu projeto.
2. No Solution Explorer, clique com o botão direito do seu projeto e selecione **Gerir pacotes NuGet**.
3. Procure por "Insights de Aplicação".
4. Selecione um dos seguintes pacotes:

   - Para iLogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/) 
 [ ![ NuGet iLogger banner](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Para NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/) 
 [ ![ NuGet NLog banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Para Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/) 
 [ ![ NuGet Log4Net](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Para System.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) 
 [ ![ NuGet System.Diagnostics banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) 
 [ ![ Banner de ouvinte de fonte de diagnóstico nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) 
 [ ![ Banner de colecionador de NuGet Etw](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) 
 [ ![ Banner de ouvinte de fonte de evento de nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

O pacote NuGet instala os conjuntos necessários e modifica web.config ou app.config se for aplicável.

## <a name="ilogger"></a>ILogger

Para, por exemplo, utilizar a implementação do Application Insights ILogger com aplicações de consola e ASP.NET Core, consulte [ApplicationInsightsLoggerProvider para registos .NET Core ILogger](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Inserir chamadas de registo de diagnóstico
Se utilizar o System.Diagnostics.Trace, uma chamada típica seria:

```csharp
System.Diagnostics.Trace.TraceWarning("Slow response - database01");
```

Se preferir log4net ou NLog, use:

```csharp
    logger.Warn("Slow response - database01");
```

## <a name="use-eventsource-events"></a>Use eventos EventSource
Pode configurar [os eventos System.Diagnostics.Tracing.EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) a enviar para o Application Insights como vestígios. Primeiro, instale o `Microsoft.ApplicationInsights.EventSourceListener` pacote NuGet. Em seguida, edite a `TelemetryModules` secção do ficheiro [ApplicationInsights.config.](./configuration-with-applicationinsights-config.md)

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada fonte, pode definir os seguintes parâmetros:
 * **O nome** especifica o nome do EventSource para recolher.
 * **O nível** de registo especifica o nível de registo para recolher: *Crítico,* *Erro,* *Informação,* *LogAlways,* *Verbose* ou *Aviso*.
 * **Palavras-chave** (opcional) especificam o valor inteiro das combinações de palavras-chave a utilizar.

## <a name="use-diagnosticsource-events"></a>Use eventos DiagnosticSource
Pode configurar [os eventos System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) a serem enviados para o Application Insights como vestígios. Primeiro, instale o [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) pacote NuGet. Em seguida, edite a secção "TelemetriaModules" do ficheiro [ApplicationInsights.config.](./configuration-with-applicationinsights-config.md)

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Para cada DiagnosticSource que pretende localizar, adicione uma entrada com o atributo **Nome** definido para o nome do seu DiagnosticSource.

## <a name="use-etw-events"></a>Use eventos ETW
Pode configurar o rastreio de eventos para eventos windows (ETW) a serem enviados para o Application Insights como vestígios. Primeiro, instale o `Microsoft.ApplicationInsights.EtwCollector` pacote NuGet. Em seguida, edite a secção "TelemetriaModules" do ficheiro [ApplicationInsights.config.](./configuration-with-applicationinsights-config.md)

> [!NOTE] 
> Os eventos da ETW só podem ser recolhidos se o processo que acolhe o SDK for executado sob uma identidade que seja membro de Utilizadores ou Administradores de Registos de Desempenho.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada fonte, pode definir os seguintes parâmetros:
 * **ProviderName** é o nome do provedor da ETW para recolher.
 * **O ProviderGuid** especifica o GUID do fornecedor ETW para recolher. Pode ser usado em vez `ProviderName` de.
 * **O nível** define o nível de registo para recolher. Pode ser *Crítico,* *Erro*, *Informação,* *LogAlways,* *Verbose,* ou *Aviso*.
 * **As palavras-chave** (opcional) definem o valor inteiro das combinações de palavras-chave a utilizar.

## <a name="use-the-trace-api-directly"></a>Utilize a API trace diretamente
Pode ligar para os Insights de Aplicação rastreia a API diretamente. Os adaptadores de registo utilizam esta API.

Por exemplo:

```csharp
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
var telemetryClient = new TelemetryClient(configuration);
telemetry.TrackTrace("Slow response - database01");
```

Uma vantagem do TrackTrace é que pode colocar dados relativamente longos na mensagem. Por exemplo, pode codificar os dados POST lá.

Também pode adicionar um nível de gravidade à sua mensagem. E, tal como outras telemetrias, pode adicionar valores de propriedade para ajudar a filtrar ou procurar diferentes conjuntos de vestígios. Por exemplo:

  ```csharp
  TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
  var telemetryClient = new TelemetryClient(configuration);
  telemetryClient.TrackTrace("Slow database response",
                              SeverityLevel.Warning,
                              new Dictionary<string, string> { { "database", "db.ID" } });
  ```

Isto permitir-lhe-ia filtrar facilmente em [Pesquisa de][diagnostic] todas as mensagens de um determinado nível de gravidade que se relacionam com uma determinada base de dados.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler para OpenCensus Python
O Azure Monitor Log Handler permite-lhe exportar registos Python para o Azure Monitor.

Instrumento a sua aplicação com o [OpenCensus Python SDK](./opencensus-python.md) para O Monitor Azure.

Este exemplo mostra como enviar um registo de nível de aviso para o Monitor Azure.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Explore os seus registos
Execute a sua aplicação em modo depurar ou implemente-a ao vivo.

No painel de visão geral da sua aplicação no [portal 'Insights' de aplicações,][portal]selecione [Search][diagnostic].

Pode, por exemplo:

* Filtrar em vestígios de registo ou em itens com propriedades específicas.
* Inspecione um item específico em detalhe.
* Encontre outros dados de registo do sistema relacionados com o mesmo pedido do utilizador (tem o mesmo OperationId).
* Guarde a configuração de uma página como favorita.

> [!NOTE]
>Se a sua aplicação enviar muitos dados e estiver a utilizar o SDK Application Insights para ASP.NET versão 2.0.0-beta3 ou posterior, a *função de amostragem adaptativa* pode funcionar e enviar apenas uma parte da sua telemetria. [Saiba mais sobre amostragem.](./sampling.md)
>

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="how-do-i-do-this-for-java"></a>Como faço isto pela Java?
Na instrumentação sem código de Java (recomendada) os troncos são recolhidos fora da caixa, utilize o [agente Java 3.0](./java-in-process-agent.md).

Se estiver a utilizar o Java SDK, utilize os [adaptadores de registo Java](./java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Não há opção Application Insights no menu de contexto do projeto
* Certifique-se de que as Ferramentas de Análise do Desenvolvedor estão instaladas na máquina de desenvolvimento. Nas extensões e atualizações de **ferramentas** de estúdio  >  visual, procure **ferramentas** **de análise de desenvolvedores.** Se não estiver no separador **Instalado,** abra o separador **Online** e instale-o.
* Este pode ser um tipo de projeto que o Developer Analytics Tools não suporta. Utilize [a instalação manual](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Não há opção de adaptador de registo na ferramenta de configuração
* Instale primeiro a estrutura de registo.
* Se estiver a utilizar o System.Diagnostics.Trace, certifique-se de que o tem [configurado em *web.config*](/dotnet/api/system.diagnostics.eventlogtracelistener).
* Certifique-se de que tem a versão mais recente do Application Insights. No Estúdio Visual, vá a  >  **Extensões e Atualizações de Ferramentas** e abra o **separador Atualizações.** Se **o Desenvolvedor Analytics Tools** estiver lá, selecione-o para atualizá-lo.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>Recebo a mensagem de erro "Instrumentação não pode ser vazia"
Provavelmente instalou o pacote NuGet do adaptador de registo sem instalar o Application Insights. No Solution Explorer, *clique* ApplicationInsights.configà direita e selecione **Update Application Insights**. Será solicitado que entre no Azure e crie um recurso Application Insights ou reutilizar um existente. Isto deve resolver o problema.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Posso ver vestígios, mas não outros eventos em pesquisa de diagnóstico
Pode levar algum tempo para todos os eventos e pedidos para passar pelo oleoduto.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Quantos dados são retidos?
Vários fatores afetam a quantidade de dados que são retidos. Para mais informações, consulte a secção [limites](./api-custom-events-metrics.md#limits) da página de métricas do evento do cliente.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Não vejo algumas entradas de registo que esperava.
Se a sua aplicação enviar quantidades volumosas de dados e estiver a utilizar o SDK Application Insights para ASP.NET versão 2.0.0-beta3 ou posterior, a função de amostragem adaptativa pode funcionar e enviar apenas uma parte da sua telemetria. [Saiba mais sobre amostragem.](./sampling.md)

## <a name="next-steps"></a><a name="add"></a>Próximos passos

* [Diagnosticar falhas e exceções em ASP.NET][exceptions]
* [Saiba mais sobre pesquisar][diagnostic]
* [Configurar testes de disponibilidade e capacidade de resposta][availability]
* [Resolução de problemas][qna]

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[start]: ./app-insights-overview.md

