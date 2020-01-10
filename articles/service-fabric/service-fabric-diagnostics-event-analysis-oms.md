---
title: Análise de eventos de Service Fabric do Azure com logs de Azure Monitor
description: Saiba mais sobre como Visualizar e analisar eventos usando logs de Azure Monitor para monitoramento e diagnóstico de clusters de Service Fabric do Azure.
author: srrengar
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: 40dd930aa21e3056d5ecc908359215d6874ed8ae
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464732"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Análise de eventos e visualização com logs de Azure Monitor
 Os logs de Azure Monitor coletam e analisam a telemetria de aplicativos e serviços hospedados na nuvem e fornecem ferramentas de análise para ajudá-lo a maximizar a disponibilidade e o desempenho. Este artigo descreve como executar consultas em logs de Azure Monitor para obter informações e solucionar problemas do que está acontecendo no cluster. As seguintes perguntas comuns são abordadas:

* Como fazer solucionar problemas de eventos de integridade?
* Como fazer saber quando um nó fica inoperante?
* Como fazer saber se os serviços do meu aplicativo foram iniciados ou interrompidos?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Visão geral do espaço de trabalho Log Analytics

>[!NOTE] 
>Embora o armazenamento de diagnóstico esteja habilitado por padrão no momento da criação do cluster, você ainda deve configurar o espaço de trabalho Log Analytics para ler do armazenamento de diagnóstico.

Azure Monitor logs coleta dados de recursos gerenciados, incluindo uma tabela de armazenamento do Azure ou um agente, e os mantém em um repositório central. Os dados podem então ser usados para análise, alertas e visualização ou para uma exportação adicional. Os logs de Azure Monitor dão suporte a eventos, dados de desempenho ou qualquer outro dado personalizado. Confira [as etapas para configurar a extensão de diagnóstico para agregar eventos](service-fabric-diagnostics-event-aggregation-wad.md) e [etapas para criar um log Analytics espaço de trabalho para ler os eventos no armazenamento](service-fabric-diagnostics-oms-setup.md) para certificar-se de que os dados estão fluindo para Azure monitor logs.

Depois que os dados são recebidos por logs de Azure Monitor, o Azure tem várias *soluções de monitoramento* que são soluções em pacotes ou painéis operacionais para monitorar dados de entrada, personalizados para vários cenários. Isso inclui uma solução *análise do Service Fabric* e uma solução de *contêineres* , que são as duas mais relevantes para diagnóstico e monitoramento ao usar clusters Service Fabric. Este artigo descreve como usar a solução Análise do Service Fabric, que é criada com o espaço de trabalho.

## <a name="access-the-service-fabric-analytics-solution"></a>Acessar a solução de Análise do Service Fabric

No [portal do Azure](https://portal.azure.com), vá para o grupo de recursos no qual você criou a solução de análise do Service Fabric.

Selecione o Resource **fabric\<nomedoespaçodetrabalhooms\>** .

No `Summary`, você verá blocos na forma de um grafo para cada uma das soluções habilitadas, incluindo uma para Service Fabric. Clique no **Service Fabric** grafo para continuar a solução de análise do Service Fabric.

![Solução Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

A imagem a seguir mostra a home page da solução Análise do Service Fabric. Este home page fornece uma exibição de instantâneo do que está acontecendo em seu cluster.

![Solução Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Se você tiver habilitado o diagnóstico na criação do cluster, poderá ver eventos para 

* [Service Fabric eventos de cluster](service-fabric-diagnostics-event-generation-operational.md)
* [Eventos de modelo de programação de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Eventos de modelo de programação de Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Além dos eventos de Service Fabric prontos para uso, eventos de sistema mais detalhados podem ser coletados [atualizando a configuração de sua extensão de diagnóstico](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Exibir eventos de Service Fabric, incluindo ações em nós

Na página Análise do Service Fabric, clique no grafo para **Service Fabric eventos**.

![Canal operacional da solução Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Clique em **lista** para exibir os eventos em uma lista. Uma vez aqui, você verá todos os eventos do sistema que foram coletados. Para referência, eles são do **WADServiceFabricSystemEventsTable** na conta de armazenamento do Azure e, da mesma forma, os eventos Reliable Services e atores que você vê em seguida são das respectivas tabelas.
    
![Canal operacional de consulta](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Como alternativa, você pode clicar na lupa à esquerda e usar a linguagem de consulta Kusto para encontrar o que está procurando. Por exemplo, para localizar todas as ações executadas em nós no cluster, você pode usar a consulta a seguir. As IDs de evento usadas abaixo são encontradas na [referência de eventos de canal operacional](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Você pode consultar muitos outros campos, como os nós específicos (computador) o serviço do sistema (TaskName).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Exibir Service Fabric eventos de ator e serviço confiável

Na página Análise do Service Fabric, clique no grafo para **Reliable Services**.

![Service Fabric solução Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Clique em **lista** para exibir os eventos em uma lista. Aqui você pode ver os eventos dos Reliable Services. Você pode ver eventos diferentes para quando o serviço RunAsync é iniciado e concluído, o que normalmente ocorre em implantações e atualizações. 

![Reliable Services de consulta](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Eventos de ator confiáveis podem ser exibidos de maneira semelhante. Para configurar eventos mais detalhados para Reliable Actors, você precisa alterar o `scheduledTransferKeywordFilter` na configuração da extensão de diagnóstico (mostrada abaixo). Os detalhes sobre os valores para eles estão na [referência de eventos dos Reliable Actors](service-fabric-reliable-actors-diagnostics.md#keywords).

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

A linguagem de consulta Kusto é poderosa. Outra consulta valiosa que você pode executar é descobrir quais nós estão gerando a maioria dos eventos. A consulta na captura de tela abaixo mostra Service Fabric eventos operacionais agregados com o serviço e o nó específicos.

![Eventos de consulta por nó](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Passos seguintes

* Para habilitar o monitoramento de infraestrutura, ou seja, contadores de desempenho, vá para a [adição do agente de log Analytics](service-fabric-diagnostics-oms-agent.md). O agente coleta contadores de desempenho e os adiciona ao espaço de trabalho existente.
* Para clusters locais, Azure Monitor logs oferece um gateway (proxy de encaminhamento HTTP) que pode ser usado para enviar dados a logs de Azure Monitor. Leia mais sobre isso em [conectando computadores sem acesso à Internet para Azure monitor logs usando o gateway de log Analytics](../azure-monitor/platform/gateway.md).
* Configure [alertas automatizados](../log-analytics/log-analytics-alerts.md) para auxiliar na detecção e no diagnóstico.
* Familiarize-se com os recursos de [pesquisa de logs e consulta](../log-analytics/log-analytics-log-searches.md) oferecidos como parte dos logs de Azure monitor.
* Obtenha uma visão geral mais detalhada dos logs de Azure Monitor e o que ele oferece, leia [o que são Azure monitor logs?](../operations-management-suite/operations-management-suite-overview.md).
