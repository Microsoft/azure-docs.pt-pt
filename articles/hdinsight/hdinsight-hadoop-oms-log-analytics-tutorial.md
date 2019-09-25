---
title: Usar logs de Azure Monitor para monitorar os clusters do Azure HDInsight
description: Saiba como usar os logs de Azure Monitor para monitorar trabalhos em execução em um cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: a693b14bb61eb52a09ab1f1ecd5d00b339357d5d
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240373"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Usar logs de Azure Monitor para monitorar clusters HDInsight

Saiba como habilitar logs de Azure Monitor para monitorar as operações de cluster do Hadoop no HDInsight e como adicionar uma solução de monitoramento do HDInsight.

[Os logs de Azure monitor](../log-analytics/log-analytics-overview.md) são um serviço no Azure monitor que monitora seus ambientes locais e de nuvem para manter a disponibilidade e o desempenho. Recolhe dados gerados por recursos nos seus ambientes na cloud e no local e de outras ferramentas de monitorização, para disponibilizar análises relativas a várias origens.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma área de trabalho do Log Analytics**. Você pode considerar esse espaço de trabalho como um ambiente de logs de Azure Monitor exclusivo com seu próprio repositório de dados, fontes de dados e soluções. Para obter instruções, consulte [criar uma área de trabalho do Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Um cluster do Azure HDInsight**. No momento, você pode usar os logs de Azure Monitor com os seguintes tipos de cluster HDInsight:

  * Hadoop
  * HBase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Para instruções sobre como criar um cluster do HDInsight, consulte [introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* **Azure PowerShell módulo AZ**.  Consulte [apresentando o novo módulo Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> Recomenda-se para colocar o cluster do HDInsight e a área de trabalho do Log Analytics na mesma região para um melhor desempenho. Os logs de Azure Monitor não estão disponíveis em todas as regiões do Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Habilitar logs de Azure Monitor usando o portal

Nesta secção, vai configurar um cluster de Hadoop do HDInsight existente para utilizar uma área de trabalho do Log Analytics do Azure para monitorizar tarefas, os registos de depuração, etc.

1. No [portal do Azure](https://portal.azure.com/), selecione o cluster.  Consulte [listar e mostrar clusters](./hdinsight-administer-use-portal-linux.md#showClusters) para obter as instruções. O cluster é aberto em uma nova página do Portal.

1. À esquerda, em **monitoramento**, selecione **Operations Management Suite**.

1. Na exibição principal, em **monitoramento do OMS**, selecione **habilitar**.

1. Na lista suspensa **selecionar um espaço de trabalho** , selecione um espaço de trabalho log Analytics existente.

1. Selecione **Guardar**.  Demora alguns instantes para guardar a definição.

    ![Ativar a monitorização para os clusters do HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "ativar a monitorização para os clusters do HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Habilitar logs de Azure Monitor usando Azure PowerShell

Você pode habilitar os logs de Azure Monitor usando o cmdlet [Enable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) do módulo Azure PowerShell AZ.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables Operations Management Suite
Enable-AzHDInsightOperationsManagementSuite -ResourceGroupName $resourceGroup -Name $cluster -WorkspaceId $WorkspaceId -PrimaryKey $PrimaryKey
```

Para desabilitar, use o cmdlet [Disable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) :

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Instalar as soluções de gerenciamento de cluster do HDInsight

O HDInsight fornece soluções de gerenciamento específicas de cluster que você pode adicionar para logs de Azure Monitor. As [soluções de gerenciamento](../log-analytics/log-analytics-add-solutions.md) adicionam funcionalidade a logs de Azure monitor, fornecendo dados adicionais e ferramentas de análise. Estas soluções recolher métricas de desempenho importantes de seus clusters do HDInsight e fornecem as ferramentas para as métricas de pesquisa. Essas soluções também oferecem a visualizações e dashboards para a maioria dos tipos de cluster suportadas no HDInsight. Ao utilizar as métricas que recolhe com a solução, pode criar regras personalizadas de monitorização e alertas.

Estas são as soluções disponíveis do HDInsight:

* Monitorização do HDInsight Hadoop
* Monitorização do HDInsight HBase
* Monitorização do HDInsight Interactive Query
* Monitorização HDInsight Kafka
* Monitorização HDInsight Spark
* Monitorização do HDInsight Storm

Para as instruções para instalar uma solução de gestão, consulte [soluções de gestão do Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Para experimentar, instale uma solução de monitoramento do Hadoop do HDInsight. Quando estiver concluído, verá uma **HDInsightHadoop** mosaico listado na **resumo**. Selecione o **HDInsightHadoop** mosaico. A solução de HDInsightHadoop é semelhante a:

![Vista de solução de monitorização HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Uma vez que o cluster é um cluster totalmente novo, o relatório não mostra todas as atividades.

## <a name="configuring-performance-counters"></a>Configurando contadores de desempenho

O Azure monitor também dá suporte à coleta e análise de métricas de desempenho para os nós no cluster. Para obter mais informações sobre como habilitar e configurar esse recurso, consulte [fontes de dados de desempenho do Linux no Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Auditoria de cluster

O HDInsight dá suporte à auditoria de cluster com logs de Azure Monitor, importando os seguintes tipos de logs:

* `log_gateway_audit_CL`-Esta tabela fornece logs de auditoria de nós de gateway de cluster que mostram tentativas de logon bem-sucedidas e com falha.
* `log_auth_CL`-Esta tabela fornece logs SSH com tentativas de logon bem-sucedidas e com falha.
* `log_ambari_audit_CL`-Esta tabela fornece logs de auditoria do Ambari.
* `log_ranger_audti_CL`-Esta tabela fornece logs de auditoria do Apache Ranger em clusters ESP.

## <a name="next-steps"></a>Passos seguintes

* [Consultar logs de Azure Monitor para monitorar clusters HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
