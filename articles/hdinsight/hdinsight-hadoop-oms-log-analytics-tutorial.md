---
title: Registos de utilização do Azure Monitor para monitorizar clusters do HDInsight do Azure
description: Saiba como utilizar os registos do Azure do Azure Monitor para monitorizar tarefas em execução num cluster do HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: hrasheed
ms.openlocfilehash: b7e0dba70d7f3a201c5f3491f0bc906977fbf229
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886456"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Registos de utilização do Azure Monitor para monitorizar clusters do HDInsight

Saiba como ativar os registos do Azure Monitor monitorizar as operações de cluster de Hadoop no HDInsight e como adicionar uma solução de monitorização de Hdinsight.

[Registos de Monitor do Azure](../log-analytics/log-analytics-overview.md) é um serviço no Azure Monitor, que monitoriza a sua cloud e ambientes para manter a disponibilidade e desempenho no local. Recolhe dados gerados por recursos nos seus ambientes na cloud e no local e de outras ferramentas de monitorização, para disponibilizar análises relativas a várias origens.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma área de trabalho do Log Analytics**. Pode pensar nesta área de trabalho como um ambiente de registos do Azure Monitor exclusivo com seu próprio repositório de dados, origens de dados e soluções. Para obter instruções, consulte [criar uma área de trabalho do Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Um cluster do Azure HDInsight**. Atualmente, pode utilizar os registos do Azure Monitor com os seguintes tipos de cluster do HDInsight:

  * Hadoop
  * HBase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Para instruções sobre como criar um cluster do HDInsight, consulte [introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* **Módulo do Azure PowerShell Az**.  Ver [apresentando o novo módulo Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> Recomenda-se para colocar o cluster do HDInsight e a área de trabalho do Log Analytics na mesma região para um melhor desempenho. Registos de Monitor do Azure não está disponível em todas as regiões do Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Ativar registos de Monitor do Azure com o portal

Nesta secção, vai configurar um cluster de Hadoop do HDInsight existente para utilizar uma área de trabalho do Log Analytics do Azure para monitorizar tarefas, os registos de depuração, etc.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No menu à esquerda, selecione **todos os serviços**.

3. Sob **ANALYTICS**, selecione **clusters do HDInsight**.

4. Selecione o cluster na lista.

5. Da esquerda, em **monitorização**, selecione **Operations Management Suite**.

6. Na vista principal, sob **monitorização do OMS**, selecione **ativar**.

7. Partir do **Selecione uma área de trabalho** pendente, selecione uma área de trabalho do Log Analytics existente.

8. Selecione **Guardar**.  Demora alguns instantes para guardar a definição.

    ![Ativar a monitorização para os clusters do HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "ativar a monitorização para os clusters do HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Ativar registos de Monitor do Azure com o Azure PowerShell

Pode ativar os registos do Azure Monitor utilizando o módulo Azure PowerShell Az [Enable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) cmdlet.

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

Para desativar a utilização a [Disable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) cmdlet:

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Instalar as soluções de gerenciamento de cluster do HDInsight

HDInsight fornece soluções de gerenciamento de cluster específicos que podem ser adicionados para os registos do Azure Monitor. [Soluções de gestão](../log-analytics/log-analytics-add-solutions.md) adicionar funcionalidade a registos do Azure Monitor, fornecendo dados adicionais e ferramentas de análise. Estas soluções recolher métricas de desempenho importantes de seus clusters do HDInsight e fornecem as ferramentas para as métricas de pesquisa. Essas soluções também oferecem a visualizações e dashboards para a maioria dos tipos de cluster suportadas no HDInsight. Ao utilizar as métricas que recolhe com a solução, pode criar regras personalizadas de monitorização e alertas.

Estas são as soluções disponíveis do HDInsight:

* Monitorização do HDInsight Hadoop
* Monitorização do HDInsight HBase
* Monitorização do HDInsight Interactive Query
* Monitorização HDInsight Kafka
* Monitorização HDInsight Spark
* Monitorização do HDInsight Storm

Para as instruções para instalar uma solução de gestão, consulte [soluções de gestão do Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Para experimentar, instale uma solução de monitorização do HDInsight Hadoop. Quando estiver concluído, verá uma **HDInsightHadoop** mosaico listado na **resumo**. Selecione o **HDInsightHadoop** mosaico. A solução de HDInsightHadoop é semelhante a:

![Vista de solução de monitorização HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Uma vez que o cluster é um cluster totalmente novo, o relatório não mostra todas as atividades.

## <a name="next-steps"></a>Passos Seguintes

* [Registos de consulta do Azure Monitor para monitorizar clusters do HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
