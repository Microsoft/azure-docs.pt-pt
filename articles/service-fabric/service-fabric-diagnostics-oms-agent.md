---
title: Monitorização de desempenho com registos do Monitor Azure
description: Saiba como configurar o Agente Descuidado de Log para monitorizar contentores e balcões de desempenho para os seus clusters de Tecido de Serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 47017fdb5f1fbaba800e71dea21afe2c39bd91e7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570149"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Monitorização de desempenho com registos do Monitor Azure

Este artigo cobre os passos para adicionar o agente Log Analytics como uma extensão de conjunto de escala de máquina virtual ao seu cluster, e conectá-lo ao seu espaço de trabalho Azure Log Analytics existente. Isto permite recolher dados de diagnóstico sobre recipientes, aplicações e monitorização de desempenho. Ao adicioná-lo como uma extensão ao recurso de conjunto de escala de máquina virtual, o Azure Resource Manager garante que ele é instalado em todos os nós, mesmo quando escala o cluster.

> [!NOTE]
> Este artigo pressupõe que você tem um espaço de trabalho Azure Log Analytics já configurado. Se não o fizer, dirija-se à [Configuração dos registos do Monitor Azure](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Adicione a extensão do agente via Azure CLI

A melhor forma de adicionar o agente Log Analytics ao seu cluster é através do conjunto de APIs de escala de máquina virtual disponível com o Azure CLI. Se ainda não tiver o Azure CLI configurado, dirija-se ao portal Azure e abra uma instância [cloud Shell](../cloud-shell/overview.md) ou [instale o Azure CLI](/cli/azure/install-azure-cli).

1. Uma vez solicitada a sua Cloud Shell, certifique-se de que está a trabalhar na mesma subscrição que o seu recurso. Verifique isto `az account show` com e certifique-se de que o valor do "nome" corresponde ao da subscrição do seu cluster.

2. No Portal, navegue para o grupo de recursos onde está localizado o seu espaço de trabalho Log Analytics. Clique no recurso de análise de registo (o tipo de recurso será log analytics workspace). Uma vez que esteja na página geral do recurso, clique em **Definições Avançadas** na secção Definições no menu esquerdo.

    ![Página de propriedades de analítica de registo](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Clique em **Servidores do Windows** se estiver em pé num cluster Windows e **em Servidores Linux** se estiver a criar um cluster Linux. Esta página irá mostrar-lhe o seu `workspace ID` e `workspace key` (listado como Chave Primária no portal). Vai precisar de ambos para o próximo passo.

4. Executar o comando para instalar o agente Log Analytics no seu cluster, utilizando a `vmss extension set` API:

    Para um cluster Windows:

    ```azurecli
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Para um cluster Linux:

    ```azurecli
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Aqui está um exemplo de que o agente Log Analytics foi adicionado a um cluster Windows.

    ![Log Analytics agente cli comando](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Isto deve levar menos de 15 minutos para adicionar o agente com sucesso aos seus nós. Pode verificar se os agentes foram adicionados utilizando a `az vmss extension list` API:

    ```azurecli
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Adicione o agente através do modelo de Gestor de Recursos

Os modelos do Gestor de Recursos de Amostra que implementam um espaço de trabalho Azure Log Analytics e adicionam um agente a cada um dos seus nós estão disponíveis para [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) ou [Linux](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS).

Você pode baixar e modificar este modelo para implementar um cluster que melhor se adapte às suas necessidades.

## <a name="view-performance-counters"></a>Ver contadores de desempenho

Agora que adicionou o agente Log Analytics, dirija-se ao portal Log Analytics para escolher quais os contadores de desempenho que gostaria de recolher.

1. No portal Azure, vá ao grupo de recursos em que criou a solução Service Fabric Analytics. Selecione **ServiceFabric \<nameOfLog AnalyticsWorkspace\>**.

2. Clique em **Log Analytics**.

3. Clique **em Definições Avançadas**.

4. Clique em **Dados** e, em seguida, clique em **Contadores de Desempenho Windows ou Linux**. Há uma lista de contadores predefinidos que pode escolher para ativar e também pode definir o intervalo para recolha. Também pode adicionar [contadores de desempenho adicionais](service-fabric-diagnostics-event-generation-perf.md) para recolher. O formato adequado é referenciado neste [artigo.](/windows/win32/perfctrs/specifying-a-counter-path)

5. Clique **em Guardar** e, em seguida, clique em **OK**.

6. Feche a lâmina de Definições Avançadas.

7. No título Geral, clique no **resumo do Espaço de Trabalho.**

8. Você verá azulejos na forma de um gráfico para cada uma das soluções ativadas, incluindo uma para Tecido de Serviço. Clique no gráfico **de Tecido de Serviço** para continuar na solução Service Fabric Analytics.

9. Você verá alguns azulejos com gráficos em canal operacional e eventos de serviços confiáveis. A representação gráfica dos dados que fluem para os balcões selecionados aparecerá em Métricas de Nó.

10. Clique num gráfico métrico do recipiente para ver detalhes adicionais. Também pode consultar dados de contador de desempenho semelhantes a eventos de cluster e filtrar nos nós, nome de contador perf e valores usando a linguagem de consulta Kusto.

![Log Analytics perf consulta de contador](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Passos seguintes

* Recolha [os contadores de desempenho relevantes.](service-fabric-diagnostics-event-generation-perf.md) Para configurar o agente Log Analytics para recolher contadores de desempenho específicos, [reveja as fontes de dados configuradas](../azure-monitor/agents/agent-data-sources.md#configuring-data-sources).
* Configurar registos do Azure Monitor para criar [alertas automatizados](../azure-monitor/alerts/alerts-overview.md) para ajudar na deteção e diagnóstico
* Como alternativa, pode recolher contadores de desempenho através da [extensão Azure Diagnostics e enviá-los para Application Insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
