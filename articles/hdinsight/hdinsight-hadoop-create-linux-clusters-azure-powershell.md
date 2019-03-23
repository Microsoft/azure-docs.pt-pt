---
title: Criar clusters do Apache Hadoop com o PowerShell - Azure HDInsight
description: Saiba como criar clusters do Apache Hadoop, Apache HBase, Apache Storm ou do Apache Spark no Linux para o HDInsight com o Azure PowerShell.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 264b4737974010baffd82b38275a8fe56163e1f2
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361443"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Criar clusters baseados em Linux no HDInsight com o Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O Azure PowerShell é um ambiente de criação de scripts poderoso que pode utilizar para controlar e automatizar a implementação e gestão das cargas de trabalho no Microsoft Azure. Este documento fornece informações sobre como criar um cluster do HDInsight baseado em Linux com o Azure PowerShell. Ele também inclui um script de exemplo.

> [!NOTE]  
> O Azure PowerShell só está disponível nos clientes do Windows. Se estiver a utilizar um cliente Linux, Unix ou Mac OS X, consulte [criar um cluster do HDInsight baseado em Linux com a CLI clássica do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) para obter informações sobre como utilizar a CLI clássica para criar um cluster.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tem de ter o seguinte antes de iniciar este procedimento:

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-Az-ps)

    > [!IMPORTANT]  
    > O suporte do Azure PowerShell para gerir recursos do HDInsight com o Gestor de Serviços do Azure está **preterido**, e foi removido a 1 de janeiro de 2017. Os passos neste documento utilizam os novos cmdlets do HDInsight que funcionam com o Azure Resource Manager.
    >
    > Siga os passos em [instalar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) para instalar a versão mais recente do Azure PowerShell. Se tiver scripts que tenham de ser modificados para utilizar os novos cmdlets que funcionam com o Azure Resource Manager, veja [Migrar para as ferramentas de desenvolvimento baseadas no Azure Resource Manager para clusters do HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) para obter mais informações.

## <a name="create-cluster"></a>Criar cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para criar um cluster do HDInsight com o Azure PowerShell, tem de concluir os seguintes procedimentos:

* Criar um grupo de recursos do Azure
* Criar uma conta de Armazenamento do Azure
* Criar um contentor de Blobs do Azure
* Criar um cluster do HDInsight

O script a seguir demonstra como criar um novo cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Os valores que especificou para o início de sessão do cluster são utilizados para criar a conta de utilizador do Hadoop para o cluster. Utilize esta conta para ligar a serviços alojados no cluster, como web interfaces do usuário ou de REST APIs.

Os valores que especificou para o utilizador SSH são utilizados para criar o utilizador SSH para o cluster. Utilize esta conta para iniciar uma sessão remota do SSH no cluster e executar tarefas. Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Se planeja usar mais do que 32 nós de trabalho (seja durante a criação de cluster ou ao dimensionar o cluster após a criação), também tem de especificar um tamanho de nó principal com, pelo menos, 8 núcleos e 14 GB de RAM.
>
> Para obter mais informações sobre tamanhos de nós e custos associados, veja os [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Pode demorar até 20 minutos para criar um cluster.

## <a name="create-cluster-configuration-object"></a>Crie cluster: Objeto de configuração

Também pode criar um através de objeto de configuração do HDInsight `New-AzHDInsightClusterConfig` cmdlet. Em seguida, pode modificar este objeto de configuração para ativar as opções de configuração adicionais para o seu cluster. Por último, utilize o `-Config` parâmetro do `New-AzHDInsightCluster` cmdlet para utilizar a configuração.

O seguinte script cria um objeto de configuração para configurar um servidor R no tipo de cluster do HDInsight. A configuração permite que um nó de extremidade, o RStudio e uma conta de armazenamento adicional.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-98)]

> [!WARNING]  
> Não é suportada a utilizar uma conta de armazenamento numa localização diferente do que o cluster do HDInsight. Ao utilizar este exemplo, crie a conta de armazenamento adicional na mesma localização que o servidor.

## <a name="customize-clusters"></a>Personalizar clusters

* Ver [clusters do HDInsight personalizar com o arranque de](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Ver [HDInsight personalizar clusters com ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Passos Seguintes

Agora que criou com êxito um cluster do HDInsight, utilize os seguintes recursos para aprender a trabalhar com o seu cluster.

### <a name="apache-hadoop-clusters"></a>Clusters do Apache Hadoop

* [Utilizar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilizar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters do Apache HBase

* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicações de Java para o Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters do Storm

* [Desenvolver topologias de Java para Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Utilizar componentes de Python no Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implementar e monitorizar topologias Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Clusters do Apache Spark

* [Criar uma aplicação autónoma com o Scala](spark/apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Apache Spark com o Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark com BI: Efetuar análise de dados interativa com o Spark no HDInsight com ferramentas de BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](spark/apache-spark-machine-learning-mllib-ipython.md)

