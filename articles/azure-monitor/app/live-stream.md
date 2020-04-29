---
title: Diagnóstico com Fluxo de Métricas Ao Vivo - Insights de Aplicação Azure
description: Monitorize a sua aplicação web em tempo real com métricas personalizadas e diagnostice problemas com um feed ao vivo de falhas, vestígios e eventos.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: ea0d786d0b8b96941d791bcc8e92fad9a869c5f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670105"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: Monitor & Diagnosticar com latência de 1 segundo

Sondar o coração pulsante da sua aplicação web ao vivo e em produção utilizando live metrics stream from [Application Insights](../../azure-monitor/app/app-insights-overview.md). Selecione métricas e contadores de desempenho para assistir em tempo real, sem qualquer perturbação no seu serviço. Inspecione os vestígios da pilha a partir de pedidos e exceções falhados da amostra. Juntamente com [profiler,](../../azure-monitor/app/profiler.md) [snapshot debugger](../../azure-monitor/app/snapshot-debugger.md). Live Metrics Stream fornece uma ferramenta de diagnóstico poderosa e não invasiva para o seu web site ao vivo.

Com live Metrics Stream, você pode:

* Valide uma correção enquanto é lançada, assistindo a performance e falha conta.
* Observe o efeito das cargas de teste e diagnostice problemas ao vivo.
* Concentre-se em sessões de teste específicas ou filtre problemas conhecidos, selecionando e filtrando as métricas que pretende ver.
* Obtenha vestígios de exceção à medida que acontecem.
* Experimente filtros para encontrar os KP's mais relevantes.
* Monitorize qualquer contador de desempenho do Windows ao vivo.
* Identifique facilmente um servidor que esteja com problemas e filtre todos os feeds KPI/live para apenas esse servidor.

[![Vídeo live Metrics Stream](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

As Métricas Ao Vivo são atualmente suportadas para aplicações ASP.NET, ASP.NET Core, Azure Functions, Java e Node.js.

## <a name="get-started"></a>Introdução

1. Se ainda não [instalou O Insp.](../../azure-monitor/azure-monitor-app-hub.yml)
2. Além dos pacotes padrão de Insights de aplicação [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) é necessário para ativar o stream de Métricas Ao Vivo.
3. **Atualize a versão mais recente** do pacote Application Insights. No Estúdio Visual, clique no seu projeto e escolha **pacotes Manage Nuget**. Abra o separador **Atualizações** e selecione todos os pacotes Microsoft.ApplicationInsights.*

    Volte a implementar a aplicação.

3. No [portal Azure,](https://portal.azure.com)abra o recurso Application Insights para a sua aplicação e, em seguida, abra o Live Stream.

4. [Proteja o canal de controlo](#secure-the-control-channel) se puder utilizar dados sensíveis, como nomes de clientes nos seus filtros.

### <a name="no-data-check-your-server-firewall"></a>Não existem dados? Verifique a firewall do seu servidor

Verifique se as portas de saída para Live [Metrics Stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) estão abertas na firewall dos seus servidores.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Como é que o Live Metrics Stream difere do Metrics Explorer e do Analytics?

| |Live Stream | Explorador de Métricas e Analytics |
|---|---|---|
|Latência|Dados apresentados num segundo|Agregado ao longo de minutos|
|Sem retenção|Os dados persistem enquanto estão na tabela, e depois são descartados|[Dados retidos durante 90 dias](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|A pedido|Os dados são transmitidos enquanto abre métricas ao vivo|Os dados são enviados sempre que o SDK é instalado e ativado|
|Gratuito|Não há nenhum custo para os dados do Live Stream|Sujeito a [preços](../../azure-monitor/app/pricing.md)
|Amostragem|Todas as métricas e contadores selecionados são transmitidos. Falhas e vestígios de pilhas são amostrados. Os Processadores de Telemetria não são aplicados.|Os eventos podem ser [amostrados](../../azure-monitor/app/api-filtering-sampling.md)|
|Canal de controlo|Os sinais de controlo do filtro são enviados para o SDK. Recomendamos que proteja este canal.|A comunicação é uma maneira, para o portal|

## <a name="select-and-filter-your-metrics"></a>Selecione e filtre as suas métricas

(Disponível com funções ASP.NET, ASP.NET Core e Azure (v2).)

Pode monitorizar o KPI personalizado ao vivo aplicando filtros arbitrários em qualquer telemetria de Application Insights a partir do portal. Clique no controlo do filtro que mostra quando estiver a passar por qualquer um dos gráficos. O gráfico seguinte está a traçar uma contagem de pedidos personalizada kPI com filtros nos atributos URL e Duração. Valide os seus filtros com a secção de pré-visualização de fluxo que mostre um feed ao vivo de telemetria que corresponda aos critérios que especificou em qualquer momento.

![Pedido personalizado KPI](./media/live-stream/live-stream-filteredMetric.png)

Pode monitorizar um valor diferente do Conde. As opções dependem do tipo de fluxo, que pode ser qualquer telemetria de Insights de Aplicação: pedidos, dependências, exceções, vestígios, eventos ou métricas. Pode ser a sua própria [medição personalizada:](../../azure-monitor/app/api-custom-events-metrics.md#properties)

![Opções de Valor](./media/live-stream/live-stream-valueoptions.png)

Além da telemetria Application Insights, também pode monitorizar qualquer contador de desempenho do Windows selecionando-o a partir das opções de streaming e fornecendo o nome do contador de desempenho.

As métricas ao vivo são agregadas em dois pontos: localmente em cada servidor, e depois em todos os servidores. Pode alterar o padrão em qualquer uma das opções, selecionando outras opções nas respetivas descidas.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Telemetria de amostra: Eventos personalizados de diagnóstico ao vivo
Por padrão, a transmissão em direto dos eventos mostra amostras de pedidos falhados e chamadas de dependência, exceções, eventos e vestígios. Clique no ícone do filtro para ver os critérios aplicados a qualquer momento. 

![Alimentação ao vivo padrão](./media/live-stream/live-stream-eventsdefault.png)

Tal como acontece com as métricas, pode especificar quaisquer critérios arbitrários a qualquer um dos tipos de telemetria de Insights de Aplicação. Neste exemplo, estamos a selecionar falhas de pedidos específicos, vestígios e eventos. Estamos também a selecionar todas as exceções e falhas de dependência.

![Feed personalizado ao vivo](./media/live-stream/live-stream-events.png)

Nota: Atualmente, para critérios baseados em mensagens exceção, utilize a mensagem de exceção ultraperiférica. No exemplo anterior, filtrar a exceção benigna com mensagem de exceção interior (segue o delimitador "<--") "O cliente desligado". utilizar uma mensagem que não contenha critérios de "conteúdo de pedido de leitura de erros".

Consulte os detalhes de um item no feed ao vivo clicando nele. Pode interromper o feed clicando em **Pausa** ou simplesmente deslocando-se para baixo, ou clicando num item. O feed ao vivo será retomado depois de rolar de volta para o topo, ou clicando no balcão dos itens recolhidos durante a sua pausa.

![Falhas ao vivo experimentadas](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrar por exemplo do servidor

Se pretender monitorizar uma determinada instância de função do servidor, pode filtrar pelo servidor.

![Falhas ao vivo experimentadas](./media/live-stream/live-stream-filter.png)

## <a name="secure-the-control-channel"></a>Proteja o canal de controlo
Os critérios de filtro personalizados que especifica são enviados de volta para o componente Métricas Vivas no SDK de Insights de Aplicação. Os filtros podem potencialmente conter informações sensíveis, tais como iDs de clientes. Pode tornar o canal seguro com uma chave API secreta, além da chave de instrumentação.
### <a name="create-an-api-key"></a>Criar uma chave API

![Criar a chave API](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Adicionar chave API à Configuração

### <a name="classic-aspnet"></a>ASP.NET clássico

No ficheiro applicationinsights.config, adicione a AutenticaçãoApiKey ao QuickPulseTelemettryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Ou em código, detetete-o no Módulo QuickPulseTelemettry:

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

Para aplicações de função Azure (v2), a fixação do canal com uma chave API pode ser realizada com uma variável ambiental.

Crie uma chave API a partir do seu recurso Desinformação de Aplicação e vá para **as Definições** de Aplicação para a sua App de Funções. **Selecione adicionar nova definição** e introduzir um nome `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` e um valor que corresponda à sua chave API.

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

No entanto, se reconhecer e confiar em todos os servidores conectados, pode experimentar os filtros personalizados sem o canal autenticado. Esta opção está disponível por seis meses. Esta sobreposição é necessária uma vez em cada nova sessão, ou quando um novo servidor fica online.

![Opções Auth Métricas Ao Vivo](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Recomendamos vivamente que instale o canal autenticado antes de introduzir informações potencialmente sensíveis, como o CustomerID, nos critérios do filtro.
>

## <a name="supported-features-table"></a>Tabela de características suportadas

| Idioma                         | Métricas Básicas       | Métricas de desempenho | Filtragem personalizada    | Telemetria da amostra    | CPU dividido por processo |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET                             | Suportado (V2.7.2+) | Suportado (V2.7.2+) | Suportado (V2.7.2+) | Suportado (V2.7.2+) | Suportado (V2.7.2+)  |
| Núcleo .NET (target=.NET Framework)| Suportado (V2.4.1+) | Suportado (V2.4.1+) | Suportado (V2.4.1+) | Suportado (V2.4.1+) | Suportado (V2.4.1+)  |
| Núcleo .NET (target=.NET Core)     | Suportado (V2.4.1+) | Suportado*          | Suportado (V2.4.1+) | Suportado (V2.4.1+) | **Não Suportado**    |
| Funções Azure v2               | Suportado           | Suportado           | Suportado           | Suportado           | **Não Suportado**    |
| Java                             | Suportado (V2.0.0+) | Suportado (V2.0.0+) | **Não Suportado**   | **Não Suportado**   | **Não Suportado**    |
| Node.js                          | Suportado (V1.3.0+) | Suportado (V1.3.0+) | **Não Suportado**   | Suportado (V1.3.0+) | **Não Suportado**    |

As métricas básicas incluem pedido, dependência e taxa de exceção. As métricas de desempenho (contadores de desempenho) incluem memória e CPU. A telemetria da amostra mostra um fluxo de informações detalhadas para pedidos e dependências falhados, exceções, eventos e vestígios.

 \*O suporte perfCounters varia ligeiramente entre versões de .NET Core que não visam o .NET Framework:

- As métricas PerfCounters são suportadas quando estão a funcionar no Serviço de Aplicações Azure para Windows. (AspNetCore SDK Versão 2.4.1 ou superior)
- Os PerfCounters são suportados quando a aplicação está a funcionar em quaisquer máquinas Windows (VM ou Cloud Service ou On-prem etc.) (AspNetCore SDK Versão 2.7.1 ou superior), mas para aplicações direcionadas para .NET Core 2.0 ou superior.
- Os PerfCounters são suportados quando a aplicação está em execução EM QUALQUER LUGAR (Linux, Windows, serviço de aplicações para Linux, contentores, etc.) na mais recente versão beta (i.e. AspNetCore SDK Versão 2.8.0-beta1 ou superior), mas para aplicações direcionadas para .NET Core 2.0 ou superior.

Por padrão, as Métricas Ao Vivo são desativadas no Nó.js SDK. Para ativar métricas `setSendLiveMetrics(true)` ao vivo, adicione aos seus métodos de [configuração](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) à medida que rubrica o SDK.

## <a name="troubleshooting"></a>Resolução de problemas

Não existem dados? Se a sua aplicação estiver numa rede protegida: Live Metrics Stream utiliza diferentes endereços IP do que outras telemetria sinuosas de aplicação. Certifique-se de que [os endereços IP](../../azure-monitor/app/ip-addresses.md) estão abertos na sua firewall.

## <a name="next-steps"></a>Passos seguintes
* [Monitorização do uso com Insights de Aplicação](../../azure-monitor/app/usage-overview.md)
* [Usando pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md)
* [Gerador de perfis](../../azure-monitor/app/profiler.md)
* [Debugger instantâneo](../../azure-monitor/app/snapshot-debugger.md)
