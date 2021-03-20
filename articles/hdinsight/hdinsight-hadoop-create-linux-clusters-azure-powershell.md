---
title: Criar clusters Apache Hadoop usando PowerShell - Azure HDInsight
description: Aprenda a criar aglomerados Apache Hadoop, Apache HBase, Apache Storm ou Apache Spark em Linux para HDInsight utilizando a Azure PowerShell.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 86622bf96d4b59537a2946073fdc638e51c3852d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945832"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Criar clusters baseados em Linux em HDInsight usando Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O Azure PowerShell é um poderoso ambiente de scripts que podes usar para controlar e automatizar a implementação e gestão das tuas cargas de trabalho no Microsoft Azure. Este documento fornece informações sobre como criar um cluster HDInsight baseado em Linux utilizando a Azure PowerShell. Também inclui um roteiro de exemplo.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Concha de Azure PowerShell](/powershell/azure/install-Az-ps) Módulo Az.

## <a name="create-cluster"></a>Criar cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para criar um cluster HDInsight utilizando a Azure PowerShell, tem de completar os seguintes procedimentos:

* Criar um grupo de recursos Azure
* Criar uma conta de Armazenamento do Azure
* Criar um recipiente Azure Blob
* Criar um cluster do HDInsight

> [!NOTE]
> A utilização do PowerShell para criar um cluster HDInsight com a Azure Data Lake Storage Gen2 não é suportada atualmente.

O seguinte script demonstra como criar um novo cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Os valores especificados para o login do cluster são utilizados para criar a conta de utilizador Hadoop para o cluster. Utilize esta conta para ligar aos serviços alojados no cluster, tais como UIs web ou REST APIs.

Os valores especificados para o utilizador SSH são utilizados para criar o utilizador SSH para o cluster. Utilize esta conta para iniciar uma sessão remota de SSH no cluster e executar empregos. Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Se pretender utilizar mais de 32 nós de trabalhadores (quer na criação de clusters, quer através da escala do cluster após a criação), deve também especificar um tamanho do nó de cabeça com pelo menos 8 núcleos e 14 GB de RAM.
>
> Para obter mais informações sobre tamanhos de nós e custos associados, veja os [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Pode levar até 20 minutos para criar um cluster.

## <a name="create-cluster-configuration-object"></a>Criar cluster: Objeto de configuração

Também pode criar um objeto de configuração HDInsight utilizando [`New-AzHDInsightClusterConfig`](/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) cmdlet. Em seguida, pode modificar este objeto de configuração para ativar opções de configuração adicionais para o seu cluster. Finalmente, utilize o `-Config` parâmetro do [`New-AzHDInsightCluster`](/powershell/module/az.hdinsight/new-azhdinsightcluster) cmdlet para utilizar a configuração.

O seguinte script cria um objeto de configuração para configurar um Servidor R no tipo de cluster HDInsight. A configuração permite um nó de borda, RStudio e uma conta de armazenamento adicional.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> A utilização de uma conta de armazenamento num local diferente do cluster HDInsight não é suportada. Ao utilizar este exemplo, crie a conta de armazenamento adicional no mesmo local que o servidor.

## <a name="customize-clusters"></a>Personalizar clusters

* Consulte [os clusters HDInsight personalizados utilizando o Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Consulte [os clusters HDInsight personalizados utilizando a Ação do Script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Passos seguintes

Agora que criou com sucesso um cluster HDInsight, use os seguintes recursos para aprender a trabalhar com o seu cluster.

### <a name="apache-hadoop-clusters"></a>Aglomerados apache Hadoop

* [Use a Colmeia Apache com HDInsight](hadoop/hdinsight-use-hive.md)
* [Use MapReduce com HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Aglomerados Apache HBase

* [Começar com Apache HBase em HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicações Java para Apache HBase em HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Aglomerados de tempestade

* [Desenvolver topologias java para tempestade em HDInsight](storm/apache-storm-develop-java-topology.md)
* [Use componentes Python em Storm on HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implementar e monitorizar topologias com tempestade em HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Aglomerados de faíscas apache

* [Criar uma aplicação autónoma com o Scala](spark/apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um aglomerado Apache Spark usando Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark com BI: Realizar análise de dados interativas usando Spark in HDInsight com ferramentas BI](spark/apache-spark-use-bi-tools.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para prever os resultados da inspeção alimentar](spark/apache-spark-machine-learning-mllib-ipython.md)