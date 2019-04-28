---
title: Live Stream de métricas com métricas e diagnósticos do Azure Application Insights | Documentos da Microsoft
description: Monitorizar a sua aplicação web em tempo real com métricas personalizadas e diagnosticar problemas com uma transmissão em direto de falhas, rastreios e eventos.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: bc85de0c8ec89ea88d2bae8e3f226da7d3163f53
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115359"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Stream de métricas em direto: Monitorizar e diagnosticar com latência de 1 segundo

Sonda o coração de beating da sua aplicação web em direto em produção com o Stream de métricas em direto da [Application Insights](../../azure-monitor/app/app-insights-overview.md). Selecionar e filtrar os contadores de desempenho e de métricas para ver em tempo real, sem qualquer perturbação ao seu serviço. Inspecione os rastreamentos de pilha de exceções e pedidos de amostra falhada. Em conjunto com [Profiler](../../azure-monitor/app/profiler.md), [depurador de instantâneos](../../azure-monitor/app/snapshot-debugger.md), e [testes de desempenho](../../azure-monitor/app/monitor-web-app-availability.md#performance-tests), Stream de métricas em direto fornece uma ferramenta de diagnóstico avançada e não INVASIVO para a web em direto site.

Com o Stream de métricas em direto, pode:

* Validar uma correção enquanto ela foi lançada, observando as contagens de desempenho e falha.
* Ver o efeito de cargas de teste e diagnosticar problemas em direto. 
* Se concentrar em sessões de teste específico ou filtrar os problemas conhecidos, ao selecionar e filtrar as métricas que pretende ver.
* Obter rastreios de exceção à medida que acontecem.
* Experimente com filtros para procurar os KPIs mais relevantes.
* Monitorize qualquer Windows desempenho contador live.
* Identifique facilmente um servidor que está a ter problemas e filtrar que todos o KPI/live feed para apenas esse servidor.

[![Vídeo de Stream de métricas em tempo real](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Métricas dinâmicas são atualmente suportadas para aplicações ASP.NET, ASP.NET Core, as funções do Azure, Java e node. js.

## <a name="get-started"></a>Introdução

1. Se ainda não ainda [instale o Application Insights](../../azure-monitor/azure-monitor-app-hub.md) na sua aplicação web, faça-o agora.
2. Além dos pacotes padrão Application Insights [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) é necessária para ativar o Live Metrics stream.
3. **Atualização para a versão mais recente** do pacote do Application Insights. No Visual Studio, clique no projeto e escolha **gerir pacotes Nuget**. Abra o **atualizações** separador e selecione todos os pacotes de Microsoft.ApplicationInsights.*.

    Volte a implementar a aplicação.

3. Na [portal do Azure](https://portal.azure.com), abra o recurso do Application Insights para a sua aplicação e, em seguida, abra o Live Stream.

4. [Proteja o canal de controlo](#secure-the-control-channel) se pode utilizar os dados confidenciais, tais como nomes de clientes nos filtros.

### <a name="nodejs"></a>Node.js

Utilizar métricas em direto com o node. js tem de atualizar para versão 1.30 ou superior do SDK. Por predefinição, métricas em direto está desativada no SDK do node. js. Para ativar a adicionar métricas em direto `setSendLiveMetrics(true)` a sua [métodos de configuração](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) como inicializar o SDK.

### <a name="no-data-check-your-server-firewall"></a>Não existem dados? Verifique a firewall do servidor

Verifique os [portas de saída para o Stream de métricas em direto](../../azure-monitor/app/ip-addresses.md#outgoing-ports) estão abertas no firewall dos seus servidores. 

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Stream de métricas em direto difere do Explorador de métricas e análise?

| |Live Stream | Explorador de métricas e análise |
|---|---|---|
|Latência|Dados apresentados dentro de um segundo|Agregado ao longo de minutos|
|Nenhuma retenção|Dados persistir enquanto ele está no gráfico e, em seguida, é rejeitado|[Dados retidos durante 90 dias](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|A pedido|Dados são transmitidos ao abrir o Live Metrics|Os dados são enviados sempre que o SDK está instalado e ativado|
|Gratuito|Não tem custos para os dados do Stream em direto|Sujeito à [preços](../../azure-monitor/app/pricing.md)
|Amostragem|Contadores e métricas de todos os selecionados são transmitidas. Falhas e de rastreamentos de pilha são objeto de amostragem. TelemetryProcessors não são aplicadas.|Eventos podem ter [amostragem](../../azure-monitor/app/api-filtering-sampling.md)|
|Canal de controlo|Sinais de controle de filtro são enviadas para o SDK. Recomendamos que proteja este canal.|A comunicação é unidirecional, para o portal|

## <a name="select-and-filter-your-metrics"></a>Selecionar e filtrar as suas métricas

(Disponível com ASP.NET, ASP.NET Core e as funções do Azure (v2).)

Pode monitorizar ao vivo de KPI personalizado ao aplicar os filtros arbitrários em qualquer telemetria do Application Insights no portal. Clique no controle de filtro que mostra quando mouse-over qualquer um dos gráficos. A tabela a seguir é desenhar uma contagem de pedido de KPI personalizada com filtros nos atributos de URL e duração. Valide os filtros com a secção de pré-visualização do Stream, que mostra uma transmissão em direto de telemetria que corresponda os critérios especificados em qualquer ponto no tempo. 

![Pedido personalizado KPI](./media/live-stream/live-stream-filteredMetric.png)

Pode monitorizar um valor diferente da contagem. As opções dependem do tipo de fluxo, o que poderia ser qualquer telemetria do Application Insights: pedidos, dependências, exceções, rastreios, eventos ou métricas. Pode ser sua própria [medida personalizada](../../azure-monitor/app/api-custom-events-metrics.md#properties):

![Opções de valor](./media/live-stream/live-stream-valueoptions.png)

Além de telemetria do Application Insights, também é possível monitorizar qualquer contador de desempenho do Windows ao selecionar que entre as opções de fluxo e indicar o nome do contador de desempenho.

Métricas dinâmicas são agregadas ao dois pontos: localmente em cada servidor e, em seguida, em todos os servidores. Pode alterar o padrão em qualquer um ao selecionar outras opções no respetivas suspensas.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Telemetria de exemplo: Eventos de diagnóstico em direto de personalizado
Por predefinição, a transmissão em direto de eventos mostra exemplos de pedidos falhados e chamadas de dependências, exceções, eventos e rastreios. Clique no ícone de filtro para ver os critérios aplicados em qualquer ponto no tempo. 

![Feed em direto do padrão](./media/live-stream/live-stream-eventsdefault.png)

Como com a métrica, pode especificar qualquer critérios arbitrários para qualquer um dos tipos de telemetria do Application Insights. Neste exemplo, selecionamos as falhas de pedido específico, rastreios e eventos. Também, selecionamos todas as exceções e falhas de dependência.

![Feed personalizado de em direto](./media/live-stream/live-stream-events.png)

Nota: Atualmente, para critérios com base na mensagem de exceção, utilize a mensagem de exceção mais externo. No exemplo anterior, para filtrar a exceção benigno com a mensagem de exceção interna (segue o "< –" delimitador) "o cliente desligado." Utilize uma mensagem de não-contém critérios de "Erro ao ler o conteúdo do pedido".

Ver os detalhes de um item no feed em direto ao clicar no mesmo. Pode colocar em pausa o feed clicando **colocar em pausa** ou simplesmente deslocar para baixo, ou clicar num item. Ao Live feed será retomado depois de se deslocar para voltar à parte superior, ou ao clicar o contador de itens recolhidos enquanto estava em pausa.

![Objeto de amostragem de falhas em direto](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrar por instância de servidor

Se pretender monitorizar uma instância de função de servidor em particular, pode filtrar por servidor.

![Objeto de amostragem de falhas em direto](./media/live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Requisitos do SDK
Stream personalizado de métricas em direto está disponível com a versão 2.4.0-beta2 ou mais recente de [SDK do Application Insights para a web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Lembre-se selecionar a opção de "Include Prerelease" do Gestor de pacotes NuGet.

## <a name="secure-the-control-channel"></a>Proteja o canal de controlo
Os critérios de filtros personalizados que especificar são enviados de volta para o componente de métricas em direto no Application Insights SDK. Os filtros poderiam potencialmente conter informações confidenciais, como customerIDs. Pode fazer o canal seguro com uma chave secreta de API, além da chave de instrumentação.
### <a name="create-an-api-key"></a>Criar uma chave de API

![Criar chave de API](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Adicionar chave de API à configuração

### <a name="classic-aspnet"></a>ASP.NET clássico

No ficheiro applicationinsights. config, adicione o AuthenticationApiKey para o QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Ou, no código, defini-lo a QuickPulseTelemetryModule:

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

### <a name="azure-function-apps"></a>Aplicações de funções do Azure

Para aplicações de funções do Azure (v2) proteger o canal com uma API chave pode ser feita com uma variável de ambiente. 

Criar uma chave de API a partir do seu recurso do Application Insights e aceda a **as definições da aplicação** para a sua aplicação de função. Selecione **Adicionar nova definição** e introduza um nome de `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` e um valor que corresponde à sua chave de API.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>ASP.NET Core (requer o ASP.NET Core SDK do Application Insights 2.3.0-beta ou superior)

Modifique o ficheiro de startup.cs da seguinte forma:

Adicionar primeiro

``` C#
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Em seguida, dentro do método ConfigureServices adicione:

``` C#
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

No entanto, se reconhece e confia em todos os servidores ligados, pode experimentar os filtros personalizados sem o canal autenticado. Esta opção está disponível durante seis meses. Esta substituição é necessária uma vez cada nova sessão ou quando um novo servidor fica online.

![Opções de autenticação de métricas em direto](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Recomendamos vivamente que configure o canal autenticado antes da inserção de informações potencialmente confidenciais, como CustomerID nos critérios de filtro.
>

## <a name="generating-a-performance-test-load"></a>Gerar uma carga de teste de desempenho

Se pretender ver o efeito de um aumento de carga, utilize o painel de teste de desempenho. Ela simula a pedidos de um número de usuários simultâneos. Ele pode executar qualquer um dos "testes manuais" (testes de ping) de uma única URL, ou pode ser executada uma [teste de desempenho na web de vários passos](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) que carrega (da mesma forma como um teste de disponibilidade).

> [!TIP]
> Depois de criar o teste de desempenho, abra o teste e o painel de Stream em direto no windows separadas. Pode ver quando é iniciado o teste de desempenho em fila de mensagens em fila e a transmissão em direto do watch ao mesmo tempo.
>


## <a name="troubleshooting"></a>Resolução de problemas

Não existem dados? Se seu aplicativo estiver numa rede protegida: Stream de métricas em direto utiliza um endereços IP diferentes do que de outra telemetria do Application Insights. Certifique-se [esses endereços IP](../../azure-monitor/app/ip-addresses.md) estão abertas na firewall.



## <a name="next-steps"></a>Passos Seguintes
* [Monitorizar a utilização com o Application Insights](../../azure-monitor/app/usage-overview.md)
* [Utilizar a pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md)
* [Gerador de perfis](../../azure-monitor/app/profiler.md)
* [Depurador de instantâneos](../../azure-monitor/app/snapshot-debugger.md)
