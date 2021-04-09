---
title: Utilize registos do Monitor Azure para monitorizar os clusters Azure HDInsight
description: Aprenda a usar registos do Azure Monitor para monitorizar os trabalhos em execução num cluster HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, devx-track-azurepowershell
ms.date: 05/13/2020
ms.openlocfilehash: 0dfa89f50dedad41394cb77f1cca9b2dd3a65308
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865541"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Use Azure Monitor logs to monitor HDInsight clusters (Utilizar registos do Azure Monitor para monitorizar clusters do HDInsight)

Saiba como ativar os registos do Azure Monitor para monitorizar as operações de cluster Hadoop em HDInsight. E como adicionar uma solução de monitorização HDInsight.

[Os registos do Azure Monitor](../azure-monitor/logs/log-query-overview.md) são um serviço Azure Monitor que monitoriza os seus ambientes de nuvem e no local. A monitorização é para manter a sua disponibilidade e desempenho. Recolhe dados gerados por recursos na sua nuvem, ambientes no local e de outras ferramentas de monitorização. Os dados são usados para fornecer análises em várias fontes.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Log Analytics. Pode pensar neste espaço de trabalho como um ambiente único de registos do Azure Monitor com o seu próprio repositório de dados, fontes de dados e soluções. Para obter as instruções, consulte [Criar um espaço de trabalho log analytics](../azure-monitor/vm/quick-collect-azurevm.md#create-a-workspace).

* Um cluster do Azure HDInsight. Atualmente, pode utilizar registos do Azure Monitor com os seguintes tipos de cluster HDInsight:

  * Hadoop
  * HBase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Para obter as instruções sobre como criar um cluster HDInsight, consulte [Começar com Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Se utilizar o PowerShell, necessitará do [Módulo Az](/powershell/azure/). Certifique-se de que tem a versão mais recente. Se necessário, `Update-Module -Name Az` corra.

* Se quiser utilizar o Azure CLI e ainda não o instalou, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

> [!NOTE]  
> Recomenda-se colocar tanto o cluster HDInsight como o espaço de trabalho Log Analytics na mesma região para um melhor desempenho. Os registos do Azure Monitor não estão disponíveis em todas as regiões de Azure.

## <a name="enable-azure-monitor-using-the-portal"></a>Ativar o Monitor Azure utilizando o portal

Nesta secção, você configura um cluster hdinsight Hadoop existente para usar um espaço de trabalho Azure Log Analytics para monitorizar trabalhos, depurar registos, e assim por diante.

1. A partir do [portal Azure,](https://portal.azure.com/)selecione o seu cluster. O cluster é aberto numa nova página do portal.

1. A partir da esquerda, em **Monitorização,** selecione **Azure Monitor**.

1. A partir da vista principal, no âmbito da **Integração do Monitor Azure,** selecione **Enable**.

1. A partir da lista de drop-down do espaço de **trabalho,** selecione um espaço de trabalho log analytics existente.

1. Selecione **Guardar**.  Leva alguns momentos para salvar a configuração.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png" alt-text="Permitir a monitorização dos clusters HDInsight":::

## <a name="enable-azure-monitor-using-azure-powershell"></a>Ativar o Monitor Azure utilizando a Azure PowerShell

Pode ativar os registos do Azure Monitor utilizando o módulo Azure PowerShell Az [Enable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) cmdlet.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Para desativar, utilize o cmdlet [Disable-AzHDInsightMonitoring:](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring)

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Ativar o Monitor Azure utilizando o Azure CLI

Pode ativar os registos do Azure Monitor utilizando o comando Azure CLI `[az hdinsight monitor enable` (/cli/azure/hdinsight/monitor#az-hdinsight-monitor-enable).

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

Para desativar, utilize o [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az-hdinsight-monitor-disable) comando.

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Instalar soluções de gestão de clusters HDInsight

O HDInsight fornece soluções de gestão específicas do cluster que pode adicionar para os registos do Azure Monitor. [As soluções de gestão](../azure-monitor/insights/solutions.md) adicionam funcionalidade aos registos do Azure Monitor, fornecendo ferramentas adicionais de dados e análise. Estas soluções recolhem métricas de desempenho importantes dos seus clusters HDInsight. E fornecer as ferramentas para procurar as métricas. Estas soluções também fornecem visualizações e dashboards para a maioria dos tipos de cluster suportados em HDInsight. Ao utilizar as métricas que recolhe com a solução, pode criar regras e alertas de monitorização personalizados.

Soluções HDInsight disponíveis:

* Monitorização de Hadoop HDInsight
* Monitorização hdInsight HBase
* Monitorização de consultas interativas HDInsight
* Monitorização HDInsight Kafka
* Monitorização de faíscas HDInsight
* Monitorização de tempestades HDInsight

Para instruções de solução de gestão, consulte [soluções de Gestão no Azure.](../azure-monitor/insights/solutions.md#install-a-monitoring-solution) Para experimentar, instale uma solução de monitorização hdInsight Hadoop. Quando estiver feito, vê-se um azulejo **HDInsightHadoop** listado em **Resumo.** Selecione o **azulejo HDInsightHadoop.** A solução HDInsightHadoop parece:

:::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png" alt-text="Vista de solução de monitorização HDInsight":::

Como o cluster é um aglomerado novo, o relatório não mostra nenhuma atividade.

## <a name="configuring-performance-counters"></a>Configurar balcões de desempenho

O monitor Azure suporta a recolha e análise de métricas de desempenho para os nós do seu cluster. Para obter mais informações, consulte [fontes de dados de desempenho do Linux no Azure Monitor](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters).

## <a name="cluster-auditing"></a>Auditoria de cluster

Auditoria de cluster de suporte HDInsight com registos Azure Monitor, importando os seguintes tipos de registos:

* `log_gateway_audit_CL` - esta tabela fornece registos de auditoria a partir de nós de gateway de cluster que mostram tentativas de login bem sucedidas e falhadas.
* `log_auth_CL` - esta tabela fornece registos SSH com tentativas de login bem sucedidas e falhadas.
* `log_ambari_audit_CL` - esta tabela fornece registos de auditoria de Ambari.
* `log_ranger_audti_CL` - esta tabela fornece registos de auditoria da Apache Ranger em aglomerados ESP.

## <a name="next-steps"></a>Passos seguintes

* [Registos do Monitor de Consulta Azure para monitorizar clusters HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Como monitorizar a disponibilidade do cluster com os registos Apache Ambari e Azure Monitor](./hdinsight-cluster-availability.md)