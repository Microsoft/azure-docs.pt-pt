---
title: 'Quickstart: Aglomerados Apache Spark com Azure CLI - Azure HDInsight'
description: Este quickstart mostra como usar o Azure CLI para criar um cluster Apache Spark em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 02/03/2020
ms.openlocfilehash: e4679d5a04be7b8c0145fd93818e4187170b4194
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049688"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Quickstart: Criar o cluster Apache Spark em Azure HDInsight usando o Azure CLI

Neste arranque rápido, aprende-se a criar um cluster Apache Spark em Azure HDInsight utilizando a interface de linha de comando Azure (CLI). O Azure HDInsight é um serviço de análise gerido, de espectro completo e de código aberto para empresas. A estrutura Apache Spark para HDInsight permite análise rápida de dados e computação de cluster utilizando o processamento na memória. A CLI do Azure é a experiência de linha de comandos para várias plataformas da Microsoft para a gestão de recursos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure CLI,](https://docs.microsoft.com/cli/azure/install-azure-cli)se não quiser usar a Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Criar um cluster do Apache Spark

1. Entre em sua assinatura do Azure. Se planeia utilizar a Azure Cloud Shell, selecione **Experimente-a** no canto superior direito do bloco de código seguinte. Caso contrário, insira o seguinte comando:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Definir variáveis de ambiente. O uso de variáveis neste quickstart é baseado em Bash. Pequenas variações serão necessárias para outros ambientes. Substitua o NOME DE GRUPO DE RECURSOS, LOCALIZAÇÃO, CLUSTERNAME, STORAGEACCOUNTNAME e PASSWORD no código abaixo pelos valores pretendidos. Em seguida, introduza os comandos CLI para definir as variáveis ambientais.

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

Depois de completar o arranque rápido, poderá querer eliminar o cluster. Com o HDInsight, seus dados são armazenados no armazenamento do Azure, para que você possa excluir um cluster com segurança quando ele não estiver em uso. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Como os encargos para o cluster são muitas vezes mais do que os encargos de armazenamento, ele faz sentido econômico excluir clusters quando eles não estiverem em uso.

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

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um cluster Apache Spark em Azure HDInsight utilizando o Azure CLI.  Avance para o próximo tutorial para aprender a usar um cluster HDInsight para executar consultas interativas em dados de amostra.

> [!div class="nextstepaction"]
> [Executar consultas interativas em Apache Spark](./apache-spark-load-data-run-query.md)
