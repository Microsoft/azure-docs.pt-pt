---
title: Tecido de serviço azure diagnosticar cenários comuns
description: Saiba mais sobre a resolução de problemas de cenários comuns de monitorização e diagnóstico dentro das aplicações azure service fabric.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: bc17f00dc46c6e995d18621353c8f10cacf7759c
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697679"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnosticar cenários comuns com tecido de serviço

Este artigo ilustra cenários comuns que os utilizadores encontraram na área de monitorização e diagnóstico com o Tecido de Serviço. Os cenários apresentados abrangem todas as 3 camadas de tecido de serviço: Aplicação, Cluster e Infraestrutura. Cada solução utiliza os registos de Application Insights e Azure Monitor, ferramentas de monitorização Azure, para completar cada cenário. Os passos em cada solução dão aos utilizadores uma introdução sobre como utilizar os registos de Aplicação Insights e Azure Monitor no contexto do Tecido de Serviço.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Pré-requisitos e recomendações

As soluções deste artigo utilizarão as seguintes ferramentas. Recomendamos que tenha estes configurados e configurados:

* [Insights de aplicação com tecido de serviço](service-fabric-tutorial-monitoring-aspnet.md)
* [Ativar diagnósticos Azure no seu cluster](service-fabric-diagnostics-event-aggregation-wad.md)
* [Criar um espaço de trabalho log Analytics](service-fabric-diagnostics-oms-setup.md)
* [Log Analytics agente para rastrear contadores de desempenho](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Como posso ver exceções não tratadas na minha candidatura?

1. Navegue para o seu recurso Application Insights com o que a sua aplicação está configurada.
2. Clique em *Procurar* no topo à esquerda. Em seguida, clique em filtro no painel seguinte.

    ![Visão geral da IA](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Você verá muitos tipos de eventos (vestígios, pedidos, eventos personalizados). Escolha "Exceção" como filtro.

    ![Lista de filtros AI](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Ao clicar numa exceção na lista, pode ver mais detalhes, incluindo o contexto de serviço, se estiver a utilizar o SDK de Insights de Aplicação de Tecido de Serviço.

    ![Exceção AI](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Como vejo quais as chamadas http são usadas nos meus serviços?

1. No mesmo recurso Application Insights, pode filtrar "pedidos" em vez de exceções e ver todos os pedidos feitos
2. Se estiver a utilizar o SDK de Aplicação de Tecido de Serviço, pode ver uma representação visual dos seus serviços ligados uns aos outros, e o número de pedidos bem sucedidos e falhados. No clique à esquerda "Mapa de aplicações"

    ![Mapa da aplicação AI App Blade ](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![ Ai](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Para mais informações sobre o mapa de aplicações, visite a documentação do Mapa de [Aplicações](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Como eu crio um alerta quando um nó cai

1. Os eventos do nó são rastreados pelo seu cluster de Tecido de Serviço. Navegue para o recurso de solução Service Fabric Analytics chamado **ServiceFabric (NameofResourceGroup)**
2. Clique no gráfico na parte inferior da lâmina intitulada "Resumo"

    ![Solução de logs Azure Monitor](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Aqui tem muitos gráficos e azulejos que exibem várias métricas. Clique num dos gráficos e irá levá-lo à Pesquisa de Registo. Aqui pode consultar quaisquer eventos de cluster ou contadores de desempenho.
4. Introduza a seguinte consulta. Estes iDs de eventos são encontrados na [referência de eventos](service-fabric-diagnostics-event-generation-operational.md#application-events) do nó

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Clique em "Nova Regra de Alerta" no topo e agora que um evento chegar com base nesta consulta, receberá um alerta no seu método de comunicação escolhido.

    ![Monitor Azure regista novo alerta](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Como posso ser alertado para a atualização da aplicação?

1. Na mesma janela de Pesquisa de Registo supérrio antes de introduzir a seguinte consulta para atualizações. Estes IDs de eventos são encontrados sob referência de eventos de [aplicação](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Clique em "Nova Regra de Alerta" no topo e agora que um evento chegar com base nesta consulta, receberá um alerta.

## <a name="how-do-i-see-container-metrics"></a>Como vejo métricas de contentores?

Na mesma vista com todos os gráficos, você verá alguns azulejos para o desempenho dos seus recipientes. Precisa da solução de Monitorização do Agente de Análise de Log e [de Contentores](service-fabric-diagnostics-oms-containers.md) para que estes azulejos possam povoar.

![Métricas de recipientes de análise de log analytics](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Para instrumentar a telemetria a partir do **seu** recipiente, terá de adicionar o [pacote de nuget Application Insights para recipientes](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Como posso monitorizar os contadores de desempenho?

1. Depois de ter adicionado o agente Log Analytics ao seu cluster, tem de adicionar os contadores de desempenho específicos que pretende rastrear. Navegue para a página do espaço de trabalho Log Analytics no portal – a partir da página da solução o separador espaço de trabalho está no menu esquerdo.

    ![Guia de espaço de trabalho de análise de log analytics](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Uma vez na página do espaço de trabalho, clique em "Definições avançadas" no mesmo menu esquerdo.

    ![Definições avançadas de análise de log analytics](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Clique em Data > Windows Performance Counters (Data > Linux Performance Counters for Linux machines) para começar a recolher contadores específicos dos seus nós através do agente Log Analytics. Aqui estão exemplos do formato para contadores adicionar

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     No arranque rápido, O VotingData e o VotingWeb são os nomes de processo utilizados, pelo que rastrear estes contadores seria como

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Contadores Perf de Log Analytics](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Isto permitir-lhe-á ver como a sua infraestrutura está a lidar com as suas cargas de trabalho e definir alertas relevantes com base na utilização de recursos. Por exemplo – pode querer estabelecer um alerta se a utilização total do Processador for superior a 90% ou abaixo de 5%. O contranome que usaria para isto é "% tempo de processador". Poderia fazê-lo criando uma regra de alerta para a seguinte consulta:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Como acompanho o desempenho dos meus Serviços fiáveis e atores?

Para acompanhar o desempenho de Serviços Fiáveis ou Atores nas suas aplicações, deve recolher também os balcões de Service Fabric Ator, Ator Method, Service e Service Method. Aqui estão exemplos de contadores de desempenho de serviço confiável e de ator para recolher

>[!NOTE]
>Os contadores de desempenho do Tecido de Serviço não podem ser recolhidos pelo agente Log Analytics atualmente, mas podem ser recolhidos por [outras soluções](service-fabric-diagnostics-partners.md) de diagnóstico

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Consulte estes links para ver a lista completa de contadores de desempenho em [Serviços](service-fabric-reliable-serviceremoting-diagnostics.md) fiáveis e [atores](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Passos seguintes

* [Procure erros de ativação do pacote de código comum](./service-fabric-diagnostics-code-package-errors.md)
* [Configurar alertas em IA](/azure/azure-monitor/platform/alerts-log) para serem notificados sobre alterações no desempenho ou utilização
* [Smart Detection in Application Insights](../azure-monitor/app/proactive-diagnostics.md) realiza uma análise proativa da telemetria que está a ser enviada para a IA para o alertar para potenciais problemas de desempenho
* Saiba mais sobre os registos do Monitor Azure [alertando](../log-analytics/log-analytics-alerts.md) para ajuda na deteção e diagnóstico.
* Para os clusters no local, os registos do Monitor Azure oferecem uma porta de entrada (HTTP Forward Proxy) que pode ser usada para enviar dados para registos do Monitor Azure. Leia mais sobre isso em [Computadores de Ligação sem acesso à Internet aos registos do Monitor Azure utilizando o gateway Log Analytics](../azure-monitor/platform/gateway.md)
* Familiarize-se com as funcionalidades de [pesquisa de registos e consulta](../log-analytics/log-analytics-log-searches.md) oferecidas como parte dos registos do Monitor Azure
* Obtenha uma visão mais detalhada dos registos do Monitor Azure e o que oferece, leia o que é os [registos do Monitor Azure?](../operations-management-suite/operations-management-suite-overview.md)
