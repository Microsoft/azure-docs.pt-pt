---
title: Criar clusters Hadoop com contas de armazenamento de transferência segura no Azure HDInsight
description: Saiba como criar clusters do HDInsight com contas de armazenamento do Azure com transferência segura ativada.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/24/2018
ms.openlocfilehash: 10ec4b55bab741f19adaf193295659b7876fe02c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64685226"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Criar cluster do Apache Hadoop com transferência segura contas de armazenamento no Azure HDInsight

A funcionalidade [Transferência segura necessária](../storage/common/storage-require-secure-transfer.md) melhora a segurança da sua conta da Armazenamento do Azure ao impor todos os pedidos à sua conta através de uma ligação segura. Esta funcionalidade e o esquema wasbs só são suportados pelo clusters do HDInsight versão 3.6 ou mais recente.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter os seguintes elementos:

* **Subscrição do Azure**: Para criar uma conta de avaliação gratuita de um mês, navegue até [azure.microsoft.com/free](https://azure.microsoft.com/free).
* **Conta de Armazenamento do Azure com transferência segura ativada**. Para obter instruções, veja [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md) e [Requer transferência segura](../storage/common/storage-require-secure-transfer.md).
* **Contentor de blobs na conta de armazenamento**.

## <a name="create-cluster"></a>Criar cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Nesta secção, vai criar um cluster do Hadoop no HDInsight com um [modelo do Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). O modelo está localizado em [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/). Não precisa de ter experiência no modelo do Resource Manager para seguir este tutorial. Para conhecer outros métodos de criação de clusters e compreender as propriedades utilizadas neste tutorial, consulte [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem seguinte para iniciar sessão no Azure e abrir o modelo do Azure Resource Manager no Portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Siga as instruções para criar o cluster com as seguintes especificações: 

    - Especifique o HDInsight versão 3.6. É necessária a versão 3.6 ou mais recente.
    - Especifique uma conta de armazenamento com transferência segura ativada.
    - Utilize um nome abreviado para a conta de armazenamento.
    - A conta de armazenamento e o contentor de blobs têm de ser criados previamente.

      Para obter instruções, veja [Criar cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

Se utilizar a ação de script para fornecer os seus próprios ficheiros de configuração, tem de utilizar wasbs nas seguintes definições:

- fs.defaultFS (site principal)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Adicionar mais contas de armazenamento

Existem várias opções para adicionar mais contas de armazenamento com transferência segura ativada:

- Modifique o modelo do Azure Resource Manager na última secção.
- Crie um cluster através do [portal do Azure](https://portal.azure.com) e especifique a conta de armazenamento associada.
- Utilize a ação de script para adicionar mais contas de armazenamento com transferência segura ativada a um cluster do HDInsight existente. Para obter mais informações, veja [Adicionar mais contas de armazenamento ao HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu a criar um cluster do HDInsight e a ativar a transferência segura para as contas de armazenamento.

Saiba mais sobre como analisar os dados com o HDInsight, veja os seguintes artigos:

* Para obter mais informações sobre como utilizar [Apache Hive](https://hive.apache.org/) com o HDInsight, incluindo como executar consultas do Hive a partir do Visual Studio, consulte [utilizar o Apache Hive com HDInsight][hdinsight-use-hive].
* Para saber mais sobre [Apache Pig](https://pig.apache.org/), uma linguagem utilizada para transformar dados, veja [utilizar o Apache Pig com o HDInsight][hdinsight-use-pig].
* Para saber mais sobre [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), uma forma de escrever programas que processam dados no Hadoop, consulte [utilizar o Apache Hadoop MapReduce com o HDInsight][hdinsight-use-mapreduce].
* Para saber mais sobre como utilizar as ferramentas do HDInsight para Visual Studio para analisar dados no HDInsight, veja [começar a utilizar as ferramentas do Visual Studio Apache Hadoop para HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Para saber mais sobre como o HDInsight armazena os dados ou como obter dados para o HDInsight, veja os seguintes artigos:

* Para obter informações sobre como o HDInsight utiliza o Armazenamento do Azure, consulte [Utilizar o Armazenamento do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como carregar dados para o HDInsight, consulte [Carregar dados para o HDInsight][hdinsight-upload-data].

Para saber mais sobre como criar ou gerir um cluster do HDInsight, veja os seguintes artigos:

* Para saber mais sobre a gestão do seu cluster do HDInsight baseado em Linux, veja [clusters do HDInsight gerir com o Apache Ambari](hdinsight-hadoop-manage-ambari.md).
* Para saber mais sobre as opções que pode selecionar ao criar um cluster do HDInsight, consulte [Criar HDInsight no Linux utilizando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).
* Se estiver familiarizado com o Linux e Apache Hadoop, mas pretende obter informações específicas sobre o Hadoop no HDInsight, veja [trabalhar com o HDInsight no Linux](hdinsight-hadoop-linux-information.md). Este artigo disponibiliza informações como:

  * URLs para os serviços alojados no cluster, tal como [Apache Ambari](https://ambari.apache.org/) e [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)
  * A localização dos [Apache Hadoop](https://hadoop.apache.org/) ficheiros e os exemplos no sistema de ficheiros local
  * A utilização do armazenamento do Azure (WASB) em vez de [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) como os dados de predefinição armazenar

[1]: ../HDInsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
