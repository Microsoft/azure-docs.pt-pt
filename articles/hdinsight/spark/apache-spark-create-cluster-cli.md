---
title: 'Quickstart: Aglomerados Apache Spark com Azure CLI - Azure HDInsight'
description: Este quickstart mostra como usar o Azure CLI para criar um cluster Apache Spark em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 02/03/2020
ms.openlocfilehash: 02113988c76dd2565c7109c2ac18c3d1287189db
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891133"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Quickstart: Criar o cluster Apache Spark em Azure HDInsight usando o Azure CLI

Neste arranque rápido, aprende-se a criar um cluster Apache Spark em Azure HDInsight utilizando a interface de linha de comando Azure (CLI). O Azure HDInsight é um serviço de análise gerido, de espectro completo e de código aberto para empresas. A estrutura Apache Spark para HDInsight permite análise rápida de dados e computação de cluster utilizando o processamento na memória. A CLI do Azure é a experiência de linha de comandos para várias plataformas da Microsoft para a gestão de recursos do Azure.

Se estiver a utilizar vários clusters juntos, vai querer criar uma rede virtual, e se estiver a usar um cluster Spark, também vai querer usar o Conector de Armazém hive. Para mais informações, consulte [Plan a rede virtual para Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) e [integre a Apache Spark e a Apache Hive com o Conector de Armazém hive](../interactive-query/apache-hive-warehouse-connector.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure CLI,](https://docs.microsoft.com/cli/azure/install-azure-cli)se não quiser usar a Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Criar um cluster do Apache Spark

1. Inscreva-se na sua assinatura Azure. Se planeia utilizar a Azure Cloud Shell, selecione **Experimente-a** no canto superior direito do bloco de código seguinte. Caso contrário, insira o seguinte comando:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Definir variáveis ambientais. O uso de variáveis neste quickstart é baseado em Bash. Serão necessárias ligeiras variações para outros ambientes. Substitua o NOME DE GRUPO DE RECURSOS, LOCALIZAÇÃO, CLUSTERNAME, STORAGEACCOUNTNAME e PASSWORD no código abaixo pelos valores pretendidos. Em seguida, introduza os comandos CLI para definir as variáveis ambientais.

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

3. Crie o grupo de recursos entrando no comando abaixo:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. Crie uma conta de armazenamento Azure inserindo o comando abaixo:

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Extrair a chave principal da conta de armazenamento Azure e armazená-la numa variável, inserindo o comando abaixo:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Crie um recipiente de armazenamento Azure entrando no comando abaixo:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Crie o cluster Apache Spark entrando no seguinte comando:

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

## <a name="clean-up-resources"></a>Limpar recursos

Depois de completar o arranque rápido, poderá querer eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Armazenamento Azure, para que possa eliminar com segurança um cluster quando não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que as taxas de armazenamento, faz sentido económico apagar clusters quando não estão em uso.

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

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um cluster Apache Spark em Azure HDInsight utilizando o Azure CLI.  Avance para o próximo tutorial para aprender a usar um cluster HDInsight para executar consultas interativas em dados de amostra.

> [!div class="nextstepaction"]
> [Executar consultas interativas em Apache Spark](./apache-spark-load-data-run-query.md)
