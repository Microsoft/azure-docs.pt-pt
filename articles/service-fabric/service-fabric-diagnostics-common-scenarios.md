---
title: Tecido de serviço Azure diagnostica cenários comuns
description: Saiba mais sobre a resolução de problemas de cenários comuns de monitorização e diagnóstico dentro das aplicações do Azure Service Fabric.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 3083348948ce97ae9d99098896878d79081a15f0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580487"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnosticar cenários comuns com tecido de serviço

Este artigo ilustra cenários comuns que os utilizadores encontraram na área da monitorização e diagnóstico com o Service Fabric. Os cenários apresentados abrangem todas as 3 camadas de tecido de serviço: Aplicação, Cluster e Infraestrutura. Cada solução utiliza insights de aplicação e registos do Monitor Azure, ferramentas de monitorização Azure, para completar cada cenário. Os passos em cada solução dão aos utilizadores uma introdução sobre como utilizar os registos de Aplicações Insights e Azure Monitor no contexto do Tecido de Serviço.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Pré-requisitos e recomendações

As soluções deste artigo utilizarão as seguintes ferramentas. Recomendamos que tenha estas configurações e configuradas:

* [Insights de Aplicação com Tecido de Serviço](service-fabric-tutorial-monitoring-aspnet.md)
* [Ativar diagnósticos Azure no seu cluster](service-fabric-diagnostics-event-aggregation-wad.md)
* [Criar um espaço de trabalho Log Analytics](service-fabric-diagnostics-oms-setup.md)
* [Log Analytics agente para rastrear contadores de desempenho](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Como posso ver exceções não manipuladas na minha candidatura?

1. Navegue para o seu recurso Application Insights com o qual a sua aplicação está configurada.
2. Clique em *Procurar* no topo esquerdo. Em seguida, clique no filtro no painel seguinte.

    ![Visão geral da AI](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Você verá muitos tipos de eventos (traços, pedidos, eventos personalizados). Escolha "Exceção" como filtro.

    ![Lista de filtros de IA](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Ao clicar numa exceção na lista, pode olhar para mais detalhes, incluindo o contexto de serviço, se estiver a utilizar o Service Fabric Application Insights SDK.

    ![Exceção da AI](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Como vejo quais as chamadas HTTP utilizadas nos meus serviços?

1. No mesmo recurso Application Insights, pode filtrar em "pedidos" em vez de exceções e ver todos os pedidos feitos
2. Se estiver a utilizar o Service Fabric Application Insights SDK, pode ver uma representação visual dos seus serviços ligados entre si, e o número de pedidos bem sucedidos e falhados. À esquerda clique em "Mapa de Aplicações"

    ![Mapa de aplicativos AI Map ](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![ AI App Map](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Para mais informações sobre o mapa de aplicações, visite a documentação do Mapa de [Aplicações](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Como posso criar um alerta quando um nó cai

1. Os eventos de nó são rastreados pelo seu cluster de Tecido de Serviço. Navegue para o recurso de solução Service Fabric Analytics chamado **ServiceFabric (NameofResourceGroup)**
2. Clique no gráfico na parte inferior da lâmina intitulada "Resumo"

    ![Solução de registos Azure Monitor](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Aqui tem muitos gráficos e azulejos exibindo várias métricas. Clique num dos gráficos e irá levá-lo à Pesquisa de Registo. Aqui pode consultar quaisquer eventos de cluster ou contadores de desempenho.
4. Insira a seguinte consulta. Estes IDs do evento são encontrados na [referência de eventos](service-fabric-diagnostics-event-generation-operational.md#application-events) de nó

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Clique em "Nova Regra de Alerta" no topo e agora, sempre que um evento chegar com base nesta consulta, receberá um alerta no seu método de comunicação escolhido.

    ![Monitor Azure regista novo alerta](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Como posso ser alertado sobre os rollbacks de upgrade de aplicações?

1. Na mesma janela 'Registar' como antes de introduzir a seguinte consulta para atualizações de atualização. Estes IDs do evento são encontrados na [referência de eventos de aplicação](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Clique em "Nova Regra de Alerta" no topo e agora, sempre que um evento chegar com base nesta consulta, receberá um alerta.

## <a name="how-do-i-see-container-metrics"></a>Como vejo as métricas dos contentores?

Na mesma vista com todos os gráficos, você verá alguns azulejos para o desempenho dos seus recipientes. Precisa da solução de [monitorização](service-fabric-diagnostics-oms-containers.md) do Agente de Analítica de Log e do Contentor para que estes azulejos povoem.

![Métricas do recipiente de log analytics](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Para a telemetria de instrumentos a partir do **seu** recipiente, terá de adicionar o [pacote de nugets Application Insights para recipientes](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Como posso monitorizar os contadores de desempenho?

1. Uma vez adicionado o agente Log Analytics ao seu cluster, tem de adicionar os contadores de desempenho específicos que pretende rastrear. Navegue para a página do espaço de trabalho Log Analytics no portal – a partir da página da solução o separador espaço de trabalho está no menu esquerdo.

    ![Separador de espaço de trabalho do Log Analytics](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Uma vez na página do espaço de trabalho, clique em "Definições avançadas" no mesmo menu esquerdo.

    ![Registar Definições Avançadas de Analítica](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Clique em Data > Contadores de Desempenho do Windows (Data > Contadores de Desempenho Linux para máquinas Linux) para começar a recolher contadores específicos a partir dos seus nós através do agente Log Analytics. Aqui estão exemplos do formato para contadores a adicionar

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     No arranque rápido, VoteData e VoteWeb são os nomes de processo utilizados, por isso rastrear estes contadores seria como

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Contadores Perf De Registo Analytics](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Isto permitir-lhe-á ver como a sua infraestrutura está a lidar com as suas cargas de trabalho e definir alertas relevantes com base na utilização de recursos. Por exemplo – pode querer definir um alerta se a utilização total do Processador for superior a 90% ou abaixo de 5%. O nome do contador que usaria para isto é "% tempo de processador". Pode fazê-lo criando uma regra de alerta para a seguinte consulta:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Como faço o rastreio do desempenho dos meus Serviços E Atores Fidedigdos?

Para acompanhar o desempenho de Serviços Fiáveis ou Atores nas suas aplicações, deverá recolher também os contadores Service Fabric Ator, Ator Method, Service e Service Method. Aqui estão exemplos de contadores de desempenho de serviço e ator confiáveis para recolher

>[!NOTE]
>Os contadores de desempenho do Service Fabric não podem ser recolhidos pelo agente Log Analytics atualmente, mas podem ser recolhidos por [outras soluções de diagnóstico](service-fabric-diagnostics-partners.md)

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Consulte estes links para obter a lista completa de contadores de desempenho em [Serviços](service-fabric-reliable-serviceremoting-diagnostics.md) e [Atores](service-fabric-reliable-actors-diagnostics.md) Fidedigdos

## <a name="next-steps"></a>Passos seguintes

* [Procure erros de ativação de pacotes de código comum](./service-fabric-diagnostics-code-package-errors.md)
* [Configurar alertas em IA](../azure-monitor/alerts/alerts-log.md) para ser notificado sobre alterações no desempenho ou utilização
* [Smart Detection in Application Insights](../azure-monitor/app/proactive-diagnostics.md) realiza uma análise proativa da telemetria enviada para a IA para alertá-lo sobre potenciais problemas de desempenho
* Saiba mais sobre os registos do Azure Monitor [alertando](../azure-monitor/alerts/alerts-overview.md) para ajudar na deteção e diagnóstico.
* Para clusters no local, os registos do Azure Monitor oferecem um gateway (HTTP Forward Proxy) que pode ser usado para enviar dados para registos do Monitor Azure. Leia mais sobre isso em [computadores de ligação sem acesso à Internet aos registos do Monitor Azure utilizando o gateway Log Analytics](../azure-monitor/agents/gateway.md)
* Familiarize-se com as funcionalidades [de pesquisa e consulta](../azure-monitor/logs/log-query-overview.md) de registo oferecidas como parte dos registos do Azure Monitor
* Obtenha uma visão geral mais detalhada dos registos do Azure Monitor e o que oferece, leia [O que são registos do Azure Monitor?](../azure-monitor/overview.md)
