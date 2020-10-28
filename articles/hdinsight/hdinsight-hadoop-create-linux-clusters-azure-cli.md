---
title: Criar clusters Apache Hadoop usando Azure CLI - Azure HDInsight
description: Saiba como criar clusters Azure HDInsight utilizando a plataforma cruzada Azure CLI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 02/03/2020
ms.openlocfilehash: 4daac353c69677021245e95c0b11550372f5d4ec
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748788"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Criar clusters HDInsight utilizando o Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Os passos neste documento walk-through criando um cluster HDInsight 3.6 usando o Azure CLI.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

CLI do Azure. Se ainda não instalou o Azure CLI, consulte [a Instalação do Azure CLI](/cli/azure/install-azure-cli) para obter etapas.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Criar um cluster

1. Faça login na sua assinatura Azure. Se pretender utilizar o Azure Cloud Shell, selecione **Experimente-o** no canto superior direito do bloco de código. Caso contrário, insira o comando abaixo:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Definir variáveis ambientais. O uso de variáveis neste artigo baseia-se em Bash. Serão necessárias ligeiras variações para outros ambientes. Consulte [a az-hdinsight-create](/cli/azure/hdinsight#az-hdinsight-create) para obter uma lista completa de possíveis parâmetros para a criação de clusters.

    |Parâmetro | Descrição |
    |---|---|
    |`--workernode-count`| O número de nós de trabalhadores no aglomerado. Este artigo utiliza a variável `clusterSizeInNodes` à medida que o valor passado para `--workernode-count` . |
    |`--version`| A versão do cluster HDInsight. Este artigo utiliza a variável `clusterVersion` à medida que o valor passado para `--version` . Ver também: [Versões HDInsight suportadas](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Tipo de aglomerado HDInsight, como: hadoop, interactivehive, hbase, kafka, storm, spark, rserver, mlservices.  Este artigo utiliza a variável `clusterType` à medida que o valor passado para `--type` . Consulte também: [Tipos de agrupamento e configuração](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|As versões de vários componentes Hadoop, em versões separadas pelo espaço em formato 'component=version'. Este artigo utiliza a variável `componentVersion` à medida que o valor passado para `--component-version` . Ver também: [Componentes hadoop](./hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions).|

    `RESOURCEGROUPNAME`Substitua, , , , e pelos `LOCATION` `CLUSTERNAME` `STORAGEACCOUNTNAME` `PASSWORD` valores desejados. Alterar valores para as outras variáveis como desejado. Em seguida, insira os comandos da CLI.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'

    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [Crie o grupo de recursos](/cli/azure/group#az-group-create) introduzindo o comando abaixo:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Para uma lista de locais válidos, use o `az account list-locations` comando e, em seguida, use uma das localizações a partir do `name` valor.

4. [Crie uma conta de Armazenamento Azure](/cli/azure/storage/account#az-storage-account-create) introduzindo o comando abaixo:

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Extrair a chave primária da conta de Armazenamento Azure](/cli/azure/storage/account/keys#az-storage-account-keys-list) e armazená-la numa variável introduzindo o comando abaixo:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Crie um recipiente de armazenamento Azure](/cli/azure/storage/container#az-storage-container-create) introduzindo o comando abaixo:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Crie o cluster HDInsight](/cli/azure/hdinsight#az-hdinsight-create) introduzindo o seguinte comando:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > Os clusters HDInsight vêm em vários tipos, que correspondem à carga de trabalho ou tecnologia para a qual o cluster está sintonizado. Não existe um método suportado para criar um cluster que combine vários tipos, tais como Storm e HBase em um cluster.

    Pode levar vários minutos para o processo de criação do cluster estar concluído. Normalmente por volta das 15.

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de concluir o artigo, pode achar conveniente eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Azure Storage, para que possa eliminar com segurança um cluster quando este não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que os encargos de armazenamento, faz sentido económico apagar clusters quando não estão a ser utilizados.

Insira todos ou alguns dos seguintes comandos para remover recursos:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Passos seguintes

Agora que criou com sucesso um cluster HDInsight usando o Azure CLI, use o seguinte para aprender a trabalhar com o seu cluster:

### <a name="apache-hadoop-clusters"></a>Aglomerados apache Hadoop

* [Use a Colmeia Apache com HDInsight](hadoop/hdinsight-use-hive.md)
* [Use MapReduce com HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Aglomerados Apache HBase

* [Começar com Apache HBase em HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicações Java para Apache HBase em HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Aglomerados de tempestade apache

* [Desenvolver topologias de Java para a Tempestade Apache em HDInsight](storm/apache-storm-develop-java-topology.md)
* [Use componentes Python em Apache Storm em HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implementar e monitorizar topologias com a Tempestade Apache em HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
