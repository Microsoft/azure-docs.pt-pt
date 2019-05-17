---
title: Criar clusters do Apache Hadoop com a CLI do Azure - Azure HDInsight
description: Saiba como criar clusters do HDInsight com a CLI do Azure de várias plataformas.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: hrasheed
ms.openlocfilehash: 0a278cd98b0dd6c6d8f0fe9bfee81e5bafd4f543
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597698"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Criar clusters do HDInsight com a CLI do Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Os passos nestas instruções de documento, criar um cluster de HDInsight 3.6 com a CLI do Azure.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

CLI do Azure. Se ainda não instalou a CLI do Azure, consulte [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter os passos.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Criar um cluster

1. Início de sessão para a sua subscrição do Azure. Se planear utilizar o Azure Cloud Shell, em seguida, selecione simplesmente **experimente** no canto superior direito do bloco de código. Caso contrário, introduza o comando abaixo:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Definir variáveis de ambiente. A utilização de variáveis, este artigo baseia-se no Bash. Pequenas variações serão necessários para outros ambientes. Ver [az-hdinsight-criar](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) para uma lista completa de parâmetros possíveis para a criação do cluster.

    |Parâmetro | Descrição |
    |---|---|
    |`--size`| O número de nós de trabalho no cluster. Este artigo utiliza a variável `clusterSizeInNodes` como o valor transmitido para `--size`. |
    |`--version`| A versão de cluster do HDInsight. Este artigo utiliza a variável `clusterVersion` como o valor transmitido para `--version`. Veja também: [HDInsight versões suportadas](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Como o tipo de cluster do HDInsight: hadoop, interactivehive, hbase, kafka, storm, spark, rserver, mlservices.  Este artigo utiliza a variável `clusterType` como o valor transmitido para `--type`. Veja também: [Tipos e a configuração de cluster](./hdinsight-hadoop-provision-linux-clusters.md#cluster-types).|
    |`--component-version`|As versões de vários componentes do Hadoop, nas versões separadas por espaços na "componente = versão" formato. Este artigo utiliza a variável `componentVersion` como o valor transmitido para `--component-version`. Veja também: [Componentes do Hadoop](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    Substitua `RESOURCEGROUPNAME`, `LOCATION`, `CLUSTERNAME`, `STORAGEACCOUNTNAME`, e `PASSWORD` com os valores pretendidos. Alterar valores para as outras variáveis conforme pretendido. Em seguida, introduza os comandos da CLI.

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

3. [Criar o grupo de recursos](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) introduzindo o comando abaixo:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Para obter uma lista de localizações válidas, utilize o `az account list-locations` de comandos e, em seguida, utilize uma das localizações do `name` valor.

4. [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) introduzindo o comando abaixo:

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

5. [Extrair a chave primária da conta de armazenamento do Azure](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) e armazená-lo numa variável, introduzindo o comando abaixo:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Criar um contentor de armazenamento do Azure](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) introduzindo o comando abaixo:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Criar o cluster de HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) introduzindo o seguinte comando:

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
    > HDInsight clusters são fornecidos em vários tipos que correspondem à carga de trabalho ou tecnologia concebido para o cluster. Não existe nenhum método suportado para criar um cluster que combina vários tipos, como o Storm e HBase num cluster.

    Pode demorar alguns minutos até que o processo de criação de cluster concluir. Normalmente, cerca de 15.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o artigo, pode achar conveniente eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Introduza todos ou alguns dos comandos seguintes para remover recursos:

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

## <a name="next-steps"></a>Passos Seguintes

Agora que criou com êxito um cluster do HDInsight com a CLI do Azure, utilize o seguinte para aprender a trabalhar com o seu cluster:

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
