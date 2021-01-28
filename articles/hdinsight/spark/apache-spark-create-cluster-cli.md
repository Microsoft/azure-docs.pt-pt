---
title: 'Quickstart: Apache Spark clusters com Azure CLI - Azure HDInsight'
description: Este quickstart mostra como usar o Azure CLI para criar um cluster Apache Spark em Azure HDInsight.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 02/03/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: db7e6b8fb6e70d6a64e84da29bbb0a299ed8da2a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940555"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Quickstart: Criar cluster Apache Spark em Azure HDInsight usando Azure CLI

Neste arranque rápido, aprende-se a criar um cluster Apache Spark em Azure HDInsight utilizando a interface de linha de comando Azure (CLI). O Azure HDInsight é um serviço de análise gerido, de espectro completo e de código aberto para empresas. A estrutura Apache Spark para HDInsight permite uma análise rápida de dados e computação de cluster utilizando o processamento na memória. A CLI do Azure é a experiência de linha de comandos para várias plataformas da Microsoft para a gestão de recursos do Azure.

Se estiver a utilizar vários clusters em conjunto, vai querer criar uma rede virtual, e se estiver a usar um cluster Spark também vai querer usar o Conector do Armazém da Colmeia. Para obter mais informações, consulte [Plan a virtual network for Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) and Integrate Apache Spark and Apache [Hive with the Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-apache-spark-cluster"></a>Criar um cluster do Apache Spark

1. Inscreva-se na sua assinatura Azure. Se pretender utilizar o Azure Cloud Shell, selecione **Experimente-o** no canto superior direito do bloco de código que se segue. Caso contrário, insira o seguinte comando:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Definir variáveis ambientais. O uso de variáveis neste quickstart é baseado em Bash. Serão necessárias ligeiras variações para outros ambientes. Substitua o resourcegroupname, a localização, o CLUSTERNAME, o STORAGEACCOUNTNAME e o PASSWORD no código abaixo pelos valores pretendidos. Em seguida, insira os comandos CLI para definir as variáveis ambientais.

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
    export clusterType=spark
    export componentVersion=Spark=2.3
    ```

3. Crie o grupo de recursos introduzindo o comando abaixo:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. Crie uma conta de armazenamento Azure introduzindo o comando abaixo:

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Extrair a chave primária da conta de armazenamento Azure e armazená-la numa variável introduzindo o comando abaixo:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Crie um recipiente de armazenamento Azure introduzindo o comando abaixo:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Crie o cluster Apache Spark introduzindo o seguinte comando:

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

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de completar o arranque rápido, é possível que queira eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Azure Storage, para que possa eliminar com segurança um cluster quando este não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que os encargos de armazenamento, faz sentido económico apagar clusters quando não estão a ser utilizados.

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

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, aprendeu a criar um cluster Apache Spark em Azure HDInsight usando Azure CLI.  Avance para o próximo tutorial para aprender a usar um cluster HDInsight para executar consultas interativas em dados de amostras.

> [!div class="nextstepaction"]
> [Executar consultas interativas sobre Apache Spark](./apache-spark-load-data-run-query.md)
