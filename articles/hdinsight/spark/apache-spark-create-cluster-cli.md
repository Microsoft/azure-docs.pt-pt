---
title: Criar um cluster do Apache Spark no HDInsight do Azure com a CLI do Azure
description: Este início rápido mostra como utilizar a CLI do Azure para criar um cluster do Apache Spark no HDInsight do Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 05/09/2019
ms.author: hrasheed
ms.openlocfilehash: b9478ca8e1b31c1761e063a6789e96043f9a2c68
ms.sourcegitcommit: 9e8dfa1169a55c3c8af93a6c5f4e0dace4de48b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65556833"
---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight-with-azure-cli"></a>Criar um cluster do Apache Spark no HDInsight do Azure com a CLI do Azure

Neste guia de introdução, saiba como criar um cluster do Apache Spark no HDInsight do Azure com a CLI do Azure. O Apache Spark permite uma análise de dados e computação de clusters rápidas através do processamento dentro da memória. O [interface de linha de comandos (CLI) do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) é a experiência de linha de comandos para várias plataformas da Microsoft para gerir recursos do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

CLI do Azure. Se ainda não instalou a CLI do Azure, consulte [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter os passos.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Criar um cluster do Apache Spark

1. Inicie sessão na sua subscrição do Azure. Se planear utilizar o Azure Cloud Shell, em seguida, selecione simplesmente **experimente** no canto superior direito do bloco de código. Caso contrário, introduza o comando abaixo:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Definir variáveis de ambiente. A utilização de variáveis neste início rápido baseia-se no Bash. Pequenas variações serão necessários para outros ambientes. Substitua RESOURCEGROUPNAME, localização, CLUSTERNAME, STORAGEACCOUNTNAME e palavra-passe no fragmento de código abaixo com os valores pretendidos. Em seguida, introduza os comandos da CLI para definir as variáveis de ambiente.

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

3. Crie o grupo de recursos ao introduzir o comando abaixo:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. Crie uma conta de armazenamento do Azure ao introduzir o comando abaixo:

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Extrair a chave primária da conta de armazenamento do Azure e o armazenamos numa variável, introduzindo o comando abaixo:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Crie um contentor de armazenamento do Azure ao introduzir o comando abaixo:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Crie o cluster do Apache Spark, introduzindo o seguinte comando:

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

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o início rápido, pode querer eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

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

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a criar um cluster do Apache Spark no HDInsight do Azure com a CLI do Azure.  Prossiga para o tutorial seguinte para saber como utilizar um cluster do Spark no HDInsight para executar consultas interativas nos dados de exemplo.

> [!div class="nextstepaction"]
> [Executar consultas interativas no Apache Spark](./apache-spark-load-data-run-query.md)
