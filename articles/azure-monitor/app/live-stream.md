---
title: Diagnóstico com Live Metrics Stream - Azure Application Insights
description: Monitorize a sua aplicação web em tempo real com métricas personalizadas e diagnostice problemas com um feed ao vivo de falhas, vestígios e eventos.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: 10818a531a43b50b86a6d413c7a504e2c19c3986
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2020
ms.locfileid: "85507351"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: Monitor & Diagnóstico com latência de 1 segundo

Monitorize a sua aplicação web ao vivo e em produção utilizando o Live Metrics Stream a partir de [Application Insights](../../azure-monitor/app/app-insights-overview.md). Selecione e filtra métricas e contadores de desempenho para assistir em tempo real, sem qualquer perturbação ao seu serviço. Inspecione os vestígios da pilha de pedidos e exceções falhados da amostra. Juntamente com [profiler](../../azure-monitor/app/profiler.md) e [Snapshot debugger,](../../azure-monitor/app/snapshot-debugger.md)live metrics stream fornece uma ferramenta de diagnóstico poderosa e não invasiva para o seu web site ao vivo.

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

1. [Instale insights de aplicação](../../azure-monitor/azure-monitor-app-hub.yml) na sua aplicação.
2. Para além dos pacotes padrão de Insights de [Aplicação, o Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) é necessário para ativar o fluxo de métricas ao vivo.
3. **Atualização para a versão mais recente** do pacote Application Insights. No Visual Studio, clique com o botão direito do seu projeto e escolha **pacotes Manage NuGet**. Abra o separador **Atualizações** e selecione todos os pacotes Microsoft.ApplicationInsights.*

    Volte a implementar a aplicação.

3. No [portal Azure,](https://portal.azure.com)abra o recurso Application Insights para a sua aplicação e, em seguida, abra live stream.

4. [Proteja o canal de controlo](#secure-the-control-channel) se utilizar dados sensíveis, tais como nomes de clientes nos seus filtros.

### <a name="no-data-check-your-server-firewall"></a>Não existem dados? Verifique a firewall do seu servidor

Verifique se as [portas de saída do Live Metrics Stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) estão abertas na firewall dos seus servidores.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Como é que o Live Metrics Stream difere das Métricas Explorer e Analytics?

| |Live Stream | Métricas Explorer e Analytics |
|---|---|---|
|Latência|Dados apresentados dentro de um segundo|Agregado ao longo de minutos|
|Sem retenção|Os dados persistem enquanto estão na tabela, e são depois descartados|[Dados retidos por 90 dias](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|A pedido|Os dados só são transmitidos enquanto o painel de Métricas Ao Vivo está aberto |Os dados são enviados sempre que o SDK é instalado e habilitado|
|Gratuito|Não há nenhuma taxa para os dados do Live Stream|Sujeito a [preços](../../azure-monitor/app/pricing.md)
|Amostragem|Todas as métricas e balcões selecionados são transmitidos. Falhas e vestígios de pilha são amostrados. Os processos de telemetria não são aplicados.|Os eventos podem ser [amostrados](../../azure-monitor/app/api-filtering-sampling.md)|
|Canal de controlo|Os sinais de controlo do filtro são enviados para o SDK. Recomendamos que proteja este canal.|A comunicação é uma maneira, para o portal|

## <a name="select-and-filter-your-metrics"></a>Selecione e filtre as suas métricas

(Disponível com ASP.NET, ASP.NET Core e Funções Azure (v2).)

Pode monitorizar o KPI personalizado ao vivo aplicando filtros arbitrários em qualquer telemetria de Insights de Aplicação a partir do portal. Clique no controlo do filtro que mostra quando se sobrepôs qualquer uma das tabelas. O gráfico seguinte está a traçar uma contagem de pedidos personalizada KPI com filtros nos atributos URL e Duração. Valide os seus filtros com a secção de pré-visualização do stream que mostra uma transmissão em direto de telemetria que corresponda aos critérios especificados em qualquer momento.

![Taxa de pedido de filtro](./media/live-stream/filter-request.png)

Pode monitorizar um valor diferente do Conde. As opções dependem do tipo de fluxo, que pode ser qualquer telemetria application Insights: pedidos, dependências, exceções, vestígios, eventos ou métricas. Pode ser a sua própria [medida personalizada:](../../azure-monitor/app/api-custom-events-metrics.md#properties)

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

![Falhas ao vivo amostradas](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>Filtrar por instância do servidor

Se pretender monitorizar uma determinada instância de função do servidor, pode filtrar por servidor. Para filtrar selecione o nome do servidor em *Servidores*.

![Falhas ao vivo amostradas](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>Proteja o canal de controlo

> [!NOTE]
> Atualmente, só é possível configurar um canal autenticado utilizando a monitorização da base de código e não é possível autenticar servidores utilizando um anexo sem código.

Os critérios de filtros personalizados especificados são enviados de volta para o componente Métricas Vivas no SDK application Insights. Os filtros podem potencialmente conter informações sensíveis, tais como os ClientesIDs. Pode tornar o canal seguro com uma chave API secreta para além da chave de instrumentação.
### <a name="create-an-api-key"></a>Criar uma chave API

![API > Criar chave API ](./media/live-stream/api-key.png)
 ![ Criar separador chave API. Selecione "autenticar o canal de controlo SDK" e depois "gerar chave"](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>Adicionar chave API à Configuração

### <a name="classic-aspnet"></a>ASP.NET clássico

No ficheiro applicationinsights.config, adicione o AuthenticationApiKey ao QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Ou em código, coloque-o no QuickPulseTelemetryModule:

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="azure-function-apps"></a>Aplicações de Funções do Azure

Para aplicações de função Azure (v2), assegurar o canal com uma chave API pode ser realizado com uma variável ambiental.

Crie uma chave API a partir do seu recurso Application Insights e vá para **Configurações de Aplicações** para a sua App de Função. **Selecione adicionar nova definição** e insira um nome `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` e um valor que corresponda à sua chave API.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-or-greater"></a>ASP.NET Core (requer insights de aplicação ASP.NET Core SDK 2.3.0 ou superior)

Modifique o seu ficheiro startup.cs da seguinte forma:

Primeiro adicionar

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Em seguida, dentro do método ConfigureServices adicionar:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

No entanto, se reconhecer e confiar em todos os servidores conectados, pode experimentar os filtros personalizados sem o canal autenticado. Esta opção está disponível por seis meses. Esta substituição é necessária uma vez que cada nova sessão, ou quando um novo servidor entra on-line.

![Opções de Auth Métricas Ao Vivo](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Recomendamos vivamente que crie o canal autenticado antes de introduzir informações potencialmente sensíveis, como o CustomerID nos critérios do filtro.
>

## <a name="supported-features-table"></a>Tabela de funcionalidades suportadas

| Linguagem                         | Métricas Básicas       | Métricas de desempenho | Filtragem personalizada    | Telemetria de amostra    | CPU dividido por processo |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET                             | Suportado (V2.7.2+) | Suportado (V2.7.2+) | Suportado (V2.7.2+) | Suportado (V2.7.2+) | Suportado (V2.7.2+)  |
| .NET Core (target=.NET Framework)| Suportado (V2.4.1+) | Suportado (V2.4.1+) | Suportado (V2.4.1+) | Suportado (V2.4.1+) | Suportado (V2.4.1+)  |
| .NET Core (target=.NET Core)     | Suportado (V2.4.1+) | Suportado*          | Suportado (V2.4.1+) | Suportado (V2.4.1+) | **Não Suportado**    |
| Funções Azure v2               | Suportado           | Suportado           | Suportado           | Suportado           | **Não Suportado**    |
| Java                             | Suportado (V2.0.0+) | Suportado (V2.0.0+) | **Não Suportado**   | **Não Suportado**   | **Não Suportado**    |
| Node.js                          | Suportado (V1.3.0+) | Suportado (V1.3.0+) | **Não Suportado**   | Suportado (V1.3.0+) | **Não Suportado**    |

As métricas básicas incluem pedido, dependência e taxa de exceção. As métricas de desempenho (contadores de desempenho) incluem memória e CPU. A telemetria da amostra mostra um fluxo de informações detalhadas para pedidos e dependências falhados, exceções, eventos e vestígios.

 \*O suporte perfCounters varia ligeiramente em todas as versões do .NET Core que não visam o Quadro .NET:

- As métricas perfCounters são suportadas quando são executadas no Azure App Service for Windows. (AspNetCore SDK Versão 2.4.1 ou superior)
- Os PerfCounters são suportados quando a aplicação está a ser em execução em quaisquer máquinas do Windows (VM ou Cloud Service ou On-prem etc.) (AspNetCore SDK Versão 2.7.1 ou superior), mas para aplicações que visam .NET Core 2.0 ou superior.
- Os PerfCounters são suportados quando a aplicação está em execução ANYWHERE (Linux, Windows, serviço de aplicações para Linux, contentores, etc.) na versão beta mais recente (i.e) AspNetCore SDK Versão 2.8.0-beta1 ou superior), mas para aplicações direcionadas para .NET Core 2.0 ou superior.

Por predefinição, as Métricas Vivas são desativadas no Node.js SDK. Para ativar as Métricas Vivas, adicione `setSendLiveMetrics(true)` aos seus [métodos de configuração](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) à medida que inicializa o SDK.

## <a name="troubleshooting"></a>Resolução de problemas

Não existem dados? Se a sua aplicação estiver numa rede protegida: Live Metrics Stream utiliza diferentes endereços IP do que outros telemetria de Insights de Aplicação. Certifique-se de que [os endereços IP](../../azure-monitor/app/ip-addresses.md) estão abertos na sua firewall.

## <a name="next-steps"></a>Passos seguintes
* [Monitorização da utilização com Insights de Aplicação](../../azure-monitor/app/usage-overview.md)
* [Usando a pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md)
* [Gerador de perfis](../../azure-monitor/app/profiler.md)
* [Debugger snapshot](../../azure-monitor/app/snapshot-debugger.md)
