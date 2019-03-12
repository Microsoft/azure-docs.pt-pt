---
title: Cenários comuns de diagnóstico do Azure Service Fabric | Documentos da Microsoft
description: Aprenda a resolver alguns cenários comuns com o Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8d6d2d83e24b5e42af5f4322021eebfe9bf09494
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551300"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnosticar cenários comuns com o Service Fabric

Este artigo ilustra cenários comuns que os utilizadores tem encontrado na área de monitorização e diagnóstico com o Service Fabric. Os cenários apresentados abrangem todas as 3 camadas do service fabric: Aplicativo, Cluster e infraestrutura. Cada solução utiliza o Application Insights e registos do Azure Monitor, ferramentas, de monitorização do Azure para concluir cada cenário. Os passos em cada solução de fornecem aos usuários uma introdução sobre como utilizar o Application Insights e registos do Azure Monitor no contexto do Service Fabric.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Pré-requisitos e recomendações

As soluções neste artigo, irão utilizar as seguintes ferramentas. Recomendamos que tenha este conjunto de cópias de segurança e configurado:

* [O Application Insights com o Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Ativar o diagnóstico do Azure no seu cluster](service-fabric-diagnostics-event-aggregation-wad.md)
* [Configurar uma área de trabalho do Log Analytics](service-fabric-diagnostics-oms-setup.md)
* [Iniciar o agente de análise para controlar os contadores de desempenho](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Como posso ver exceções sem tratamento no meu aplicativo?

1. Navegue para o recurso do Application Insights que seu aplicativo está configurado com.
2. Clique em *pesquisa* no canto superior esquerdo. Em seguida, clique em filtro no painel seguinte.

    ![Descrição geral de IA](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Verá muitos tipos de eventos (rastreios, pedidos, eventos personalizados). Escolha "Exception" como o filtro.

    ![Lista de filtros de IA](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Ao clicar numa exceção na lista, pode ver mais detalhes, incluindo o contexto de serviço se estiver a utilizar o SDK do Service Fabric para o Application Insights.

    ![Exceção de IA](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Como ver qual HTTP chamadas são usadas em meus serviços?

1. O mesmo recurso do Application Insights, pode filtrar os "pedidos" em vez de exceções e ver todos os pedidos efetuados
2. Se estiver a utilizar o SDK do Service Fabric para o Application Insights, pode ver uma representação visual dos seus serviços ligados entre si e o número de foi concluída com êxito e pedidos falhados. No lado esquerdo, clique em "Mapa da aplicação"

    ![Painel de mapa da aplicação de IA](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![mapa da aplicação de IA](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Para obter mais informações sobre o mapa da aplicação, visite o [documentação de mapa da aplicação](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Como criar um alerta quando um nó ficar inativo

1. Eventos de nó são controlados pelo cluster do Service Fabric. Navegue para o recurso de solução de análise do Service Fabric com o nome **ServiceFabric(NameofResourceGroup)**
2. Clique no gráfico na parte inferior do painel intitulada "Resumo"

    ![Solução de registos de Monitor do Azure](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Aqui tem muitos mosaicos exibindo a várias métricas e gráficos. Clique dos gráficos e será direcionado para a pesquisa de registos. Aqui pode consultar quaisquer contadores de desempenho ou eventos de cluster.
4. Introduza a seguinte consulta. Estes IDs de evento encontram-se no [referência de eventos do nó](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Clique em "Nova regra de alerta" na parte superior e agora sempre que um evento chegar com base nesta consulta, receberá um alerta no seu método escolhido de comunicação.

    ![Monitor do Azure regista novo alerta](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Como pode posso ser alertado reversões de atualização de aplicação?

1. Na janela de pesquisa de registos mesmo como antes, introduza a seguinte consulta para reversões de atualização. Estes IDs de evento estão localizados sob [referência de eventos de aplicações](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Clique em "Nova regra de alerta" na parte superior e agora sempre que um evento chegar com base nesta consulta, receberá um alerta.

## <a name="how-do-i-see-container-metrics"></a>Como posso ver métricas de contentor?

Na vista do mesmo com todos os gráficos, verá alguns mosaicos para o desempenho dos seus contentores. Terá do agente do Log Analytics e [solução de monitorização de contentores](service-fabric-diagnostics-oms-containers.md) para esses blocos preencher.

![Métricas de contentor do log Analytics](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>A telemetria de instrumento dos **dentro** seu contentor terá de adicionar o [pacote nuget do Application Insights para contentores](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Como pode monitorizar os contadores de desempenho?

1. Depois de adicionar o agente Log Analytics ao seu cluster, terá de adicionar os contadores de desempenho específicos que pretende controlar. Navegue até à página da área de trabalho do Log Analytics no portal – da página da solução, que o separador de área de trabalho é no menu da esquerda.

    ![Separador de área de trabalho do log Analytics](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Assim que estiver na página da área de trabalho, clique em "Definições avançadas" no menu esquerdo do mesmo.

    ![Definições avançadas do log Analytics](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Clique em dados > contadores de desempenho do Windows (dados > contadores de desempenho do Linux para máquinas do Linux) para iniciar a recolha de contadores específicos de seus nós por meio do agente do Log Analytics. Seguem-se exemplos de formato para contadores adicionar

    * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
    * `Processor(_Total)\\% Processor Time`

    Guia de introdução, VotingData e VotingWeb são os nomes de processo utilizados, para que estes contadores de controlo teria o seguinte aspeto

    * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
    * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

    ![Contadores de desempenho do log Analytics](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Isto permite-lhe ver a forma como a sua infraestrutura é processar cargas de trabalho e definir os alertas relevantes com base na utilização de recursos. Por exemplo, pode querer definir um alerta se a utilização do processador total for superior a 90% ou inferior a 5%. O nome do contador que utilizaria para isso é "% de tempo do processador". Pode fazer isso criando uma regra de alerta para a seguinte consulta:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Como controlar o desempenho da minha Reliable Services e Atores?

Para controlar o desempenho dos Reliable Services ou Atores em seus aplicativos, deve recolher os contadores de Ator do Service Fabric, o método de Ator, o serviço e o método de serviço também. Seguem-se exemplos de reliable service ator contadores de desempenho e para recolher

>[!NOTE]
>Contadores de desempenho do Service Fabric não podem ser recolhidas pelo agente do Log Analytics atualmente, mas podem ser coletados por [outras soluções de diagnóstico](service-fabric-diagnostics-partners.md)

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Verifique estas ligações para obter a lista completa dos contadores de desempenho no Reliable [serviços](service-fabric-reliable-serviceremoting-diagnostics.md) e [Atores](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Passos Seguintes

* [Configure alertas em IA](../azure-monitor/app/alerts.md) para ser notificado sobre as alterações no desempenho ou na utilização
* [Deteção no Application Insights inteligente](../azure-monitor/app/proactive-diagnostics.md) realiza uma análise proativa de telemetria a ser enviada para IA para o avisar de potenciais problemas de desempenho
* Saiba mais sobre os registos do Azure Monitor [alertas](../log-analytics/log-analytics-alerts.md) para ajudar no diagnóstico e de deteção.
* Para os clusters no local, registos do Azure Monitor oferece um gateway (reencaminhar Proxy HTTP) que pode ser utilizado para enviar dados para os registos do Azure Monitor. Leia mais sobre isso em [ligar computadores sem acesso à Internet para os registos de Monitor do Azure com o gateway do Log Analytics](../azure-monitor/platform/gateway.md)
* Familiarizar-se com o [pesquisas e consultas de registo](../log-analytics/log-analytics-log-searches.md) funcionalidades oferecidos como parte dos registos do Azure Monitor
* Obtenha uma visão geral mais detalhada de registos do Azure Monitor e o que ele oferece, leia [o que é o Azure Monitor registos?](../operations-management-suite/operations-management-suite-overview.md)
