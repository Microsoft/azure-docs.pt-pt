---
title: Apache Hadoop & armazenamento de transferência segura-Azure HDInsight
description: Saiba como criar clusters do HDInsight com contas de armazenamento do Azure com transferência segura ativada.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 09a6b158c4390f881754c90d52a476f0bc249a5a
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947644"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Criar Apache Hadoop cluster com contas de armazenamento de transferência segura no Azure HDInsight

A funcionalidade [Transferência segura necessária](../storage/common/storage-require-secure-transfer.md) melhora a segurança da sua conta da Armazenamento do Azure ao impor todos os pedidos à sua conta através de uma ligação segura. Esta funcionalidade e o esquema wasbs só são suportados pelo clusters do HDInsight versão 3.6 ou mais recente.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter:

* Assinatura do Azure: para criar uma conta de avaliação gratuita de um mês, navegue até [Azure.Microsoft.com/Free](https://azure.microsoft.com/free).
* Uma conta de armazenamento do Azure com transferência segura habilitada. Para obter instruções, veja [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md) e [Requer transferência segura](../storage/common/storage-require-secure-transfer.md). Habilitar a transferência de armazenamento seguro depois de criar um cluster requer etapas adicionais não abordadas neste artigo.
* Um contêiner de BLOBs na conta de armazenamento.

## <a name="create-cluster"></a>Criar cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Nesta secção, vai criar um cluster do Hadoop no HDInsight com um [modelo do Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). O modelo está localizado no [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/). A experiência do modelo do Resource Manager não é necessária para seguir este artigo. Para outros métodos de criação de cluster e noções básicas sobre as propriedades usadas neste artigo, consulte [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem seguinte para iniciar sessão no Azure e abrir o modelo do Azure Resource Manager no Portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Siga as instruções para criar o cluster com as seguintes especificações:

    * Especifique o HDInsight versão 3.6. É necessária a versão 3.6 ou mais recente.
    * Especifique uma conta de armazenamento com transferência segura ativada.
    * Utilize um nome abreviado para a conta de armazenamento.
    * A conta de armazenamento e o contentor de blobs têm de ser criados previamente.

      Para obter instruções, veja [Criar cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

Se utilizar a ação de script para fornecer os seus próprios ficheiros de configuração, tem de utilizar wasbs nas seguintes definições:

* fs.defaultFS (site principal)
* spark.eventLog.dir
* spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Adicionar mais contas de armazenamento

Existem várias opções para adicionar mais contas de armazenamento com transferência segura ativada:

* Modifique o modelo do Azure Resource Manager na última secção.
* Crie um cluster através do [portal do Azure](https://portal.azure.com) e especifique a conta de armazenamento associada.
* Utilize a ação de script para adicionar mais contas de armazenamento com transferência segura ativada a um cluster do HDInsight existente. Para obter mais informações, veja [Adicionar mais contas de armazenamento ao HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a criar um cluster HDInsight e a habilitar a transferência segura para as contas de armazenamento.

Saiba mais sobre como analisar os dados com o HDInsight, veja os seguintes artigos:

* Para saber mais sobre como usar [Apache Hive](https://hive.apache.org/) com o hdinsight, incluindo como executar consultas de hive do Visual Studio, consulte [usar o Apache Hive com o hdinsight](hadoop/hdinsight-use-hive.md).
* Para saber mais sobre o [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), uma maneira de escrever programas que processam dados no Hadoop, consulte [usar o Apache Hadoop MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md).
* Para saber mais sobre como usar as ferramentas do HDInsight para Visual Studio para analisar dados no HDInsight, consulte Introdução ao [uso do Visual Studio Apache Hadoop Tools para HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Para saber mais sobre como o HDInsight armazena os dados ou como obter dados para o HDInsight, veja os seguintes artigos:

* Para obter informações sobre como o HDInsight utiliza o Armazenamento do Azure, consulte [Utilizar o Armazenamento do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como carregar dados para o HDInsight, veja [Upload data to HDInsight](hdinsight-upload-data.md) (Carregar dados para o HDInsight).

Para saber mais sobre como criar ou gerir um cluster do HDInsight, veja os seguintes artigos:

* Para saber mais sobre como gerenciar seu cluster HDInsight baseado em Linux, consulte [gerenciar clusters hdinsight usando o Apache Ambari](hdinsight-hadoop-manage-ambari.md).
* Para saber mais sobre as opções que pode selecionar ao criar um cluster do HDInsight, consulte [Criar HDInsight no Linux utilizando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).
* Se você estiver familiarizado com o Linux e Apache Hadoop, mas quiser saber sobre as especificações sobre o Hadoop no HDInsight, consulte [trabalhando com o HDInsight no Linux](hdinsight-hadoop-linux-information.md). Este artigo disponibiliza informações como:

  * URLs para serviços hospedados no cluster, como [Apache Ambari](https://ambari.apache.org/) e [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)
  * O local dos arquivos de [Apache Hadoop](https://hadoop.apache.org/) e exemplos no sistema de arquivos local
  * O uso do armazenamento do Azure (WASB) em vez de [Apache HADOOP HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) como o armazenamento de dados padrão
