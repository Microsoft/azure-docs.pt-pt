---
title: Análise de eventos de tecido de serviço Azure com registos do Monitor Azure
description: Saiba visualizar e analisar eventos utilizando registos do Monitor Azure para monitorização e diagnóstico de clusters de tecido de serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: 40dd930aa21e3056d5ecc908359215d6874ed8ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75464732"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Análise e visualização de eventos com registos do Monitor Azure
 Os registos do Azure Monitor recolhem e analisam a telemetria a partir de aplicações e serviços hospedados na nuvem e fornecem ferramentas de análise para ajudá-lo a maximizar a sua disponibilidade e desempenho. Este artigo descreve como executar consultas em registos do Monitor Azure para obter insights e resolver o que está a acontecer no seu cluster. São abordadas as seguintes questões comuns:

* Como posso resolver os eventos de saúde?
* Como sei quando um nó cai?
* Como sei se os serviços da minha candidatura começaram ou pararam?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Visão geral do espaço de trabalho log Analytics

>[!NOTE] 
>Enquanto o armazenamento de diagnóstico é ativado por padrão no tempo de criação do cluster, ainda deve configurar o espaço de trabalho log Analytics para ler a partir do armazenamento de diagnóstico.

Os registos do Azure Monitor recolhem dados de recursos geridos, incluindo uma mesa de armazenamento Azure ou um agente, e mantém-nos num repositório central. Os dados podem então ser utilizados para análise, alerta e visualização, ou exportação adicional. O Registo do Monitor Azure suporta eventos, dados de desempenho ou quaisquer outros dados personalizados. Verifique [os passos para configurar a extensão de diagnóstico para agregar eventos](service-fabric-diagnostics-event-aggregation-wad.md) e [passos para criar um espaço de trabalho log Analytics para ler a partir dos eventos armazenados](service-fabric-diagnostics-oms-setup.md) para garantir que os dados estão fluindo para os registos do Monitor Azure.

Depois de os dados serem recebidos pelos registos do Azure Monitor, o Azure dispõe de *várias Soluções de Monitorização* que são soluções pré-embaladas ou dashboards operacionais para monitorizar os dados recebidos, personalizados para vários cenários. Estes incluem uma solução De Análise de *Tecido de Serviço* e uma solução de *Contentores,* que são as duas mais relevantes para diagnósticos e monitorização ao utilizar clusters de Tecido de Serviço. Este artigo descreve como usar a solução Service Fabric Analytics, que é criada com o espaço de trabalho.

## <a name="access-the-service-fabric-analytics-solution"></a>Aceda à solução Análise de Tecido de Serviço

No [Portal Azure,](https://portal.azure.com)vá ao grupo de recursos em que criou a solução Service Fabric Analytics.

Selecione o **nome serviceFabricOfOMSWorkspace\<\>**.

Em, `Summary`você verá azulejos na forma de um gráfico para cada uma das soluções ativadas, incluindo uma para tecido de serviço. Clique no gráfico **de Tecido de Serviço** para continuar na solução Service Fabric Analytics.

![Solução de tecido de serviço](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

A imagem seguinte mostra a página inicial da solução Service Fabric Analytics. Esta página inicial fornece uma visão instantânea do que está a acontecer no seu cluster.

![Solução de tecido de serviço](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Se você permitiu diagnósticos sobre criação de cluster, você pode ver eventos para 

* [Eventos de cluster de tecido de serviço](service-fabric-diagnostics-event-generation-operational.md)
* [Eventos de modelos de programação de atores fiáveis](service-fabric-reliable-actors-diagnostics.md)
* [Eventos de modelos de programação de serviços fiáveis](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Além dos eventos de Tecido de Serviço fora da caixa, eventos de sistema mais detalhados podem ser recolhidos [atualizando o config da sua extensão de diagnóstico](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Ver Eventos de Tecido de Serviço, incluindo ações em nós

Na página Análise de Tecidode Serviço, clique no gráfico para **eventos**de tecido de serviço .

![Canal Operacional de Solução de Tecido de Serviço](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Clique na **Lista** para ver os eventos numa lista. Uma vez aqui verá todos os eventos do sistema que foram recolhidos. Para referência, estes são do **WADServiceFabricSystemEventsTable** na conta de Armazenamento Azure, e da mesma forma os serviços confiáveis e eventos de atores que você vê a seguir são dessas tabelas respetivas.
    
![Canal Operacional de Consulta](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Alternativamente, pode clicar na lupa à esquerda e usar a linguagem de consulta Kusto para encontrar o que procura. Por exemplo, para encontrar todas as ações tomadas em nós no cluster, você pode usar a seguinte consulta. Os iDs do evento utilizados abaixo encontram-se na referência de [eventos de canais operacionais](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Pode consultar em muitos outros campos, como os nós específicos (Computador) o serviço de sistema (Nome de tarefa).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Ver serviço de serviço fiável serviço e eventos de ator

Na página Análise de Tecidode Serviço, clique no gráfico para **Serviços Fiáveis**.

![Serviço solução de tecido fiável Serviços fiáveis](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Clique na **Lista** para ver os eventos numa lista. Aqui pode ver eventos a partir dos serviços confiáveis. Você pode ver diferentes eventos para quando o runasync de serviço é iniciado e concluído, o que normalmente acontece em implementações e upgrades. 

![Serviços fiáveis de consulta](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Eventos de ator confiáveis podem ser vistos de forma semelhante. Para configurar eventos mais detalhados para `scheduledTransferKeywordFilter` atores fiáveis, é necessário alterar o config para a extensão de diagnóstico (mostrado abaixo). Os detalhes sobre os valores para estes estão na [referência de eventos de atores fiáveis.](service-fabric-reliable-actors-diagnostics.md#keywords)

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

A linguagem de consulta kusto é poderosa. Outra consulta valiosa que pode fazer é descobrir quais os nódosos que estão a gerar mais eventos. A consulta na imagem abaixo mostra eventos operacionais do Tecido de Serviço agregados com o serviço e o nó específicos.

![Eventos de consulta por nó](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Passos seguintes

* Para permitir a monitorização da infraestrutura, ou seja, contadores de desempenho, dirija-se à [adição do agente Log Analytics](service-fabric-diagnostics-oms-agent.md). O agente recolhe contadores de desempenho e adiciona-os ao seu espaço de trabalho existente.
* Para clusters no local, os registos do Monitor Azure oferecem um Gateway (HTTP Forward Proxy) que pode ser usado para enviar dados para registos do Monitor Azure. Leia mais sobre isso em [Computadores de Ligação sem acesso à Internet aos registos do Monitor Azure utilizando o gateway Log Analytics](../azure-monitor/platform/gateway.md).
* Configure [o alerta automatizado](../log-analytics/log-analytics-alerts.md) para ajudar na deteção e diagnóstico.
* Familiarize-se com as funcionalidades de [pesquisa de registos e consulta](../log-analytics/log-analytics-log-searches.md) oferecidas como parte dos registos do Monitor Azure.
* Obtenha uma visão mais detalhada dos registos do Monitor Azure e o que oferece, leia o que é os [registos do Monitor Azure?](../operations-management-suite/operations-management-suite-overview.md)
