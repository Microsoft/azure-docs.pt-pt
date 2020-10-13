---
title: Create Azure HDInsight - Azure Data Lake Storage Gen2 - Azure CLI
description: Aprenda a usar o Azure Data Lake Storage Gen2 com clusters Azure HDInsight usando Azure CLI.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: e8cfe55f4843439743535b978483d8518bd020fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91858786"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-azure-cli"></a>Criar um cluster com data lake storage gen2 usando Azure CLI

Para criar um cluster HDInsight que usa data lake storage gen2 para armazenamento, siga estes passos.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com a Azure Data Lake Storage Gen2, consulte a [secção de visão geral.](hdinsight-hadoop-use-data-lake-storage-gen2.md) 
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os exemplos de script do CLI, tem três opções:
    - Utilize a Casca de [Nuvem Azure](../cloud-shell/overview.md) a partir do portal Azure (ver secção seguinte).
    - Utilize o Azure Cloud Shell incorporado através do botão "Try It", localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente do Azure CLI](/cli/azure/install-azure-cli) (2.0.13 ou posterior) se preferir utilizar uma consola CLI local. Faça o sessão no Azure utilizando `az login` , utilizando uma conta que está associada à subscrição Azure sob a qual pretende implementar a identidade gerida atribuída pelo utilizador. Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Você pode [baixar um ficheiro de modelo de amostra](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) e descarregar um ficheiro de [parâmetros de amostra](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Antes de utilizar o modelo e o corte de código Azure CLI abaixo, substitua os seguintes espaços reservados pelos seus valores corretos:

| Marcador de posição | Descrição |
|---|---|
| `<SUBSCRIPTION_ID>` | O ID da sua assinatura Azure |
| `<RESOURCEGROUPNAME>` | O grupo de recursos onde pretende a criação da nova conta de cluster e armazenamento. |
| `<MANAGEDIDENTITYNAME>` | O nome da identidade gerida que será dada permissões na sua conta de armazenamento com a Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | A nova conta de armazenamento com a Azure Data Lake Storage Gen2 que será criada. |
| `<FILESYSTEMNAME>`  | O nome do sistema de ficheiros que este cluster deve utilizar na conta de armazenamento. |
| `<CLUSTERNAME>` | O nome do seu cluster HDInsight. |
| `<PASSWORD>` | A sua senha escolhida para iniciar sessão no cluster utilizando o SSH e o dashboard Ambari. |

O código abaixo faz os seguintes passos iniciais:

1. Entra na sua conta Azure.
1. Define a subscrição ativa onde serão feitas as operações de criação.
1. Cria um novo grupo de recursos para as novas atividades de implantação.
1. Cria uma identidade gerida atribuída pelo utilizador.
1. Adiciona uma extensão ao CLI Azure para usar funcionalidades para data lake storage gen2.
1. Cria uma nova conta de armazenamento com data lake storage gen2 usando a `--hierarchical-namespace true` bandeira.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Em seguida, inscreva-se no portal. Adicione a nova identidade gerida atribuída ao utilizador na função **de Contribuinte de Dados de Armazenamento** na conta de armazenamento. Este passo é descrito no passo 3 sob [a utilização do portal Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

 > [!IMPORTANT]
 > Certifique-se de que a sua conta de armazenamento tem a identidade atribuída ao utilizador com permissões de função **de contribuinte de dados de armazenamento,** caso contrário a criação de cluster falhará.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

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

Criou com sucesso um cluster HDInsight. Agora aprende a trabalhar com o teu grupo.

### <a name="apache-spark-clusters"></a>Aglomerados de faíscas apache

* [Personalize os clusters HDInsight baseados em Linux utilizando ações de script](hdinsight-hadoop-customize-cluster-linux.md)
* [Criar uma aplicação autónoma com o Scala](spark/apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um aglomerado Apache Spark usando Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark com BI: Realizar análise de dados interativas usando Spark in HDInsight com ferramentas BI](spark/apache-spark-use-bi-tools.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para prever os resultados da inspeção alimentar](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Aglomerados apache Hadoop

* [Use a Colmeia Apache com HDInsight](hadoop/hdinsight-use-hive.md)
* [Use MapReduce com HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Aglomerados Apache HBase

* [Começar com Apache HBase em HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicações Java para Apache HBase em HDInsight](hbase/apache-hbase-build-java-maven-linux.md)