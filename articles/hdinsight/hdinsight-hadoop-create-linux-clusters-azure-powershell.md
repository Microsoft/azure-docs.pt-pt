---
title: Crie clusters Apache Hadoop usando PowerShell - Azure HDInsight
description: Aprenda a criar apache hadoop, Apache HBase, Apache Storm ou Apache Spark clusters em Linux para HDInsight utilizando o Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 3c93eca493275612ac14a995140b2e91cc40fe98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644669"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Crie clusters baseados em Linux no HDInsight usando o Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O Azure PowerShell é um poderoso ambiente de scripts que podes usar para controlar e automatizar a implementação e gestão das tuas cargas de trabalho no Microsoft Azure. Este documento fornece informações sobre como criar um cluster HDInsight baseado em Linux utilizando o Azure PowerShell. Também inclui um guião de exemplo.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/install-Az-ps) Módulo Az.

## <a name="create-cluster"></a>Criar cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para criar um cluster HDInsight utilizando o Azure PowerShell, deve completar os seguintes procedimentos:

* Criar um grupo de recursos Azure
* Criar uma conta de Armazenamento do Azure
* Crie um recipiente Azure Blob
* Criar um cluster do HDInsight

> [!NOTE]
> A utilização da PowerShell para criar um cluster HDInsight com o Azure Data Lake Storage Gen2 não é suportado atualmente.

O seguinte guião demonstra como criar um novo cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Os valores que especifica para o login do cluster são utilizados para criar a conta de utilizador Hadoop para o cluster. Utilize esta conta para se conectar a serviços hospedados no cluster, tais como UIs web ou APIs REST.

Os valores que especifica para o utilizador SSH são utilizados para criar o utilizador SSH para o cluster. Utilize esta conta para iniciar uma sessão remota de SSH no cluster e executar trabalhos. Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Se planeia utilizar mais de 32 nós de trabalhador (seja na criação de clusters ou através da escala do cluster após a criação), também deve especificar um tamanho do nó da cabeça com pelo menos 8 núcleos e 14 GB de RAM.
>
> Para obter mais informações sobre tamanhos de nós e custos associados, veja os [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Pode levar até 20 minutos para criar um aglomerado.

## <a name="create-cluster-configuration-object"></a>Criar cluster: Configuração do objeto

Também pode criar um objeto de [`New-AzHDInsightClusterConfig`](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) configuração HDInsight utilizando cmdlet. Em seguida, pode modificar este objeto de configuração para permitir opções adicionais de configuração para o seu cluster. Por fim, `-Config` utilize o [`New-AzHDInsightCluster`](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) parâmetro do cmdlet para utilizar a configuração.

O seguinte script cria um objeto de configuração para configurar um Servidor R no tipo de cluster HDInsight. A configuração permite um nó de borda, RStudio, e uma conta de armazenamento adicional.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> A utilização de uma conta de armazenamento num local diferente do cluster HDInsight não é suportada. Ao utilizar este exemplo, crie a conta de armazenamento adicional no mesmo local que o servidor.

## <a name="customize-clusters"></a>Personalizar clusters

* Ver [Personalizar os clusters HDInsight utilizando bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Ver [Personalizar os clusters HDInsight utilizando](hdinsight-hadoop-customize-cluster-linux.md)a Ação do Script .

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Passos seguintes

Agora que criou com sucesso um cluster HDInsight, use os seguintes recursos para aprender a trabalhar com o seu cluster.

### <a name="apache-hadoop-clusters"></a>Aglomerados Apache Hadoop

* [Use a Colmeia Apache com HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar mapeiaReduzir com HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Aglomerados Apache HBase

* [Começa com Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicações Java para Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Aglomerados de tempestades

* [Desenvolver topoologias java para tempestade no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Use componentes Python em Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implementar e monitorizar topoologias com tempestade no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Aglomerados de faíscas Apache

* [Criar uma aplicação autónoma com o Scala](spark/apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark com BI: Realizar análise interativa de dados utilizando spark in HDInsight com ferramentas BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para prever resultados da inspeção alimentar](spark/apache-spark-machine-learning-mllib-ipython.md)