---
title: Criar clusters do Apache Hadoop com a CLI do Azure clássica - Azure HDInsight
description: Saiba como criar clusters do HDInsight com a CLI clássica do Azure de várias plataformas.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 21985b009694dc5a21c65d4c9dc9536cf6c01a0e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727074"
---
# <a name="create-hdinsight-clusters-using-the-azure-classic-cli"></a>Criar clusters do HDInsight com a CLI clássica do Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Os passos nestas instruções de documento, criar um cluster de HDInsight 3.5, com a CLI clássica do Azure.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Azure CLI clássica**. Os passos neste documento foram testados pela última vez com a CLI clássica do Azure versão 0.10.14.

## <a name="log-in-to-your-azure-subscription"></a>Inicie sessão na subscrição do Azure

Siga os passos documentados em [ligar a uma subscrição do Azure da Interface de linha de comandos do Azure](/cli/azure/authenticate-azure-cli) e ligue à sua subscrição com o **início de sessão** método.

## <a name="create-a-cluster"></a>Criar um cluster

Os seguintes passos devem ser efetuados a partir de uma linha de comando, como o PowerShell ou Bash.

1. Utilize o seguinte comando para autenticar a sua subscrição do Azure:

        azure login

    São-lhe pedido para fornecer o nome e a palavra-passe. Se tiver várias subscrições do Azure, utilize `azure account set <subscriptionname>` para configurar a subscrição que utilizam os comandos da CLI clássicos.

2. Mude para o modo Azure Resource Manager utilizando o seguinte comando:

        azure config mode arm

3. Crie um grupo de recursos. Este grupo de recursos contém o cluster do HDInsight e associado à conta de armazenamento.

        azure group create groupname location

    * Substitua `groupname` com um nome exclusivo para o grupo.

    * Substitua `location` com a região geográfica na qual pretende criar o grupo no.

       Para obter uma lista de localizações válidas, utilize o `azure location list` de comandos e, em seguida, utilize uma das localizações do `Name` coluna.

4. Criar uma conta de armazenamento. Esta conta de armazenamento é utilizada como armazenamento predefinido para o cluster do HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Substitua `groupname` com o nome do grupo criado no passo anterior.

    * Substitua `location` com a mesma localização que utilizou no passo anterior.

    * Substitua `storagename` com um nome exclusivo para a conta de armazenamento.

        > [!NOTE]  
        > Para obter mais informações sobre os parâmetros utilizados neste comando, utilize `azure storage account create -h` para ver a ajuda deste comando.

5. Obter a chave utilizada para aceder à conta de armazenamento.

        azure storage account keys list -g groupname storagename

    * Substitua `groupname` com o nome do grupo de recursos.
    * Substitua `storagename` com o nome da conta de armazenamento.

      Guarde os dados que são retornados, o `key` o valor para `key1`.

6. Crie um cluster do HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Substitua `groupname` com o nome do grupo de recursos.

    * Substitua `Hadoop` com o tipo de cluster que pretende criar. Por exemplo, `Hadoop`, `HBase`, `Kafka`, `Spark`, ou `Storm`.

      > [!IMPORTANT]  
      > HDInsight clusters são fornecidos em vários tipos que correspondem à carga de trabalho ou tecnologia concebido para o cluster. Não existe nenhum método suportado para criar um cluster que combina vários tipos, como o Storm e HBase num cluster.

    * Substitua `location` com a mesma localização que utilizou nos passos anteriores.

    * Substitua `storagename` com o nome de conta de armazenamento.

    * Substitua `storagekey` com a chave de obteve no passo anterior.

    * Para o `--defaultStorageContainer` parâmetro, utilizar o mesmo nome que estiver a utilizar para o cluster.

    * Substitua `admin` e `httppassword` com o nome e a palavra-passe que pretende utilizar quando aceder ao cluster através de HTTPS.

    * Substitua `sshuser` e `sshuserpassword` com o nome de utilizador e palavra-passe que pretende utilizar quando aceder ao cluster através de SSH

      > [!IMPORTANT]  
      > Este exemplo cria um cluster com dois nós de trabalho. Também pode alterar o número de nós de trabalho após a criação do cluster ao realizar operações de dimensionamento. Se pretender utilizar mais do que 32 nós de trabalho, em seguida, tem de selecionar um tamanho de nó principal com, pelo menos, 8 núcleos e 14 GB de RAM. Pode definir o tamanho de nó principal usando o `--headNodeSize` parâmetro durante a criação do cluster.
      >
      > Para obter mais informações sobre tamanhos de nós e custos associados, veja os [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
      
      Pode demorar alguns minutos até que o processo de criação de cluster concluir. Normalmente, cerca de 15.

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Passos Seguintes

Agora que criou com êxito um cluster do HDInsight com a CLI clássica, utilize o seguinte para aprender a trabalhar com o seu cluster:

### <a name="apache-hadoop-clusters"></a>Clusters do Apache Hadoop

* [Utilizar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilizar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters do Apache HBase

* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicações de Java para o Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clusters do Apache Storm

* [Desenvolver topologias de Java para Apache Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Utilizar componentes de Python no Apache Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implementar e monitorizar topologias Apache Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
