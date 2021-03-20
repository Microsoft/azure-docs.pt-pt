---
title: 'Tutorial: Criar um aglomerado de proxy Apache Kafka REST em HDInsight usando Azure CLI'
description: Aprenda a executar operações Apache Kafka usando um representante kafka REST em Azure HDInsight.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ff11b8461b483f5a66df19bb1b108a1fe1168fb9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944029"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Tutorial: Criar um aglomerado de proxy Apache Kafka REST em HDInsight usando Azure CLI

Neste tutorial, você aprende a criar um aglomerado de [procuração](./rest-proxy.md) Apache Kafka REST em Azure HDInsight usando interface de linha de comando Azure (CLI). O Azure HDInsight é um serviço de análise gerido, de espectro completo e de código aberto para empresas. O Apache Kafka é uma plataforma de código aberto de transmissão em fluxo distribuída. É frequentemente utilizado como mediador de mensagens, uma vez que fornece funcionalidades semelhantes a uma fila de mensagens de publicação-subscrição. Kafka REST Proxy permite-lhe interagir com o seu cluster Kafka através de uma [API REST](/rest/api/hdinsight-kafka-rest-proxy/) sobre HTTP. A CLI do Azure é a experiência de linha de comandos para várias plataformas da Microsoft para a gestão de recursos do Azure.

Só os recursos dentro da mesma rede virtual podem aceder à API do Apache Kafka. Pode aceder diretamente ao cluster utilizando sSH. Para ligar outros serviços, redes ou máquinas virtuais ao Apache Kafka, tem primeiro de criar uma rede virtual e, em seguida, criar os recursos dentro da rede. Para obter mais informações, consulte [Connect to Apache Kafka utilizando uma rede virtual.](./apache-kafka-connect-vpn-gateway.md)

Neste tutorial, aprende-se:

> [!div class="checklist"]
> * Pré-requisitos para o representante kafka REST
> * Criar um cluster Apache Kafka usando Azure CLI

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma candidatura registada na Azure AD. As aplicações do cliente que escreve para interagir com o representante kafka REST usarão o ID e o segredo desta aplicação para autenticar a Azure. Para obter mais informações, consulte [Registar uma aplicação com a plataforma de identidade da Microsoft.](../../active-directory/develop/quickstart-register-app.md)

* Um grupo de segurança Azure AD com o seu pedido registado como membro. Este grupo de segurança será utilizado para controlar quais aplicações podem interagir com o representante DO REST. Para obter mais informações sobre a criação de grupos AD Azure, consulte [criar um grupo básico e adicionar membros usando o Azure Ative Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* CLI do Azure. Certifique-se de que tem pelo menos a versão 2.0.79. Consulte [a Instalação do Azure CLI](/cli/azure/install-azure-cli).

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
    |resourceGroupName|Substitua o RESOURCEGROUPNAME pelo nome do seu novo grupo de recursos.|
    |localização|Substitua a LOCALIZAÇÃO por uma região onde o cluster será criado. Para uma lista de locais válidos, use o `az account list-locations` comando|
    |clusterName|Substitua o CLUSTERNAME por um nome globalmente único para o seu novo cluster.|
    |storageAccount|Substitua o STORAGEACCOUNTNAME por um nome para a sua nova conta de armazenamento.|
    |httpPassword|Substitua a PASSWORD por uma palavra-passe para o login do cluster, **administração**.|
    |sshPassword|Substitua a PASSWORD por uma palavra-passe para o nome de utilizador de concha segura, **sshuser**.|
    |nome do Grupo de Segurança|Substitua o NOME SECURITYGROUPNAME pelo nome do grupo de segurança AAD do cliente para Kafka Rest Proxy. A variável será passada para o `--kafka-client-group-name` parâmetro para `az-hdinsight-create` .|
    |securityGroupID|Substitua o SECURITYGROUPID pelo ID do grupo de segurança AAD do cliente para o Kafka Rest Proxy. A variável será passada para o `--kafka-client-group-id` parâmetro para `az-hdinsight-create` .|
    |storageContainer|Recipiente de armazenamento que o cluster irá utilizar, deixe como é para este tutorial. Esta variável será definida com o nome do cluster.|
    |trabalhadornodeEnse|Número de nós de trabalhadores no aglomerado, deixe como é para este tutorial. Para garantir uma elevada disponibilidade, Kafka requer um mínimo de 3 nós de trabalhadores|
    |clusterType|Tipo de cluster HDInsight, deixe como é para este tutorial.|
    |clusterVersão|Versão hdInsight cluster, deixe como é para este tutorial. Kafka Rest Proxy requer uma versão de cluster mínima de 4.0.|
    |componenteVersão|Versão kafka, deixe como é para este tutorial. Kafka Rest Proxy requer uma versão componente mínima de 2.1.|

    Atualizar as variáveis com os valores desejados. Em seguida, insira os comandos CLI para definir as variáveis ambientais.

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

1. [Crie o grupo de recursos](/cli/azure/group#az-group-create) introduzindo o comando abaixo:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Crie uma conta de Armazenamento Azure](/cli/azure/storage/account#az-storage-account-create) introduzindo o comando abaixo:

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

1. [Extrair a chave primária](/cli/azure/storage/account/keys#az-storage-account-keys-list) da conta de Armazenamento Azure e armazená-la numa variável introduzindo o comando abaixo:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Crie um recipiente de armazenamento Azure](/cli/azure/storage/container#az-storage-container-create) introduzindo o comando abaixo:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Crie o cluster HDInsight](/cli/azure/hdinsight#az-hdinsight-create). Antes de entrar no comando, observe os seguintes parâmetros:

    1. Parâmetros necessários para os clusters kafka:

        |Parâmetro | Descrição|
        |---|---|
        |-tipo|O valor deve ser **Kafka.**|
        |--trabalhadornode-dado-discos-por-nó|O número de discos de dados a utilizar por nó de trabalhador. HDInsight Kafka é suportado apenas com discos de dados. Este tutorial usa um valor de **2**.|

    1. Parâmetros necessários para o proxy Kafka REST:

        |Parâmetro | Descrição|
        |---|---|
        |--kafka-management-node-size|Do tamanho do nó. Este tutorial utiliza o valor **Standard_D4_v2.**|
        |--kafka-cliente-grupo-id|O grupo de segurança cliente AAD ID para Kafka Rest Proxy. O valor é passado a partir da variável **$securityGroupID.**|
        |--kafka-cliente-nome de grupo|O nome do grupo de segurança cliente AAD para Kafka Rest Proxy. O valor é passado a partir da variável **$securityGroupName.**|
        |-versão|A versão do cluster HDInsight deve ser pelo menos 4.0. O valor é passado a partir da variável **$clusterVersion.**|
        |--versão componente|A versão Kafka deve ser pelo menos 2.1. O valor é passado a partir da variável **$componentVersion.**|
    
        Se quiser criar o cluster sem procuração REST, `--kafka-management-node-size` elimine, e a partir do `--kafka-client-group-id` `--kafka-client-group-name` `az hdinsight create` comando.

    1. Se tiver uma rede virtual existente, adicione os parâmetros `--vnet-name` e `--subnet` os seus valores.

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

    Pode levar vários minutos para o processo de criação do cluster estar concluído. Normalmente por volta das 15.

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de concluir o artigo, pode achar conveniente eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Azure Storage, para que possa eliminar com segurança um cluster quando este não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que os encargos de armazenamento, faz sentido económico apagar clusters quando não estão a ser utilizados.

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

Agora que criou com sucesso um aglomerado de procuração Apache Kafka REST em Azure HDInsight usando Azure CLI, use o código Python para interagir com o proxy REST:

> [!div class="nextstepaction"]
> [Criar aplicação de amostra](./rest-proxy.md#client-application-sample)