---
title: Monitorização de Desempenho com registos do Monitor Azure
description: Aprenda a configurar o Agente De Log Analytics para monitorizar recipientes e contadores de desempenho para os seus clusters Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: c3c1bf511f3313e7408d6ce90b73de60bd1309f7
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366750"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Monitorização de Desempenho com registos do Monitor Azure

Este artigo cobre os passos para adicionar o agente Log Analytics como uma extensão de conjunto de escala de máquina virtual ao seu cluster, e conectá-lo ao espaço de trabalho do Azure Log Analytics existente. Isto permite recolher dados de diagnóstico sobre contentores, aplicações e monitorização de desempenho. Ao adicioná-lo como uma extensão ao recurso conjunto de conjunto de máquinas virtuais, o Azure Resource Manager garante que é instalado em todos os nós, mesmo ao escalonar o cluster.

> [!NOTE]
> Este artigo assume que já tem um espaço de trabalho Azure Log Analytics. Se não o fizer, dirija-se aos [registos do Monitor Azure](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Adicione a extensão do agente via Azure CLI

A melhor maneira de adicionar o agente Log Analytics ao seu cluster é através da escala virtual de máquinas APIs disponíveis com o Azure CLI. Se ainda não tiver o Azure CLI montado, dirija-se ao portal Azure e abra uma instância [Cloud Shell,](../cloud-shell/overview.md) ou [instale o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Assim que for solicitada a Cloud Shell, certifique-se de que está a trabalhar na mesma subscrição que o seu recurso. Verifique isto com `az account show` e certifique-se de que o valor do "nome" corresponde ao da subscrição do seu cluster.

2. No Portal, navegue para o grupo de recursos onde está localizado o seu espaço de trabalho Log Analytics. Clique no recurso de análise de registo (o tipo do recurso será log Analytics espaço de trabalho). Uma vez na página de visão geral do recurso, clique em **Definições Avançadas** na secção Definições no menu esquerdo.

    ![Página de propriedades de análise de log](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Clique nos **Servidores do Windows** se estiver a levantar um cluster do Windows e **servidores Linux** se estiver a criar um cluster Linux. Esta página irá mostrar-lhe o seu `workspace ID` e `workspace key` (listado como Chave Primária no portal). Vai precisar de ambos para o próximo passo.

4. Envie o comando para instalar o agente Log Analytics no seu cluster, utilizando a API `vmss extension set`:

    Para um cluster Windows:

    ```azurecli
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Para um aglomerado de Linux:

    ```azurecli
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Aqui está um exemplo de que o agente Log Analytics está a ser adicionado a um cluster Windows.

    ![Log Analytics agent cli comando](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Isto deve demorar menos de 15 min para adicionar o agente com sucesso aos seus nós. Pode verificar se os agentes foram adicionados utilizando a API `az vmss extension list`:

    ```azurecli
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Adicione o agente através do modelo de Gestor de Recursos

Os modelos do Gestor de Recursos de Amostra que implementam um espaço de trabalho do Azure Log Analytics e adicionam um agente a cada um dos seus nós estão disponíveis para [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) ou [Linux](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS).

Você pode baixar e modificar este modelo para implementar um cluster que melhor se adapte às suas necessidades.

## <a name="view-performance-counters"></a>Ver contadores de desempenho

Agora que adicionou o agente Log Analytics, dirija-se ao portal Log Analytics para escolher quais os contadores de desempenho que gostaria de recolher.

1. No portal Azure, vá ao grupo de recursos em que criou a solução Service Fabric Analytics. Selecione **ServiceFabric\<nomeOfLog AnalyticsWorkspace\>** .

2. Clique em **Log Analytics**.

3. Clique em **Definições Avançadas**.

4. Clique em **Dados**e, em seguida, clique em Contadores de **Desempenho Windows ou Linux**. Existe uma lista de contadores predefinidos que pode escolher para ativar e pode definir o intervalo para a recolha também. Também pode adicionar contadores de [desempenho adicionais](service-fabric-diagnostics-event-generation-perf.md) para recolher. O formato adequado é referenciado neste [artigo.](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx)

5. Clique em **Guardar**e, em seguida, clique **em OK**.

6. Feche a lâmina de Definições Avançadas.

7. Sob a rubrica Geral, clique no resumo do **Espaço de Trabalho**.

8. Você verá azulejos na forma de um gráfico para cada uma das soluções ativadas, incluindo uma para tecido de serviço. Clique no gráfico **de Tecido de Serviço** para continuar na solução Service Fabric Analytics.

9. Você verá alguns azulejos com gráficos em canal operacional e eventos de serviços confiáveis. A representação gráfica dos dados que fluem para os contadores selecionados aparecerá em Métricas de Nó.

10. Clique num gráfico de Métrica de Contentores para ver detalhes adicionais. Também pode consultar dados de contra-desempenho semelhantes aos eventos de cluster e filtrar nos nós, contra-nome perf e valores usando a linguagem de consulta Kusto.

![Log Analytics perf counter consulta](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Passos seguintes

* Recolha contadores de [desempenho relevantes.](service-fabric-diagnostics-event-generation-perf.md) Para configurar o agente Log Analytics para recolher contadores de desempenho específicos, reveja [a configuração de fontes](../azure-monitor/platform/agent-data-sources.md#configuring-data-sources)de dados .
* Configure registos do Monitor Azure para criar [alertaautomatizado](../log-analytics/log-analytics-alerts.md) para ajudar na deteção e diagnóstico
* Como alternativa, pode recolher contadores de desempenho através da [extensão do Azure Diagnostics e enviá-los para Application Insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
