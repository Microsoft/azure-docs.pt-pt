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
ms.date: 05/16/2018
ms.author: srrengar
ms.openlocfilehash: 146ea6431516f7b78d23cd4b36aff43110639e83
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633100"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnosticar cenários comuns com o Service Fabric

Este artigo ilustra cenários comuns que os utilizadores tem encontrado na área de monitorização e diagnóstico com o Service Fabric. Os cenários apresentados abrangem todas as 3 camadas do service fabric: aplicação, o Cluster e infraestrutura. Cada solução utiliza o Application Insights e Log Analytics, ferramentas de monitorização do Azure, para concluir cada cenário. Os passos em cada solução de dar aos utilizadores uma introdução sobre como utilizar o Application Insights e o Log Analytics no contexto do Service Fabric.

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

    Para obter mais informações sobre o mapa da aplicação, visite o [documentação de mapa da aplicação](../application-insights/app-insights-app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Como criar um alerta quando um nó ficar inativo

1. Eventos de nó são controlados pelo cluster do Service Fabric. Navegue para o recurso de solução de análise do Service Fabric com o nome **ServiceFabric(NameofResourceGroup)**
2. Clique no gráfico na parte inferior do painel intitulada "Resumo"

    ![Solução de análise de registo](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Aqui tem muitos mosaicos exibindo a várias métricas e gráficos. Clique dos gráficos e será direcionado para a pesquisa de registos. Aqui pode consultar quaisquer contadores de desempenho ou eventos de cluster.
4. Introduza a seguinte consulta. Estes IDs de evento encontram-se no [referência de eventos do nó](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId >= 25623 or EventId <= 25626
    ```

5. Clique em "Nova regra de alerta" na parte superior e agora sempre que um evento chegar com base nesta consulta, receberá um alerta no seu método escolhido de comunicação.

    ![Novo alerta do log Analytics](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Como pode posso ser alertado reversões de atualização de aplicação?

1. Na janela de pesquisa de registos mesmo como antes, introduza a seguinte consulta para reversões de atualização. Estes IDs de evento estão localizados sob [referência de eventos de aplicações](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId == 29623 or EventId == 29624
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
    * `Service Fabric Service(*)\\Average milliseconds per request`

    Guia de introdução, VotingData e VotingWeb são os nomes de processo utilizados, para que estes contadores de controlo teria o seguinte aspeto

    * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
    * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

    ![Contadores de desempenho do log Analytics](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Isto permite-lhe ver a forma como a sua infraestrutura é processar cargas de trabalho e definir os alertas relevantes com base na utilização de recursos. Por exemplo, pode querer definir um alerta se a utilização do processador total for superior a 90% ou inferior a 5%. O nome do contador que utilizaria para isso é "% de tempo do processador". Pode fazer isso criando uma regra de alerta para a seguinte consulta:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Como controlar o desempenho da minha Reliable Services e Atores?

Para controlar o desempenho dos Reliable Services ou Atores em seus aplicativos, deve adicionar os contadores de Ator do Service Fabric, o método de Ator, o serviço e o método de serviço também. Pode adicionar estes contadores de maneira semelhante como o cenário acima, eis exemplos de reliable service ator contadores de desempenho e para adicionar no Log Analytics:

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Verifique estas ligações para obter a lista completa dos contadores de desempenho no Reliable [serviços](service-fabric-reliable-serviceremoting-diagnostics.md) e [Atores](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Passos Seguintes

* [Configure alertas em IA](../application-insights/app-insights-alerts.md) para ser notificado sobre as alterações no desempenho ou na utilização
* [Deteção no Application Insights inteligente](../application-insights/app-insights-proactive-diagnostics.md) realiza uma análise proativa de telemetria a ser enviada para IA para o avisar de potenciais problemas de desempenho
* Saiba mais sobre o Log Analytics [alertas](../log-analytics/log-analytics-alerts.md) para ajudar no diagnóstico e de deteção.
* Para os clusters no local, o Log Analytics oferece um gateway (reencaminhar Proxy HTTP) que pode ser utilizado para enviar dados para o Log Analytics. Leia mais sobre isso em [ligar os computadores sem acesso à Internet ao Log Analytics com o gateway do Log Analytics](../azure-monitor/platform/gateway.md)
* Familiarizar-se com o [pesquisas e consultas de registo](../log-analytics/log-analytics-log-searches.md) funcionalidades oferecidos como parte do Log Analytics
* Obtenha uma visão geral mais detalhada do Log Analytics e o que ele oferece, leia [o que é o Log Analytics?](../operations-management-suite/operations-management-suite-overview.md)
