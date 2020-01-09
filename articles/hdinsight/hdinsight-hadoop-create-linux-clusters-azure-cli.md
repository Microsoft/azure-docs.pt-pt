---
title: Criar clusters Apache Hadoop usando o CLI do Azure-Azure HDInsight
description: Saiba como criar clusters do Azure HDInsight usando o CLI do Azure de plataforma cruzada.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 80a13e504b7cb075692256d5c813a95c51002ab6
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495115"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Criar clusters HDInsight usando o CLI do Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

As etapas neste documento orientam a criação de um cluster HDInsight 3,6 usando o CLI do Azure.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

CLI do Azure. Se você ainda não instalou o CLI do Azure, consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter as etapas.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Criar um cluster

1. Faça logon na sua assinatura do Azure. Se você planeja usar Azure Cloud Shell, selecione **Experimente** no canto superior direito do bloco de código. Caso contrário, digite o comando a seguir:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Definir variáveis de ambiente. O uso de variáveis neste artigo baseia-se no bash. Pequenas variações serão necessárias para outros ambientes. Consulte [AZ-hdinsight-Create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) para obter uma lista completa de possíveis parâmetros para a criação do cluster.

    |Parâmetro | Descrição |
    |---|---|
    |`--size`| O número de nós de trabalho no cluster. Este artigo usa a variável `clusterSizeInNodes` como o valor passado para `--size`. |
    |`--version`| A versão do cluster HDInsight. Este artigo usa a variável `clusterVersion` como o valor passado para `--version`. Consulte também: [versões do HDInsight com suporte](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Tipo de cluster HDInsight, como: Hadoop, interactivehive, HBase, Kafka, Storm, Spark, rserver, mlservices.  Este artigo usa a variável `clusterType` como o valor passado para `--type`. Consulte também: [tipos de cluster e configuração](./hdinsight-hadoop-provision-linux-clusters.md#cluster-types).|
    |`--component-version`|As versões de vários componentes do Hadoop, em versões separadas por espaços no formato ' componente = versão '. Este artigo usa a variável `componentVersion` como o valor passado para `--component-version`. Consulte também: [componentes do Hadoop](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    Substitua `RESOURCEGROUPNAME`, `LOCATION`, `CLUSTERNAME`, `STORAGEACCOUNTNAME`e `PASSWORD` pelos valores desejados. Altere os valores para as outras variáveis conforme desejado. Em seguida, insira os comandos da CLI.

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

3. [Crie o grupo de recursos](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) inserindo o comando abaixo:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Para obter uma lista de locais válidos, use o comando `az account list-locations` e, em seguida, use um dos locais do valor `name`.

4. [Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) inserindo o comando a seguir:

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

5. [Extraia a chave primária da conta de armazenamento do Azure](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) e armazene-a em uma variável digitando o comando a seguir:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Crie um contêiner de armazenamento do Azure](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) inserindo o comando a seguir:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Crie o cluster HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) inserindo o seguinte comando:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > Os clusters HDInsight vêm em vários tipos, que correspondem à carga de trabalho ou à tecnologia para a qual o cluster está ajustado. Não há nenhum método com suporte para criar um cluster que combine vários tipos, como o Storm e o HBase em um cluster.

    Pode levar vários minutos para que o processo de criação do cluster seja concluído. Geralmente em cerca de 15.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o artigo, pode achar conveniente eliminar o cluster. Com o HDInsight, seus dados são armazenados no armazenamento do Azure, para que você possa excluir um cluster com segurança quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes mais do que os encargos de armazenamento, ele faz sentido econômico excluir clusters quando eles não estiverem em uso.

Insira todos ou alguns dos comandos a seguir para remover os recursos:

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

Agora que você criou com êxito um cluster HDInsight usando o CLI do Azure, use o seguinte para aprender a trabalhar com o cluster:

### <a name="apache-hadoop-clusters"></a>Clusters Apache Hadoop

* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters do Apache HBase

* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicativos Java para o Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clusters Apache Storm

* [Desenvolver topologias Java para Apache Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usar componentes do Python no Apache Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com Apache Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
