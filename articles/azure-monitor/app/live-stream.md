---
title: Diagnosticar com informações de Aplicativo Azure de Live Metrics Stream
description: Monitore seu aplicativo Web em tempo real com métricas personalizadas e diagnostique problemas com um feed ao vivo de falhas, rastreamentos e eventos.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: 00fae22b91b2ad68392a21a29df3c2aec6bf5c5e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406736"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: monitorar o diagnóstico de & com latência de 1 segundo

Investigue o coração de pulsação de seu aplicativo Web em produção em tempo real usando Live Metrics Stream da [Application insights](../../azure-monitor/app/app-insights-overview.md). Selecione e filtre métricas e contadores de desempenho para observar em tempo real, sem nenhum distúrbios para o serviço. Inspecione os rastreamentos de pilha de exemplos de solicitações e exceções com falha. Junto com o [criador de perfil](../../azure-monitor/app/profiler.md), depurador de [instantâneos](../../azure-monitor/app/snapshot-debugger.md). O Live Metrics Stream fornece uma ferramenta de diagnóstico poderosa e não invasiva para seu site da Web em tempo real.

Com Live Metrics Stream, você pode:

* Valide uma correção enquanto ela é liberada observando o desempenho e as contagens de falhas.
* Assista ao efeito de cargas de teste e diagnostique problemas ao vivo.
* Concentre-se em sessões de teste específicas ou filtre problemas conhecidos, selecionando e filtrando as métricas que você deseja inspecionar.
* Obter rastreamentos de exceção conforme eles acontecem.
* Experimente filtros para localizar os KPIs mais relevantes.
* Monitore qualquer contador de desempenho do Windows Live.
* Identifique facilmente um servidor que está tendo problemas e filtre todo o KPI/feed ao vivo para apenas esse servidor.

[vídeo de Live Metrics Stream de ![](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Atualmente, há suporte para métricas em tempo real para aplicativos ASP.NET, ASP.NET Core, Azure Functions, Java e node. js.

## <a name="get-started"></a>Começar

1. Se você ainda não [instalou Application insights](../../azure-monitor/azure-monitor-app-hub.yml) em seu aplicativo Web, faça isso agora.
2. Além dos pacotes de Application Insights padrão, [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) é necessário para habilitar o Stream de métricas em tempo real.
3. **Atualize para a versão mais recente** do pacote de Application insights. No Visual Studio, clique com o botão direito do mouse em seu projeto e escolha **gerenciar pacotes NuGet**. Abra a guia **atualizações** e selecione todos os pacotes Microsoft. ApplicationInsights. *.

    Volte a implementar a aplicação.

3. No [portal do Azure](https://portal.azure.com), abra o recurso de Application insights para seu aplicativo e, em seguida, abra Live Stream.

4. [Proteja o canal de controle](#secure-the-control-channel) se você puder usar dados confidenciais, como nomes de clientes em seus filtros.

### <a name="no-data-check-your-server-firewall"></a>Não tem dados? Verifique o Firewall do servidor

Verifique se as [portas de saída para Live Metrics Stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) estão abertas no firewall dos servidores.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Como Live Metrics Stream diferem da Metrics Explorer e da análise?

| |Live Stream | Metrics Explorer e análise |
|---|---|---|
|Latência|Dados exibidos dentro de um segundo|Agregado em minutos|
|Sem retenção|Os dados persistem enquanto estão no gráfico e, em seguida, são descartados|[Dados retidos por 90 dias](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|A pedido|Os dados são transmitidos enquanto você abre as métricas ao vivo|Os dados são enviados sempre que o SDK é instalado e habilitado|
|Gratuito|Não há nenhum custo para Live Stream dados|Sujeito a [preços](../../azure-monitor/app/pricing.md)
|Amostragem|Todas as métricas e contadores selecionados são transmitidos. As falhas e os rastreamentos de pilha são amostrados. TelemetryProcessors não são aplicadas.|Os eventos podem ser [amostrados](../../azure-monitor/app/api-filtering-sampling.md)|
|Canal de controle|Os sinais de controle de filtro são enviados para o SDK. Recomendamos que você proteja esse canal.|A comunicação é de uma maneira para o portal|

## <a name="select-and-filter-your-metrics"></a>Selecionar e filtrar suas métricas

(Disponível com ASP.NET, ASP.NET Core e Azure Functions (v2).)

Você pode monitorar o KPI personalizado em tempo real aplicando filtros arbitrários em qualquer telemetria de Application Insights do Portal. Clique no controle de filtro que aparece quando você passa o mouse sobre qualquer um dos gráficos. O gráfico a seguir está plotando um KPI de contagem de solicitação personalizada com filtros nos atributos URL e Duration. Valide seus filtros com a seção visualização de fluxo que mostra um feed ao vivo de telemetria que corresponde aos critérios que você especificou em qualquer ponto no tempo.

![KPI de solicitação personalizada](./media/live-stream/live-stream-filteredMetric.png)

Você pode monitorar um valor diferente da contagem. As opções dependem do tipo de fluxo, que pode ser qualquer Application Insights telemetria: solicitações, dependências, exceções, rastreamentos, eventos ou métricas. Pode ser sua própria [medida personalizada](../../azure-monitor/app/api-custom-events-metrics.md#properties):

![Opções de valor](./media/live-stream/live-stream-valueoptions.png)

Além de Application Insights telemetria, você também pode monitorar qualquer contador de desempenho do Windows selecionando-o nas opções de fluxo e fornecendo o nome do contador de desempenho.

As métricas ao vivo são agregadas em dois pontos: localmente em cada servidor e em todos os servidores. Você pode alterar o padrão em qualquer um deles selecionando outras opções nos respectivos menus suspensos.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Telemetria de exemplo: eventos de diagnóstico dinâmicos personalizados
Por padrão, o feed ao vivo de eventos mostra exemplos de solicitações com falha e chamadas de dependência, exceções, eventos e rastreamentos. Clique no ícone de filtro para ver os critérios aplicados em qualquer ponto no tempo. 

![Feed ao vivo padrão](./media/live-stream/live-stream-eventsdefault.png)

Assim como acontece com as métricas, você pode especificar qualquer critério arbitrário para qualquer um dos tipos de telemetria Application Insights. Neste exemplo, estamos selecionando falhas de solicitação específicas, rastreamentos e eventos. Também estamos selecionando todas as exceções e falhas de dependência.

![Feed ao vivo personalizado](./media/live-stream/live-stream-events.png)

Observação: no momento, para critérios baseados em mensagem de exceção, use a mensagem de exceção mais externa. No exemplo anterior, para filtrar a exceção benigno com a mensagem de exceção interna (segue o delimitador "<--") "o cliente desconectou". Use uma mensagem que não contém os critérios de "erro ao ler o conteúdo da solicitação".

Consulte os detalhes de um item no feed ao vivo clicando nele. Você pode pausar o feed clicando em **Pausar** ou simplesmente rolando para baixo ou clicando em um item. O Live feed será retomado depois que você rolar de volta para a parte superior ou clicando no contador de itens coletados enquanto ele estava em pausa.

![Amostras de falhas ao vivo](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrar por instância do servidor

Se você quiser monitorar uma instância de função de servidor específica, poderá filtrar por servidor.

![Amostras de falhas ao vivo](./media/live-stream/live-stream-filter.png)

## <a name="secure-the-control-channel"></a>Proteger o canal de controle
Os critérios de filtros personalizados especificados são enviados de volta ao componente de métricas em tempo real no SDK do Application Insights. Potencialmente, os filtros podem conter informações confidenciais, como customerIDs. Você pode tornar o canal seguro com uma chave de API secreta além da chave de instrumentação.
### <a name="create-an-api-key"></a>Criar uma chave de API

![Criar chave de API](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Adicionar chave de API à configuração

### <a name="classic-aspnet"></a>ASP.NET clássico

No arquivo applicationinsights. config, adicione o AuthenticationApiKey ao QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Ou no código, defina-o no QuickPulseTelemetryModule:

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

Para os aplicativos de funções do Azure (v2), a proteção do canal com uma chave de API pode ser realizada com uma variável de ambiente.

Crie uma chave de API de dentro de seu Application Insights recurso e vá para **configurações de aplicativo** para seu aplicativo de funções. Selecione **Adicionar nova configuração** e insira um nome de `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` e um valor que corresponda à sua chave de API.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-or-greater"></a>ASP.NET Core (requer Application Insights SDK do ASP.NET Core 2.3.0 ou superior)

Modifique o arquivo startup.cs da seguinte maneira:

Primeiro adicionar

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Em seguida, dentro do método configureservices, adicione:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

No entanto, se você reconhecer e confiar em todos os servidores conectados, poderá tentar os filtros personalizados sem o canal autenticado. Essa opção está disponível por seis meses. Essa substituição é necessária uma vez a cada nova sessão ou quando um novo servidor fica online.

![Opções de autenticação de métricas dinâmicas](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>É altamente recomendável que você configure o canal autenticado antes de inserir informações potencialmente confidenciais, como CustomerID, nos critérios de filtro.
>

## <a name="supported-features-table"></a>Tabela de recursos com suporte

| Linguagem                         | Métricas básicas       | Métricas de desempenho | Filtragem personalizada    | Telemetria de exemplo    | Divisão de CPU por processo |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET                             | Com suporte (V 2.7.2 +) | Com suporte (V 2.7.2 +) | Com suporte (V 2.7.2 +) | Com suporte (V 2.7.2 +) | Com suporte (V 2.7.2 +)  |
| .NET Core (Target =. NET Framework)| Com suporte (V 2.4.1 +) | Com suporte (V 2.4.1 +) | Com suporte (V 2.4.1 +) | Com suporte (V 2.4.1 +) | Com suporte (V 2.4.1 +)  |
| .NET Core (Target =. NET Core)     | Com suporte (V 2.4.1 +) | Suportado*          | Com suporte (V 2.4.1 +) | Com suporte (V 2.4.1 +) | **Não Suportado**    |
| Azure Functions v2               | Suportadas           | Suportadas           | Suportadas           | Suportadas           | **Não Suportado**    |
| Java                             | Com suporte (V 2.0.0 +) | Com suporte (V 2.0.0 +) | **Não Suportado**   | **Não Suportado**   | **Não Suportado**    |
| Node.js                          | Com suporte (V 1.3.0 +) | Com suporte (V 1.3.0 +) | **Não Suportado**   | Com suporte (V 1.3.0 +) | **Não Suportado**    |

As métricas básicas incluem solicitação, dependência e taxa de exceção. As métricas de desempenho (contadores de desempenho) incluem memória e CPU. A telemetria de exemplo mostra um fluxo de informações detalhadas para solicitações e dependências com falha, exceções, eventos e rastreamentos.

 \* suporte a PerfCounters varia ligeiramente entre as versões do .NET Core que não têm como alvo o .NET Framework:

- Há suporte para métricas PerfCounters ao executar no serviço Azure App para Windows. (AspNetCore SDK versão 2.4.1 ou superior)
- PerfCounters têm suporte quando o aplicativo está em execução em qualquer computador Windows (VM ou serviço de nuvem ou local, etc.) (AspNetCore SDK versão 2.7.1 ou superior), mas para aplicativos destinados ao .NET Core 2,0 ou superior.
- Há suporte para PerfCounters quando o aplicativo está em execução em qualquer lugar (Linux, Windows, serviço de aplicativo para Linux, contêineres, etc.) na versão beta mais recente (ou seja, AspNetCore SDK versão 2.8.0-beta1 ou superior), mas para aplicativos destinados ao .NET Core 2,0 ou superior.

Por padrão, as métricas ao vivo são desabilitadas no SDK do node. js. Para habilitar as métricas em tempo real, adicione `setSendLiveMetrics(true)` aos seus [métodos de configuração](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) à medida que você inicializar o SDK.

## <a name="troubleshooting"></a>Resolução de problemas

Não tem dados? Se seu aplicativo estiver em uma rede protegida: Live Metrics Stream usa endereços IP diferentes dos outros Application Insights telemetria. Verifique se [esses endereços IP](../../azure-monitor/app/ip-addresses.md) estão abertos no firewall.

## <a name="next-steps"></a>Passos seguintes
* [Monitorando o uso com Application Insights](../../azure-monitor/app/usage-overview.md)
* [Usando a pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md)
* [Gerador de perfis](../../azure-monitor/app/profiler.md)
* [Depurador de instantâneos](../../azure-monitor/app/snapshot-debugger.md)
