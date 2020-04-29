---
title: Crie clusters Apache Hadoop usando O ClI Azure - Azure HDInsight
description: Aprenda a criar clusters Azure HDInsight utilizando o Azure CLI de plataforma cruzada.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/03/2020
ms.openlocfilehash: b9d935e72c67b78484337e39e0897d4962340636
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77199046"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Criar clusters HDInsight utilizando o Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Os passos neste documento através da criação de um cluster HDInsight 3.6 utilizando o Azure CLI.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Azure CLI. Se ainda não instalou o Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) para os passos.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Criar um cluster

1. Faça login na sua assinatura Azure. Se planeia utilizar a Azure Cloud Shell, então selecione **Experimente no** canto superior direito do bloco de código. Caso contrário, insira o comando abaixo:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Definir variáveis ambientais. O uso de variáveis neste artigo é baseado em Bash. Serão necessárias ligeiras variações para outros ambientes. Consulte [az-hdinsight-create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) para obter uma lista completa de possíveis parâmetros para a criação de clusters.

    |Parâmetro | Descrição |
    |---|---|
    |`--workernode-count`| O número de nós operários no agrupamento. Este artigo usa `clusterSizeInNodes` a variável `--workernode-count`à medida que o valor passou para . |
    |`--version`| A versão do cluster HDInsight. Este artigo usa `clusterVersion` a variável `--version`à medida que o valor passou para . Ver também: [Versões HDInsight suportadas](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Tipo de cluster HDInsight, como: hadoop, interactivehive, hbase, kafka, tempestade, faísca, rserver, mlservices.  Este artigo usa `clusterType` a variável `--type`à medida que o valor passou para . Ver também: [Tipos de cluster e configuração](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|As versões de vários componentes Hadoop, em versões separadas pelo espaço em formato 'component=version'. Este artigo usa `componentVersion` a variável `--component-version`à medida que o valor passou para . Ver também: [Componentes Hadoop](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    Substitua, `CLUSTERNAME` `RESOURCEGROUPNAME` `LOCATION` `STORAGEACCOUNTNAME`, `PASSWORD` e com os valores desejados. Alterar valores para as outras variáveis como desejado. Em seguida, introduza os comandos CLI.

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

3. [Crie o grupo de recursos](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) entrando no comando abaixo:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Para uma lista de locais `az account list-locations` válidos, utilize o comando e, em seguida, utilize uma das localizações a partir do `name` valor.

4. [Crie uma conta de Armazenamento Azure](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) inserindo o comando abaixo:

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

5. [Extrair a chave principal da conta de Armazenamento Azure](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) e armazená-la numa variável, inserindo o comando abaixo:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Crie um recipiente](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) de armazenamento Azure entrando no comando abaixo:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Crie o cluster HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) introduzindo o seguinte comando:

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

    Pode levar vários minutos para que o processo de criação do cluster seja concluído. Normalmente por volta das 15.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o artigo, pode achar conveniente eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Armazenamento Azure, para que possa eliminar com segurança um cluster quando não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que as taxas de armazenamento, faz sentido económico apagar clusters quando não estão em uso.

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

Agora que criou com sucesso um cluster HDInsight utilizando o Azure CLI, use o seguinte para aprender a trabalhar com o seu cluster:

### <a name="apache-hadoop-clusters"></a>Aglomerados Apache Hadoop

* [Use a Colmeia Apache com HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar mapeiaReduzir com HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Aglomerados Apache HBase

* [Começa com Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicações Java para Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Aglomerados de tempestade apache

* [Desenvolva topoologias java para tempestade Apache no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Utilize componentes Python na Tempestade Apache no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implementar e monitorizar topoologias com tempestade Apache no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
