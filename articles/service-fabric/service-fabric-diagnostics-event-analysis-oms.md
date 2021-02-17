---
title: Análise de eventos de tecido de serviço Azure com registos do Monitor Azure
description: Saiba como visualizar e analisar eventos utilizando registos do Azure Monitor para monitorização e diagnóstico de clusters de tecidos de serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: 678e96c9b5611cd6d72abd7c4582691b55ffe479
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581550"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Análise e visualização de eventos com registos do Azure Monitor
 Os registos do Azure Monitor recolhem e analisam a telemetria a partir de aplicações e serviços alojados na nuvem e fornecem ferramentas de análise para o ajudar a maximizar a sua disponibilidade e desempenho. Este artigo descreve como executar consultas nos registos do Azure Monitor para obter insights e resolver problemas do que está a acontecer no seu cluster. As seguintes questões comuns são abordadas:

* Como resolvo os problemas de saúde?
* Como sei quando um nó cai?
* Como sei se os serviços da minha candidatura começaram ou pararam?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Visão geral do espaço de trabalho Log Analytics

>[!NOTE] 
>Enquanto o armazenamento de diagnóstico é ativado por padrão no tempo de criação do cluster, você ainda deve configurar o espaço de trabalho Log Analytics para ler a partir do armazenamento de diagnóstico.

Os registos do Azure Monitor recolhem dados de recursos geridos, incluindo uma mesa de armazenamento Azure ou um agente, e mantêm-nos num repositório central. Os dados podem então ser utilizados para análise, alerta e visualização, ou posterior exportação. Os registos do Azure Monitor suportam eventos, dados de desempenho ou quaisquer outros dados personalizados. Confira [as etapas para configurar a extensão de diagnóstico a eventos e passos agregados](service-fabric-diagnostics-event-aggregation-wad.md) [para criar um espaço de trabalho Log Analytics para ler dos eventos armazenados](service-fabric-diagnostics-oms-setup.md) para garantir que os dados estão fluindo para os registos do Azure Monitor.

Depois de os dados serem recebidos pelos registos do Azure Monitor, o Azure dispõe *de várias Soluções de Monitorização* que são soluções pré-embaladas ou dashboards operacionais para monitorizar os dados de entrada, personalizados para vários cenários. Estes incluem uma solução *de Service Fabric Analytics* e uma solução de *Contentores,* que são as duas mais relevantes para diagnósticos e monitorização ao utilizar clusters de Tecido de Serviço. Este artigo descreve como utilizar a solução Service Fabric Analytics, que é criada com o espaço de trabalho.

## <a name="access-the-service-fabric-analytics-solution"></a>Aceda à solução Service Fabric Analytics

No [Portal Azure,](https://portal.azure.com)vá ao grupo de recursos em que criou a solução Service Fabric Analytics.

Selecione o **serviço \<nameOfOMSWorkspace\> de recursosFabric**.

Em `Summary` , você verá azulejos sob a forma de um gráfico para cada uma das soluções ativadas, incluindo uma para Tecido de Serviço. Clique no gráfico **de Tecido de Serviço** para continuar na solução Service Fabric Analytics.

![Solução de Tecido de Serviço](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

A imagem a seguir mostra a página inicial da solução Service Fabric Analytics. Esta página inicial fornece uma visão instantânea do que está a acontecer no seu cluster.

![Screenshot que mostra a página inicial da solução Service Fabric Analytics.](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Se você ativou diagnósticos sobre a criação de cluster, você pode ver eventos para 

* [Eventos de cluster de tecido de serviço](service-fabric-diagnostics-event-generation-operational.md)
* [Eventos de modelos de programação de atores confiáveis](service-fabric-reliable-actors-diagnostics.md)
* [Eventos de modelos de programação de serviços fiáveis](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Além dos eventos de Tecido de Serviço fora da caixa, eventos de sistema mais detalhados podem ser recolhidos [atualizando a configuração da sua extensão de diagnóstico.](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Ver Eventos de Tecido de Serviço, incluindo ações em nos acenos

Na página Service Fabric Analytics, clique no gráfico para **Eventos de Tecido de Serviço.**

![Canal Operacional de Solução de Tecido de Serviço](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Clique em **Lista** para ver os eventos numa lista. Uma vez aqui, você verá todos os eventos do sistema que foram recolhidos. Para referência, estes são do **WADServiceFabricSystemEventsTable** na conta de Armazenamento Azure, e da mesma forma os eventos de serviços e atores fiáveis que você vê a seguir são dessas respetivas tabelas.
    
![Canal Operacional de Consulta](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Em alternativa, pode clicar na lupa à esquerda e usar a linguagem de consulta Kusto para encontrar o que procura. Por exemplo, para encontrar todas as ações tomadas em nós no cluster, você pode usar a seguinte consulta. Os IDs do evento utilizados abaixo encontram-se na [referência de eventos](service-fabric-diagnostics-event-generation-operational.md)de canal operacional .

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Pode consultar muitos mais campos, como os nós específicos (Computador) do serviço de sistema (TaskName).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Ver serviço de serviço reliable serviço e eventos de ator

Na página Service Fabric Analytics, clique no gráfico para **Serviços Fiáveis**.

![Serviços fiáveis de solução de tecido de serviço](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Clique em **Lista** para ver os eventos numa lista. Aqui pode ver os eventos dos serviços de confiança. Pode ver diferentes eventos para quando o runasync de serviço é iniciado e concluído, o que normalmente acontece em implementações e upgrades. 

![Serviços fiáveis de consulta](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Eventos de atores fiáveis podem ser vistos de forma semelhante. Para configurar eventos mais detalhados para atores fiáveis, é necessário alterar `scheduledTransferKeywordFilter` o config para a extensão de diagnóstico (mostrado abaixo). Os detalhes sobre os valores para estes estão na [referência de eventos de atores confiáveis.](service-fabric-reliable-actors-diagnostics.md#keywords)

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

A linguagem de consulta kusto é poderosa. Outra consulta valiosa que pode fazer é descobrir quais os nós que estão a gerar mais eventos. A consulta na imagem abaixo mostra eventos operacionais do Service Fabric agregados com o serviço e o nó específicos.

![Eventos de Consulta por Nó](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Passos seguintes

* Para permitir a monitorização da infraestrutura, ou seja, contadores de desempenho, dirija-se à [adição do agente Log Analytics](service-fabric-diagnostics-oms-agent.md). O agente recolhe contadores de desempenho e adiciona-os ao seu espaço de trabalho existente.
* Para clusters no local, os registos do Azure Monitor oferecem um Gateway (HTTP Forward Proxy) que pode ser usado para enviar dados para registos do Monitor Azure. Leia mais sobre isso em [computadores de ligação sem acesso à Internet aos registos do Monitor Azure utilizando o gateway Log Analytics](../azure-monitor/agents/gateway.md).
* Configure  [o alerta automatizado](../azure-monitor/alerts/alerts-overview.md) para ajudar na deteção e diagnóstico.
* Familiarize-se com as funcionalidades [de pesquisa e consulta](../azure-monitor/logs/log-query-overview.md) de registo oferecidas como parte dos registos do Azure Monitor.
* Obtenha uma visão geral mais detalhada dos registos do Azure Monitor e o que oferece, leia [O que são registos do Azure Monitor?](../azure-monitor/overview.md)
