---
title: Criar clusters Apache Hadoop usando o PowerShell-Azure HDInsight
description: Saiba como criar clusters Apache Hadoop, Apache HBase, Apache Storm ou Apache Spark no Linux para HDInsight usando o Azure PowerShell.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: hrasheed
ms.openlocfilehash: a6847e75a0a6dcf944b033054ac466841294d28b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494787"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Criar clusters baseados em Linux no HDInsight usando Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Microsoft Azure. Este documento fornece informações sobre como criar um cluster HDInsight baseado em Linux usando Azure PowerShell. Ele também inclui um script de exemplo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de iniciar este procedimento, você deve ter o seguinte:

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-Az-ps)

    > [!IMPORTANT]  
    > O suporte do Azure PowerShell para gerir recursos do HDInsight com o Gestor de Serviços do Azure está **preterido**, e foi removido a 1 de janeiro de 2017. Os passos neste documento utilizam os novos cmdlets do HDInsight que funcionam com o Azure Resource Manager.
    >
    > Siga as etapas em [instalar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) para instalar a versão mais recente do Azure PowerShell. Se tiver scripts que tenham de ser modificados para utilizar os novos cmdlets que funcionam com o Azure Resource Manager, veja [Migrar para as ferramentas de desenvolvimento baseadas no Azure Resource Manager para clusters do HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) para obter mais informações.

## <a name="create-cluster"></a>Criar cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para criar um cluster HDInsight usando Azure PowerShell, você deve concluir os seguintes procedimentos:

* Criar um grupo de recursos do Azure
* Criar uma conta de Armazenamento do Azure
* Criar um contêiner de blob do Azure
* Criar um cluster do HDInsight

O script a seguir demonstra como criar um novo cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Os valores que você especificar para o logon do cluster são usados para criar a conta de usuário do Hadoop para o cluster. Use essa conta para se conectar aos serviços hospedados no cluster, como UIs da Web ou APIs REST.

Os valores especificados para o usuário SSH são usados para criar o usuário SSH para o cluster. Use essa conta para iniciar uma sessão SSH remota no cluster e executar trabalhos. Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Se você planeja usar mais de 32 nós de trabalho (na criação do cluster ou dimensionando o cluster após a criação), você também deve especificar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.
>
> Para obter mais informações sobre tamanhos de nós e custos associados, veja os [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Pode levar até 20 minutos para criar um cluster.

## <a name="create-cluster-configuration-object"></a>Criar cluster: objeto de configuração

Você também pode criar um objeto de configuração do HDInsight usando `New-AzHDInsightClusterConfig` cmdlet. Você pode modificar esse objeto de configuração para habilitar opções de configuração adicionais para o cluster. Por fim, use o parâmetro `-Config` do cmdlet `New-AzHDInsightCluster` para usar a configuração.

O script a seguir cria um objeto de configuração para configurar um servidor R no tipo de cluster HDInsight. A configuração habilita um nó de borda, RStudio e uma conta de armazenamento adicional.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> Não há suporte para o uso de uma conta de armazenamento em um local diferente do cluster HDInsight. Ao usar este exemplo, crie a conta de armazenamento adicional no mesmo local que o servidor.

## <a name="customize-clusters"></a>Personalizar clusters

* Consulte [Personalizar clusters HDInsight usando a inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Consulte [Personalizar clusters HDInsight usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Passos seguintes

Agora que você criou um cluster HDInsight com êxito, use os recursos a seguir para aprender a trabalhar com o cluster.

### <a name="apache-hadoop-clusters"></a>Clusters Apache Hadoop

* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters do Apache HBase

* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicativos Java para o Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters Storm

* [Desenvolver topologias Java para o Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usar componentes do Python no Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com o Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Clusters Apache Spark

* [Criar uma aplicação autónoma com o Scala](spark/apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use o Spark no HDInsight para prever os resultados da inspeção de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)

