---
title: 'Tutorial: Criar um cluster ativado por proxy Apache Kafka REST no HDInsight utilizando o Azure CLI'
description: Aprenda a executar operações apache kafka usando um proxy Kafka REST no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 6ba5e433839d1f27c9522749fd7a8831c7243aae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201886"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Tutorial: Criar um cluster ativado por proxy Apache Kafka REST no HDInsight utilizando o Azure CLI

Neste tutorial, aprende-se a criar um cluster [ativado](./rest-proxy.md) por proxy Apache Kafka REST em Azure HDInsight utilizando a interface de linha de comando Azure (CLI). O Azure HDInsight é um serviço de análise gerido, de espectro completo e de código aberto para empresas. O Apache Kafka é uma plataforma de código aberto de transmissão em fluxo distribuída. É frequentemente utilizado como mediador de mensagens, uma vez que fornece funcionalidades semelhantes a uma fila de mensagens de publicação-subscrição. Kafka REST Proxy permite-lhe interagir com o seu cluster Kafka através de um [REST API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) sobre HTTP. A CLI do Azure é a experiência de linha de comandos para várias plataformas da Microsoft para a gestão de recursos do Azure.

Só os recursos dentro da mesma rede virtual podem aceder à API do Apache Kafka. Pode aceder diretamente ao cluster utilizando SSH. Para ligar outros serviços, redes ou máquinas virtuais ao Apache Kafka, tem primeiro de criar uma rede virtual e, em seguida, criar os recursos dentro da rede. Para mais informações, consulte [Connect to Apache Kafka utilizando uma rede virtual](./apache-kafka-connect-vpn-gateway.md).

Neste tutorial, aprende-se:

> [!div class="checklist"]
> * Pré-requisitos para procuração Kafka REST
> * Crie um cluster Apache Kafka usando o Azure CLI

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um pedido registado na Azure AD. As aplicações de clientes que escrever para interagir com o representante do Kafka REST usarão o ID e o segredo desta aplicação para autenticar o Azure. Para mais informações, consulte [O Registo de uma aplicação com a plataforma de identidade microsoft](../../active-directory/develop/quickstart-register-app.md).

* Um grupo de segurança Azure AD com a sua aplicação registada como membro. Este grupo de segurança será utilizado para controlar quais as aplicações autorizadas a interagir com o representante do REST. Para obter mais informações sobre a criação de grupos DeA Azure, consulte [Criar um grupo básico e adicionar membros usando o Diretório Ativo Azure](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* Azure CLI. Certifique-se de que tem pelo menos a versão 2.0.79. Ver [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-an-apache-kafka-cluster"></a>Criar um cluster do Apache Kafka

1. Inscreva-se na sua assinatura Azure.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Definir variáveis ambientais. O uso de variáveis neste tutorial é baseado em Bash. Serão necessárias ligeiras variações para outros ambientes.

    |Variável | Descrição |
    |---|---|
    |resourceGroupName|Substitua o NOME DE GRUPO DE RECURSOS pelo nome do seu novo grupo de recursos.|
    |localização|Substitua o LOCAL por uma região onde o cluster será criado. Para uma lista de locais `az account list-locations` válidos, utilize o comando|
    |clusterName|Substitua o CLUSTERNAME por um nome globalmente único para o seu novo cluster.|
    |storageAccount|Substitua o STORAGEACCOUNTNAME por um nome para a sua nova conta de armazenamento.|
    |httpPassword|Substitua a PASSWORD por uma palavra-passe para o login do cluster, **administrador**.|
    |sshPassword|Substitua a PASSWORD por uma palavra-passe para o nome de utilizador da concha segura, **sshuser**.|
    |securityGroupName|Substitua o NOME DE SEGURANÇA pelo nome do grupo de segurança AAD do cliente para Kafka Rest Proxy. A variável será `--kafka-client-group-name` passada para `az-hdinsight-create`o parâmetro para .|
    |securityGroupID|Substitua o SECURITYGROUPID pelo ID do grupo de segurança AAD para kafka Rest Proxy. A variável será `--kafka-client-group-id` passada para `az-hdinsight-create`o parâmetro para .|
    |recipiente de armazenamento|Recipiente de armazenamento que o cluster utilizará, deixe como está para este tutorial. Esta variável será definida com o nome do cluster.|
    |workernodeCount|Número de nós operários no agrupamento, deixe como é para este tutorial. Para garantir uma elevada disponibilidade, Kafka requer um mínimo de 3 nós de trabalhador|
    |clusterType|Tipo de cluster HDInsight, deixe como está para este tutorial.|
    |clusterVersion|Versão de cluster HDInsight, deixe como está para este tutorial. Kafka Rest Proxy requer uma versão mínima de cluster de 4.0.|
    |componenteVersão|Versão Kafka, deixe como é para este tutorial. Kafka Rest Proxy requer uma versão componente mínima de 2.1.|

    Atualize as variáveis com os valores desejados. Em seguida, introduza os comandos CLI para definir as variáveis ambientais.

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. [Crie o grupo de recursos](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) entrando no comando abaixo:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Crie uma conta de Armazenamento Azure](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) inserindo o comando abaixo:

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. [Extrair a chave principal](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) da conta de Armazenamento Azure e armazená-la numa variável, inserindo o comando abaixo:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Crie um recipiente](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) de armazenamento Azure entrando no comando abaixo:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Crie o cluster HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create). Antes de entrar no comando, note os seguintes parâmetros:

    1. Parâmetros necessários para os aglomerados de Kafka:

        |Parâmetro | Descrição|
        |---|---|
        |-tipo|O valor deve ser **Kafka.**|
        |--workernode-data-disks-per-nó|O número de discos de dados a utilizar por nó de trabalhador. HDInsight Kafka é suportado apenas com discos de dados. Este tutorial utiliza um valor de **2**.|

    1. Parâmetros necessários para o proxy Kafka REST:

        |Parâmetro | Descrição|
        |---|---|
        |--kafka-gestão-tamanho|Do tamanho do nó. Este tutorial utiliza o valor **Standard_D4_v2.**|
        |--kafka-cliente-grupo-id|O cliente do grupo de segurança AAD ID para Kafka Rest Proxy. O valor é passado a partir da **variável $securityGroupID.**|
        |--kafka-cliente-nome de grupo|O nome do grupo de segurança AAD cliente para Kafka Rest Proxy. O valor é passado a partir da **variável $securityGroupName.**|
        |-versão|A versão do cluster HDInsight deve ser de pelo menos 4.0. O valor é passado da **variável $clusterVersion.**|
        |--versão componente|A versão Kafka deve ser pelo menos 2.1. O valor é passado da **variável $componentVersion.**|
    
        Se quiser criar o cluster sem `--kafka-management-node-size`procuração `--kafka-client-group-name` REST, `az hdinsight create` elimine , `--kafka-client-group-id`e a partir do comando.

    1. Se tiver uma rede virtual existente, `--vnet-name` adicione `--subnet`os parâmetros e os seus valores.

    Introduza o seguinte comando para criar o cluster:

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    Pode levar vários minutos para que o processo de criação do cluster seja concluído. Normalmente por volta das 15.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o artigo, pode achar conveniente eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Armazenamento Azure, para que possa eliminar com segurança um cluster quando não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que as taxas de armazenamento, faz sentido económico apagar clusters quando não estão em uso.

Insira todos ou alguns dos seguintes comandos para remover recursos:

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Agora que criou com sucesso um cluster ativado por proxy Apache Kafka REST em Azure HDInsight utilizando o Azure CLI, utilize o código Python para interagir com o proxy REST:

> [!div class="nextstepaction"]
> [Criar aplicação de amostra](./rest-proxy.md#client-application-sample)