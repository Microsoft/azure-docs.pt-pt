---
title: Registos do Azure Service Fabric - monitorização de desempenho com o Azure Monitor | Documentos da Microsoft
description: Saiba como configurar o agente de análise de registo para a monitorização de contentores e contadores de desempenho para os seus clusters do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 3d35075c768855ebd907b96de2ded82757d5e525
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242909"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Monitorização de desempenho com os registos do Azure Monitor

Este artigo aborda os passos para adicionar o agente Log Analytics como extensão de conjunto de dimensionamento de máquinas virtuais ao seu cluster e ligá-la à sua área de trabalho do Log Analytics do Azure existente. Isso permite recolher dados de diagnóstico sobre contentores, aplicativos e monitorização do desempenho. Ao adicioná-la como uma extensão para o recurso de conjunto de dimensionamento de máquina virtual, do Azure Resource Manager garante que é instalado em cada nó, mesmo quando a dimensionar o cluster.

> [!NOTE]
> Este artigo pressupõe que tem uma área de trabalho do Log Analytics do Azure já configurada. Se não o fizer, aceda a [configurar os registos do Azure do Azure Monitor](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Adicione a extensão do agente através da CLI do Azure

A melhor forma de adicionar o agente Log Analytics para o cluster é por meio de dimensionamento da máquina virtual definida APIs disponíveis com a CLI do Azure. Se não tiver o CLI do Azure configurar ainda, ir para o portal do Azure e abrir um [Cloud Shell](../cloud-shell/overview.md) instância, ou [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Depois do Cloud Shell é solicitada, certifique-se de que está a trabalhar na mesma subscrição que o seu recurso. Verificar isto com `az account show` e certificar-se de que o valor de "nome" corresponde da subscrição do seu cluster.

2. No Portal, navegue para o grupo de recursos onde está localizada a sua área de trabalho do Log Analytics. Clique para o recurso de análise de registo (o tipo do recurso será área de trabalho do Log Analytics). Assim que estiver a página de descrição geral de recursos, clique em **definições avançadas** na secção definições no menu da esquerda.

    ![Página de propriedades do log analytics](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. Clique em **servidores do Windows** se estão esperando um cluster do Windows, e **servidores Linux** se estiver a criar um cluster do Linux. Esta página irá mostrar sua `workspace ID` e `workspace key` (apresentados como chave primária no portal). Precisará para o passo seguinte.

4. Execute o comando para instalar o agente do Log Analytics no seu cluster, utilizando o `vmss extension set` API no Cloud Shell:

    Para um cluster do Windows:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Para um cluster do Linux:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Eis um exemplo do agente do Log Analytics que está a ser adicionado a um cluster do Windows.

    ![Comando de cli de agente de análise de registo](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. Esta operação deve demorar menos de 15 minutos para adicionar com êxito o agente para os nós. Pode verificar que os agentes foram adicionados ao utilizar o `az vmss extension list` API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Adicionar agente por meio do modelo do Resource Manager

Modelos do Gestor de recursos de exemplo que implementar uma área de trabalho do Log Analytics do Azure e adicionar um agente a cada um dos seus nós está disponível para [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) ou [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Pode transferir e modificar este modelo para implementar um cluster mais adequado às suas necessidades.

## <a name="view-performance-counters"></a>Contadores de desempenho do Vista

Agora que adicionou o agente do Log Analytics, head a ativação pós-falha para o portal do Log Analytics para escolher os contadores de desempenho pretende recolher. 

1. No portal do Azure, vá para o grupo de recursos em que criou a solução de análise do Service Fabric. Selecione **ServiceFabric\<nameOfLog AnalyticsWorkspace\>**.

2. Clique em **Log Analytics**.

3. Clique em **definições avançadas**.

4. Clique em **dados**, em seguida, clique em **contadores de desempenho de Linux ou Windows**. Há uma lista de contadores padrão, que pode optar por ativar e pode definir o intervalo de coleção. Também pode adicionar [contadores de desempenho adicionais](service-fabric-diagnostics-event-generation-perf.md) para recolher. O formato correto é referenciado nesta [artigo](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Clique em **salvar**, em seguida, clique em **OK**.

6. Feche o painel de definições avançadas.

7. Sob o cabeçalho de geral, clique em **resumo de área de trabalho**.

8. Verá os mosaicos na forma de um gráfico para cada uma das soluções de ativadas, incluindo um para o Service Fabric. Clique nas **Service Fabric** gráfico para continuar para a solução de análise do Service Fabric.

9. Verá alguns mosaicos com gráficos no canal operacional e de eventos do reliable services. A representação gráfica de fluxo para os contadores que selecionou de dados será apresentado no nó de métricas. 

10. Clique num gráfico de métrica de contentor para ver detalhes adicionais. Pode também consultar os dados de contador de desempenho da mesma forma para eventos de cluster e filtra os nós, o nome do contador de desempenho e a valores usando a linguagem de consulta de Kusto.

![Consulta do contador de desempenho do log Analytics](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Passos Seguintes

* Recolher relevante [contadores de desempenho](service-fabric-diagnostics-event-generation-perf.md). Para configurar o agente do Log Analytics para recolher contadores de desempenho específicos, reveja [configurar origens de dados](../azure-monitor/platform/agent-data-sources.md#configuring-data-sources).
* Configurar registos de Monitor do Azure para configurar [alertas automáticos](../log-analytics/log-analytics-alerts.md) para ajudar a detetar e diagnóstico
* Como alternativa pode recolher contadores de desempenho por meio de [extensão de diagnóstico do Azure e enviá-los para o Application Insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
