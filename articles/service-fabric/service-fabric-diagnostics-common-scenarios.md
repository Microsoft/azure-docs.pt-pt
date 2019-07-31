---
title: Cenários comuns do Azure Service Fabric diagnosticar | Microsoft Docs
description: Saiba como solucionar problemas de cenários comuns com o Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 265aea1b8873d812859b39175c732c3e7118cbb5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "60394256"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnosticar cenários comuns com o Service Fabric

Este artigo ilustra os cenários comuns que os usuários encontraram na área de monitoramento e diagnóstico com o Service Fabric. Os cenários apresentados abrangem todas as três camadas do Service Fabric: Aplicativo, cluster e infraestrutura. Cada solução usa logs de Application Insights e Azure Monitor, ferramentas de monitoramento do Azure, para concluir cada cenário. As etapas em cada solução fornecem aos usuários uma introdução sobre como usar os logs Application Insights e Azure Monitor no contexto de Service Fabric.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Pré-requisitos e recomendações

As soluções neste artigo usarão as ferramentas a seguir. Recomendamos que você os tenha configurado e configurados:

* [Application Insights com Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Habilitar Diagnóstico do Azure em seu cluster](service-fabric-diagnostics-event-aggregation-wad.md)
* [Configurar um espaço de trabalho Log Analytics](service-fabric-diagnostics-oms-setup.md)
* [Agente de Log Analytics para acompanhar contadores de desempenho](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Como posso ver exceções sem tratamento em meu aplicativo?

1. Navegue até o recurso de Application Insights ao qual seu aplicativo está configurado.
2. Clique em *Pesquisar* na parte superior esquerda. Em seguida, clique em filtrar no painel seguinte.

    ![Visão geral do ia](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Você verá muitos tipos de eventos (rastreamentos, solicitações, eventos personalizados). Escolha "exceção" como filtro.

    ![Lista de filtros de ia](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Ao clicar em uma exceção na lista, você poderá examinar mais detalhes, incluindo o contexto do serviço, se estiver usando o SDK do Application Insights de Service Fabric.

    ![Exceção de ia](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Como fazer exibir quais chamadas HTTP são usadas em meus serviços?

1. No mesmo Application Insights recurso, você pode filtrar "solicitações" em vez de exceções e exibir todas as solicitações feitas
2. Se você estiver usando o SDK do Application Insights Service Fabric, poderá ver uma representação visual de seus serviços conectados entre si e o número de solicitações bem-sucedidas e com falha. À esquerda, clique em "mapa do aplicativo"

    ![Mapa de aplicativos de](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ia da folha ![mapa do aplicativo ai](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Para obter mais informações sobre o mapa do aplicativo, visite a [documentação do mapa do aplicativo](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Como fazer criar um alerta quando um nó ficar inativo

1. Os eventos de nó são acompanhados pelo Cluster Service Fabric. Navegue até o Análise do Service Fabric recurso de solução chamado **NameofResourceGroup (infabric)**
2. Clique no grafo na parte inferior da folha intitulado "Summary"

    ![Solução de logs de Azure Monitor](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Aqui, você tem muitos grafos e blocos exibindo várias métricas. Clique em um dos grafos e ele será levado para a pesquisa de logs. Aqui você pode consultar quaisquer eventos de cluster ou contadores de desempenho.
4. Insira a consulta a seguir. Essas IDs de evento são encontradas na [referência de eventos de nó](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Clique em "nova regra de alerta" na parte superior e, agora, sempre que um evento chegar com base nessa consulta, você receberá um alerta no método de comunicação escolhido.

    ![Novo alerta dos logs do Azure Monitor](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Como posso ser alertado sobre reversões de atualização de aplicativos?

1. Na mesma janela de pesquisa de log que antes, insira a consulta a seguir para reversões de atualização. Essas IDs de evento são encontradas em [referência de eventos de aplicativo](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Clique em "nova regra de alerta" na parte superior e, agora, sempre que um evento chegar com base nessa consulta, você receberá um alerta.

## <a name="how-do-i-see-container-metrics"></a>Como fazer ver métricas de contêiner?

Na mesma exibição com todos os grafos, você verá alguns blocos para o desempenho de seus contêineres. Você precisa do agente de Log Analytics e da [solução de monitoramento](service-fabric-diagnostics-oms-containers.md) de contêineres para que esses blocos sejam preenchidos.

![Métricas de contêiner Log Analytics](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Para instrumentar a telemetria de **dentro** de seu contêiner, você precisará adicionar o [Application insights pacote NuGet para contêineres](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Como posso monitorar os contadores de desempenho?

1. Depois de adicionar o agente de Log Analytics ao cluster, você precisará adicionar os contadores de desempenho específicos que deseja acompanhar. Navegue até a página do espaço de trabalho Log Analytics no portal – na página da solução, a guia espaço de trabalho está no menu à esquerda.

    ![Guia Log Analytics espaço de trabalho](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Quando você estiver na página do espaço de trabalho, clique em "configurações avançadas" no mesmo menu à esquerda.

    ![Log Analytics configurações avançadas](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Clique em dados > contadores de desempenho do Windows (dados > contadores de desempenho do Linux para computadores Linux) para começar a coletar contadores específicos de seus nós por meio do agente de Log Analytics. Aqui estão exemplos do formato dos contadores a serem adicionados

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     No início rápido, VotingData e VotingWeb são os nomes de processo usados, portanto, controlar esses contadores seria parecido com

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Contadores de desempenho de Log Analytics](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Isso permitirá que você veja como sua infraestrutura está lidando com suas cargas de trabalho e defina alertas relevantes com base na utilização de recursos. Por exemplo – talvez você queira definir um alerta se a utilização total do processador ficar acima de 90% ou abaixo de 5%. O nome do contador que você usaria para isso é "% de tempo do processador". Você pode fazer isso criando uma regra de alerta para a seguinte consulta:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Como fazer acompanhar o desempenho de meus Reliable Services e atores?

Para acompanhar o desempenho de Reliable Services ou atores em seus aplicativos, você também deve coletar os contadores ator Service Fabric, método de ator, serviço e método de serviço. Aqui estão exemplos de contadores de desempenho de serviço confiável e ator a serem coletados

>[!NOTE]
>Service Fabric contadores de desempenho não podem ser coletados pelo agente do Log Analytics no momento, mas podem ser coletados por [outras soluções de diagnóstico](service-fabric-diagnostics-partners.md)

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Verifique esses links para obter a lista completa de contadores de desempenho em Reliable [Services](service-fabric-reliable-serviceremoting-diagnostics.md) and [atores](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Passos Seguintes

* [Configurar alertas no ia](../azure-monitor/app/alerts.md) para ser notificado sobre alterações no desempenho ou no uso
* A [detecção inteligente no Application insights](../azure-monitor/app/proactive-diagnostics.md) executa uma análise proativa da telemetria que está sendo enviada ao ia para avisá-lo sobre possíveis problemas de desempenho
* Saiba mais sobre [alertas](../log-analytics/log-analytics-alerts.md) de Azure monitor logs para auxiliar na detecção e no diagnóstico.
* Para clusters locais, Azure Monitor logs oferece um gateway (proxy de encaminhamento HTTP) que pode ser usado para enviar dados a logs de Azure Monitor. Leia mais sobre isso em [conectando computadores sem acesso à Internet para Azure monitor logs usando o gateway de log Analytics](../azure-monitor/platform/gateway.md)
* Familiarize-se com os recursos de [pesquisa de logs e consulta](../log-analytics/log-analytics-log-searches.md) oferecidos como parte dos logs de Azure monitor
* Obtenha uma visão geral mais detalhada dos logs de Azure Monitor e o que ele oferece, leia [o que são Azure monitor logs?](../operations-management-suite/operations-management-suite-overview.md)
