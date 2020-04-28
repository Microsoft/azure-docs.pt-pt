---
title: Utilize registos do Monitor Azure para monitorizar os clusters Azure HDInsight
description: Aprenda a utilizar os registos do Monitor Azure para monitorizar os trabalhos em funcionamento num cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 41688792330214943eeb116dc4b5aaf7eebfeebf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192047"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Use Azure Monitor logs to monitor HDInsight clusters (Utilizar registos do Azure Monitor para monitorizar clusters do HDInsight)

Aprenda a ativar os registos do Monitor Azure para monitorizar as operações de cluster hadoop no HDInsight. E como adicionar uma solução de monitorização HDInsight.

[Os registos do Azure Monitor](../log-analytics/log-analytics-overview.md) são um serviço Azure Monitor que monitoriza os ambientes de nuvem e no local. A monitorização é para manter a sua disponibilidade e desempenho. Recolhe dados gerados por recursos na sua nuvem, ambientes no local e de outras ferramentas de monitorização. Os dados são utilizados para fornecer análises através de várias fontes.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Log Analytics. Pode pensar neste espaço de trabalho como um ambiente único de logs Azure Monitor com o seu próprio repositório de dados, fontes de dados e soluções. Para obter as instruções, consulte Criar um espaço de [trabalho Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Um cluster do Azure HDInsight. Atualmente, pode utilizar registos do Monitor Azure com os seguintes tipos de cluster HDInsight:

  * Hadoop
  * HBase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Para obter as instruções sobre como criar um cluster HDInsight, consulte [Iniciar-se com Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Módulo Azure PowerShell Az.  Ver [Introdução do novo módulo Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Certifique-se de que tem a versão mais recente. Se necessário, `Update-Module -Name Az`corra.

> [!NOTE]  
> Recomenda-se colocar tanto o cluster HDInsight como o espaço de trabalho log Analytics na mesma região para um melhor desempenho. Os registos do Monitor Azure não estão disponíveis em todas as regiões do Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Ativar os registos do Monitor Azure utilizando o portal

Nesta secção, configura um aglomerado de Hadoop HDInsight existente para utilizar um espaço de trabalho Azure Log Analytics para monitorizar trabalhos, depuração de registos, e assim por diante.

1. A partir do [portal Azure,](https://portal.azure.com/)selecione o seu cluster. O cluster é aberto numa nova página do portal.

1. A partir da esquerda, sob **monitorização,** selecione **Monitor Azure**.

1. Do ponto de vista principal, no âmbito da **Integração do Monitor Azure,** selecione **Enable**.

1. A partir da lista de drop-down do espaço de **trabalho,** selecione um espaço de trabalho existente no Log Analytics.

1. Selecione **Guardar**.  Leva alguns momentos para salvar o cenário.

    ![Ativar a monitorização dos clusters HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Ativar a monitorização dos clusters HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Ativar os registos do Monitor Azure utilizando o Azure PowerShell

Pode ativar os registos do Monitor Azure utilizando o módulo PowerShell Az [Do ido-az.](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring)

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

Para desativar, utilize o cmdlet [de monitorização de desativação AzHDInsightMonitoring:](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring)

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Instale soluções de gestão de clusterS HDInsight

O HDInsight fornece soluções de gestão específicas do cluster que pode adicionar para registos do Monitor Azure. [As soluções](../log-analytics/log-analytics-add-solutions.md) de gestão adicionam funcionalidade aos registos do Monitor Do Azure, fornecendo ferramentas adicionais de dados e análises. Estas soluções recolhem métricas de desempenho importantes dos seus clusters HDInsight. E forneça as ferramentas para procurar as métricas. Estas soluções também fornecem visualizações e dashboards para a maioria dos tipos de cluster suportados no HDInsight. Ao utilizar as métricas que recolhe com a solução, pode criar regras e alertas de monitorização personalizados.

Soluções HDInsight disponíveis:

* Monitorização de Hadoop HDInsight
* Monitorização HDInsight HBase
* Monitorização interativa de consultas hDInsight
* Monitorização HDInsight Kafka
* Monitorização de faíscas HDInsight
* Monitorização de tempestades HDInsight

Para obter instruções de solução de gestão, consulte soluções de [gestão em Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Para experimentar, instale uma solução de monitorização de Hadoop HDInsight. Quando estiver feito, vê-se um azulejo **HDInsightHadoop** listado em **Resumo**. Selecione o azulejo **HDInsightHadoop.** A solução HDInsightHadoop parece:

![Vista de solução de monitorização HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Como o cluster é um novo cluster, o relatório não mostra nenhuma atividade.

## <a name="configuring-performance-counters"></a>Configurar contadores de desempenho

O monitor Azure suporta a recolha e análise de métricas de desempenho para os nós do seu cluster. Para mais informações, consulte as fontes de dados de desempenho do [Linux no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Auditoria de clusters

HDInsight suporta auditoria de cluster com registos do Monitor Azure, importando os seguintes tipos de registos:

* `log_gateway_audit_CL`- esta tabela fornece registos de auditoria a partir de nós de gateway de cluster que mostram tentativas de login bem sucedidas e falhadas.
* `log_auth_CL`- esta tabela fornece registos SSH com tentativas de login bem sucedidas e falhadas.
* `log_ambari_audit_CL`- esta tabela fornece registos de auditoria da Ambari.
* `log_ranger_audti_CL`- esta tabela fornece registos de auditoria da Apache Ranger em clusters ESP.

## <a name="next-steps"></a>Passos seguintes

* [Registos do Monitor De Consulta Azure para monitorizar os clusters HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Como monitorizar a disponibilidade do cluster com os registos Apache Ambari e Azure Monitor](./hdinsight-cluster-availability.md)
