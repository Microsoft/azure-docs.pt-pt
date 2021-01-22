---
title: Diagnóstico com Live Metrics Stream - Azure Application Insights
description: Monitorize a sua aplicação web em tempo real com métricas personalizadas e diagnostice problemas com um feed ao vivo de falhas, vestígios e eventos.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: 865de94f1d9b4012a908643bbf87f38aeb8594a0
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679471"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: Monitor & Diagnóstico com latência de 1 segundo

Monitorize a sua aplicação web ao vivo e em produção utilizando live metrics stream (também conhecido como QuickPulse) a partir de [Application Insights](./app-insights-overview.md). Selecione e filtra métricas e contadores de desempenho para assistir em tempo real, sem qualquer perturbação ao seu serviço. Inspecione os vestígios da pilha de pedidos e exceções falhados da amostra. Juntamente com [profiler](./profiler.md) e [Snapshot debugger,](./snapshot-debugger.md)live metrics stream fornece uma ferramenta de diagnóstico poderosa e não invasiva para o seu web site ao vivo.

Com live metrics stream, você pode:

* Valide uma correção enquanto é lançada, assistindo a desempenho e contagem de falhas.
* Observe o efeito das cargas de teste e diagnostice problemas ao vivo.
* Concentre-se em sessões de teste específicas ou filtre problemas conhecidos, selecionando e filtrando as métricas que pretende ver.
* Obtenham vestígios de exceção à medida que acontecem.
* Experimente com filtros para encontrar os KPI's mais relevantes.
* Monitorize qualquer contador de desempenho do Windows ao vivo.
* Identifique facilmente um servidor que esteja com problemas e filtre todos os feeds KPI/live para apenas esse servidor.

![Separador de métricas ao vivo](./media/live-stream/live-metric.png)

As Métricas Ao Vivo são atualmente suportadas para ASP.NET, ASP.NET Core, Azure Functions, Java e Node.js apps.

## <a name="get-started"></a>Introdução

1. Siga as diretrizes específicas da linguagem para ativar as Métricas Vivas.
   * [ASP.NET](./asp-net.md) - As Métricas vivas são ativadas por padrão.
   * [ASP.NET Core](./asp-net-core.md)- Métricas vivas é ativada por padrão.
   * [.NET/.NET Core Console/Worker](./worker-service.md)- As métricas ao vivo são ativadas por padrão.
   * [.NET Aplicações - Ativar a utilização do código](#enable-livemetrics-using-code-for-any-net-application).
    * [Java](./java-in-process-agent.md) - As Métricas Vivas são ativadas por padrão.
   * [Node.js](./nodejs.md#live-metrics)

2. No [portal Azure,](https://portal.azure.com)abra o recurso Application Insights para a sua aplicação e, em seguida, abra live stream.

3. [Proteja o canal de controlo](#secure-the-control-channel) se utilizar dados sensíveis, tais como nomes de clientes nos seus filtros.

### <a name="enable-livemetrics-using-code-for-any-net-application"></a>Ativar o LiveMetrics utilizando o código para qualquer aplicação .NET

Embora o LiveMetrics esteja ativado por padrão ao embarcar utilizando instruções recomendadas para aplicações .NET, o seguinte mostra como configurar as Métricas Vivas manualmente.

1. Instale o pacote NuGet [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)
2. O código de aplicação da consola de amostras que se segue mostra a criação de Métricas Vivas.

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using System;
using System.Threading.Tasks;

namespace LiveMetricsDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create a TelemetryConfiguration instance.
            TelemetryConfiguration config = TelemetryConfiguration.CreateDefault();
            config.InstrumentationKey = "INSTRUMENTATION-KEY-HERE";
            QuickPulseTelemetryProcessor quickPulseProcessor = null;
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    quickPulseProcessor = new QuickPulseTelemetryProcessor(next);
                    return quickPulseProcessor;
                })
                .Build();

            var quickPulseModule = new QuickPulseTelemetryModule();

            // Secure the control channel.
            // This is optional, but recommended.
            quickPulseModule.AuthenticationApiKey = "YOUR-API-KEY-HERE";
            quickPulseModule.Initialize(config);
            quickPulseModule.RegisterTelemetryProcessor(quickPulseProcessor);

            // Create a TelemetryClient instance. It is important
            // to use the same TelemetryConfiguration here as the one
            // used to setup Live Metrics.
            TelemetryClient client = new TelemetryClient(config);

            // This sample runs indefinitely. Replace with actual application logic.
            while (true)
            {
                // Send dependency and request telemetry.
                // These will be shown in Live Metrics stream.
                // CPU/Memory Performance counter is also shown
                // automatically without any additional steps.
                client.TrackDependency("My dependency", "target", "http://sample",
                    DateTimeOffset.Now, TimeSpan.FromMilliseconds(300), true);
                client.TrackRequest("My Request", DateTimeOffset.Now,
                    TimeSpan.FromMilliseconds(230), "200", true);
                Task.Delay(1000).Wait();
            }
        }
    }
}
```

Enquanto a amostra acima é para uma aplicação de consola, o mesmo código pode ser usado em qualquer aplicação .NET. Se houver ativações de outros TelemetriaModules que recolhem automaticamente a telemetria, é importante garantir que a mesma configuração utilizada para a inicialização desses módulos também é utilizada para o módulo Métricas Vivas.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Como é que o Live Metrics Stream difere das Métricas Explorer e Analytics?

| |Live Stream | Métricas Explorer e Analytics |
|---|---|---|
|**Latência**|Dados apresentados dentro de um segundo|Agregado ao longo de minutos|
|**Sem retenção**|Os dados persistem enquanto estão na tabela, e são depois descartados|[Dados retidos por 90 dias](./data-retention-privacy.md#how-long-is-the-data-kept)|
|**A pedido**|Os dados só são transmitidos enquanto o painel de Métricas Ao Vivo está aberto |Os dados são enviados sempre que o SDK é instalado e habilitado|
|**Gratuito**|Não há nenhuma taxa para os dados do Live Stream|Sujeito a [preços](./pricing.md)
|**Amostragem**|Todas as métricas e balcões selecionados são transmitidos. Falhas e vestígios de pilha são amostrados. |Os eventos podem ser [amostrados](./api-filtering-sampling.md)|
|**Canal de controlo**|Os sinais de controlo do filtro são enviados para o SDK. Recomendamos que proteja este canal.|A comunicação é uma maneira, para o portal|

## <a name="select-and-filter-your-metrics"></a>Selecione e filtre as suas métricas

(Disponível com ASP.NET, ASP.NET Core e Funções Azure (v2).)

Pode monitorizar o KPI personalizado ao vivo aplicando filtros arbitrários em qualquer telemetria de Insights de Aplicação a partir do portal. Clique no controlo do filtro que mostra quando se sobrepôs qualquer uma das tabelas. O gráfico seguinte está a traçar uma contagem de pedidos personalizada KPI com filtros nos atributos URL e Duração. Valide os seus filtros com a secção de pré-visualização do stream que mostra uma transmissão em direto de telemetria que corresponda aos critérios especificados em qualquer momento.

![Taxa de pedido de filtro](./media/live-stream/filter-request.png)

Pode monitorizar um valor diferente do Conde. As opções dependem do tipo de fluxo, que pode ser qualquer telemetria application Insights: pedidos, dependências, exceções, vestígios, eventos ou métricas. Pode ser a sua própria [medida personalizada:](./api-custom-events-metrics.md#properties)

![Construtor de consultas na taxa de pedido com métrica personalizada](./media/live-stream/query-builder-request.png)

Além da telemetria Do Application Insights, também pode monitorizar qualquer contador de desempenho do Windows selecionando-o a partir das opções de streaming e fornecendo o nome do contador de desempenho.

As métricas ao vivo são agregadas em dois pontos: localmente em cada servidor e, em seguida, em todos os servidores. Pode alterar o padrão em qualquer um dos casos, selecionando outras opções nas respetivas reduções.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Telemetria da Amostra: Eventos de diagnóstico ao vivo personalizados
Por padrão, a transmissão em direto dos eventos mostra amostras de pedidos falhados e chamadas de dependência, exceções, eventos e vestígios. Clique no ícone do filtro para ver os critérios aplicados em qualquer momento.

![Botão de filtro](./media/live-stream/filter.png)

Tal como acontece com as métricas, pode especificar quaisquer critérios arbitrários para qualquer um dos tipos de telemetria Application Insights. Neste exemplo, estamos a selecionar falhas específicas de pedidos e eventos.

![Construtor de Consultas](./media/live-stream/query-builder.png)

> [!NOTE]
> Atualmente, para critérios baseados em mensagens de exceção, utilize a mensagem de exceção mais externa. No exemplo anterior, filtrar a exceção benigna com mensagem de exceção interior (segue o delimiter "<--" ) "O cliente desligado". utilize uma mensagem que não contenha critérios de "Conteúdo de pedido de leitura de erro".

Consulte os detalhes de um item no feed ao vivo clicando nele. Pode fazer uma pausa no feed clicando em **Pausa** ou simplesmente deslocando-se para baixo ou clicando num item. O feed ao vivo será retomado depois de voltar ao topo, ou clicando no balcão dos itens recolhidos durante a pausa.

![A screenshot mostra a janela de telemetria Sample com uma exceção selecionada e os detalhes de exceção apresentados na parte inferior da janela.](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>Filtrar por instância do servidor

Se pretender monitorizar uma determinada instância de função do servidor, pode filtrar por servidor. Para filtrar selecione o nome do servidor em *Servidores*.

![Falhas ao vivo amostradas](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>Proteja o canal de controlo

> [!NOTE]
> Atualmente, só é possível configurar um canal autenticado utilizando a monitorização baseada em códigos e não é possível autenticar servidores utilizando um anexo sem código.

Os critérios de filtros personalizados especificados no portal Métricas Vivas são enviados de volta para o componente Métricas Vivas no SDK de Insights de Aplicação. Os filtros podem potencialmente conter informações sensíveis, tais como os ClientesIDs. Pode tornar o canal seguro com uma chave API secreta para além da chave de instrumentação.

### <a name="create-an-api-key"></a>Criar uma chave API

![API > Criar chave API ](./media/live-stream/api-key.png)
 ![ Criar separador chave API. Selecione "autenticar o canal de controlo SDK" e depois "gerar chave"](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>Adicionar chave API à Configuração

### <a name="aspnet"></a>ASP.NET

No ficheiro applicationinsights.config, adicione o AuthenticationApiKey ao QuickPulseTelemetryModule:

```XML
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>
```

### <a name="aspnet-core"></a>ASP.NET Core

Para ASP.NET aplicações [Core,](./asp-net-core.md) siga as instruções abaixo.

Modificar `ConfigureServices` o seu ficheiro Startup.cs da seguinte forma:

Adicione o seguinte espaço de nome.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Em seguida, modifique `ConfigureServices` o método como abaixo.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // existing code which include services.AddApplicationInsightsTelemetry() to enable Application Insights.
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
}
```

Mais informações sobre a configuração ASP.NET aplicações core podem ser encontradas na nossa orientação sobre [a configuração de módulos de telemetria em ASP.NET Core](./asp-net-core.md#configuring-or-removing-default-telemetrymodules).

### <a name="workerservice"></a>Serviço de Trabalhadores

Para aplicações [WorkerService,](./worker-service.md) siga as instruções abaixo.

Adicione o seguinte espaço de nome.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Em seguida, adicione a seguinte linha antes da chamada `services.AddApplicationInsightsTelemetryWorkerService` .

```csharp
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Mais informações sobre a configuração das aplicações do WorkerService podem ser encontradas na nossa orientação sobre [a configuração de módulos de telemetria nos Serviços de Trabalhadores.](./worker-service.md#configuring-or-removing-default-telemetrymodules)

### <a name="azure-function-apps"></a>Aplicações de Funções do Azure

Para aplicações de função Azure (v2), assegurar o canal com uma chave API pode ser realizado com uma variável ambiental.

Crie uma chave API a partir do seu recurso Application Insights e vá para **Definições > Configuração** para a sua App de Função. Selecione **nova definição** de aplicação e introduza um nome `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` e um valor que corresponda à sua chave API.

No entanto, se reconhecer e confiar em todos os servidores conectados, pode experimentar os filtros personalizados sem o canal autenticado. Esta opção está disponível por seis meses. Esta substituição é necessária uma vez que cada nova sessão, ou quando um novo servidor entra on-line.

![Opções de Auth Métricas Ao Vivo](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Recomendamos vivamente que crie o canal autenticado antes de introduzir informações potencialmente sensíveis, como o CustomerID nos critérios do filtro.
>

## <a name="supported-features-table"></a>Tabela de funcionalidades suportadas

| Linguagem                         | Métricas Básicas       | Métricas de desempenho | Filtragem personalizada    | Telemetria de amostra    | CPU dividido por processo |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET Framework                   | Suportado (V2.7.2+) | Suportado (V2.7.2+) | Suportado (V2.7.2+) | Suportado (V2.7.2+) | Suportado (V2.7.2+)  |
| .NET Core (target=.NET Framework)| Suportado (V2.4.1+) | Suportado (V2.4.1+) | Suportado (V2.4.1+) | Suportado (V2.4.1+) | Suportado (V2.4.1+)  |
| .NET Core (target=.NET Core)     | Suportado (V2.4.1+) | Suportado*          | Suportado (V2.4.1+) | Suportado (V2.4.1+) | **Não Suportado**    |
| Funções Azure v2               | Suportado           | Suportado           | Suportado           | Suportado           | **Não Suportado**    |
| Java                             | Suportado (V2.0.0+) | Suportado (V2.0.0+) | **Não Suportado**   | **Não Suportado**   | **Não Suportado**    |
| Node.js                          | Suportado (V1.3.0+) | Suportado (V1.3.0+) | **Não Suportado**   | Suportado (V1.3.0+) | **Não Suportado**    |

As métricas básicas incluem pedido, dependência e taxa de exceção. As métricas de desempenho (contadores de desempenho) incluem memória e CPU. A telemetria da amostra mostra um fluxo de informações detalhadas para pedidos e dependências falhados, exceções, eventos e vestígios.

 \* O suporte perfCounters varia ligeiramente em todas as versões do .NET Core que não visam o Quadro .NET:

- As métricas perfCounters são suportadas quando são executadas no Azure App Service for Windows. (AspNetCore SDK Versão 2.4.1 ou superior)
- Os PerfCounters são suportados quando a aplicação está a ser em execução em quaisquer máquinas do Windows (VM ou Cloud Service ou On-prem etc.) (AspNetCore SDK Versão 2.7.1 ou superior), mas para aplicações que visam .NET Core 2.0 ou superior.
- Os PerfCounters são suportados quando a aplicação está em execução ANYWHERE (Linux, Windows, serviço de aplicações para Linux, contentores, etc.) nas versões mais recentes (ou seja, as versões AspNetCore SDK 2.8.0 ou superior), mas apenas para aplicações direcionadas para .NET Core 2.0 ou superior.

## <a name="troubleshooting"></a>Resolução de problemas

Live Metrics Stream utiliza diferentes endereços IP do que outros telemetria de Insights de Aplicação. Certifique-se de que [os endereços IP](./ip-addresses.md) estão abertos na sua firewall. Verifique também se as [portas de saída do Live Metrics Stream](./ip-addresses.md#outgoing-ports) estão abertas na firewall dos seus servidores.

## <a name="next-steps"></a>Passos seguintes

* [Monitorização da utilização com Insights de Aplicação](./usage-overview.md)
* [Usando a pesquisa de diagnóstico](./diagnostic-search.md)
* [Gerador de perfis](./profiler.md)
* [Debugger snapshot](./snapshot-debugger.md)