---
title: Monitoramento de desempenho com logs de Azure Monitor
description: Saiba como configurar o agente de Log Analytics para monitorar contêineres e contadores de desempenho para seus clusters de Service Fabric do Azure.
author: srrengar
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 8c8978a0114caf57d01f7add0bd9357c5d0775dc
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609949"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Monitoramento de desempenho com logs de Azure Monitor

Este artigo aborda as etapas para adicionar o agente de Log Analytics como uma extensão de conjunto de dimensionamento de máquinas virtuais ao cluster e conectá-lo ao seu espaço de trabalho do Azure Log Analytics existente. Isso permite coletar dados de diagnóstico sobre contêineres, aplicativos e monitoramento de desempenho. Ao adicioná-lo como uma extensão ao recurso do conjunto de dimensionamento de máquinas virtuais, o Azure Resource Manager garante que ele seja instalado em todos os nós, mesmo ao dimensionar o cluster.

> [!NOTE]
> Este artigo pressupõe que você tenha um espaço de trabalho do Azure Log Analytics já configurado. Se você não fizer isso, vá para [Configurar Logs de Azure monitor](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Adicionar a extensão do agente via CLI do Azure

A melhor maneira de adicionar o agente de Log Analytics ao cluster é por meio das APIs do conjunto de dimensionamento de máquinas virtuais disponíveis com o CLI do Azure. Se você ainda não tiver CLI do Azure configurado, vá para portal do Azure e abra uma instância [Cloud Shell](../cloud-shell/overview.md) ou [Instale o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Quando o Cloud Shell for solicitado, verifique se você está trabalhando na mesma assinatura que o recurso. Verifique isso com `az account show` e verifique se o valor de "nome" corresponde ao da assinatura do cluster.

2. No portal, navegue até o grupo de recursos em que seu espaço de trabalho do Log Analytics está localizado. Clique no recurso log Analytics (o tipo do recurso será Log Analytics espaço de trabalho). Quando você estiver na página Visão geral do recurso, clique em **Configurações avançadas** na seção configurações no menu à esquerda.

    ![Página de propriedades do log Analytics](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Clique em **servidores Windows** se você estiver acumulando um cluster do Windows e **servidores Linux** se estiver criando um cluster do Linux. Esta página mostrará sua `workspace ID` e `workspace key` (listado como chave primária no Portal). Você precisará dos dois para a próxima etapa.

4. Execute o comando para instalar o agente de Log Analytics no cluster, usando a API `vmss extension set` em seu Cloud Shell:

    Para um cluster do Windows:

    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Para um cluster do Linux:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Aqui está um exemplo do agente de Log Analytics que está sendo adicionado a um cluster do Windows.

    ![Comando da CLI do agente Log Analytics](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Isso deve levar menos de 15 minutos para adicionar com êxito o agente aos seus nós. Você pode verificar se os agentes foram adicionados usando a API de `az vmss extension list`:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Adicionar o agente por meio do modelo do Resource Manager

Os modelos do Resource Manager de exemplo que implantam um espaço de trabalho do Azure Log Analytics e adicionam um agente a cada um de seus nós estão disponíveis para [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) ou [Linux](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS).

Você pode baixar e modificar esse modelo para implantar um cluster que melhor atenda às suas necessidades.

## <a name="view-performance-counters"></a>Exibir contadores de desempenho

Agora que você adicionou o agente de Log Analytics, acesse o portal de Log Analytics para escolher quais contadores de desempenho você gostaria de coletar.

1. Na portal do Azure, vá para o grupo de recursos no qual você criou a solução de Análise do Service Fabric. Selecione o **\<do NameOfLog AnalyticsWorkspace\>** .

2. Clique em **Log Analytics**.

3. Clique em **Configurações avançadas**.

4. Clique em **dados**e em **contadores de desempenho do Windows ou do Linux**. Há uma lista de contadores padrão que você pode optar por habilitar e também pode definir o intervalo para coleta. Você também pode adicionar [outros contadores de desempenho](service-fabric-diagnostics-event-generation-perf.md) para coletar. O formato correto é referenciado neste [artigo](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Clique em **salvar**e em **OK**.

6. Feche a folha configurações avançadas.

7. No cabeçalho geral, clique em **Resumo do espaço de trabalho**.

8. Você verá blocos na forma de um grafo para cada uma das soluções habilitadas, incluindo uma para Service Fabric. Clique no **Service Fabric** grafo para continuar a solução de análise do Service Fabric.

9. Você verá alguns blocos com grafos em eventos de canal operacional e Reliable Services. A representação gráfica dos dados que fluem para os contadores selecionados aparecerá em métricas de nó.

10. Clique em um grafo de métrica de contêiner para ver detalhes adicionais. Você também pode consultar dados do contador de desempenho da mesma forma que os eventos de cluster e filtrar os nós, o nome do contador perf e os valores usando a linguagem de consulta Kusto.

![Log Analytics consulta do contador de desempenho](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Passos seguintes

* Coletar [contadores de desempenho](service-fabric-diagnostics-event-generation-perf.md)relevantes. Para configurar o agente de Log Analytics para coletar contadores de desempenho específicos, examine [Configurando fontes de dados](../azure-monitor/platform/agent-data-sources.md#configuring-data-sources).
* Configurar logs de Azure Monitor para configurar [alertas automatizados](../log-analytics/log-analytics-alerts.md) para auxiliar na detecção e no diagnóstico
* Como alternativa, você pode coletar contadores de desempenho por meio da [extensão diagnóstico do Azure e enviá-los para Application insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
