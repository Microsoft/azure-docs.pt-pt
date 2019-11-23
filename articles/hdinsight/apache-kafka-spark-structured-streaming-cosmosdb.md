---
title: Apache Spark & Apache Kafka with Cosmos DB - Azure HDInsight
description: Learn how to use Apache Spark Structured Streaming to read data from Apache Kafka and then store it into Azure Cosmos DB. Neste exemplo, vai transmitir dados através de um bloco de notas Jupyter do Spark no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/18/2019
ms.openlocfilehash: 04faafca0811e60ded47d1e91a82054a1c1cdb25
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406160"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Use Apache Spark Structured Streaming with Apache Kafka and Azure Cosmos DB

Learn how to use [Apache Spark](https://spark.apache.org/) [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) to read data from [Apache Kafka](https://kafka.apache.org/) on Azure HDInsight, and then store the data into Azure Cosmos DB.

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) is a globally distributed, multi-model database. This example uses a SQL API database model. For more information, see the [Welcome to Azure Cosmos DB](../cosmos-db/introduction.md) document.

A transmissão em fluxo estruturada do Spark é um motor de processamento de fluxos incorporado no SQL do Spark. Permite-lhe expressar computações de transmissão em fluxo, tal como a computação em lotes o faz em dados estáticos. For more information on Structured Streaming, see the [Structured Streaming Programming Guide](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) at Apache.org.

> [!IMPORTANT]  
> This example used Spark 2.2 on HDInsight 3.6.
>
> Os passos neste documento criam um grupo de recursos do Azure que contém um cluster do Spark no HDInsight e um cluster do Kafka no HDInsight. Estes dois clusters estão localizados numa Rede Virtual do Azure, o que permite que o cluster do Spark comunique diretamente com o cluster do Kafka.
>
> Quando tiver concluído os passos neste documento, elimine os clusters para evitar encargos em excesso.

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka on HDInsight doesn't provide access to the Kafka brokers over the public internet. Anything that talks to Kafka must be in the same Azure virtual network as the nodes in the Kafka cluster. For this example, both the Kafka and Spark clusters are located in an Azure virtual network. The following diagram shows how communication flows between the clusters:

![Diagrama de clusters do Spark e Kafka numa rede virtual do Azure](./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png)

> [!NOTE]  
> O serviço Kafka está limitado à comunicação na rede virtual. Outros serviços em cluster, como SSH e Ambari, podem ser acedidos através da Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, veja [Portas e URIs utilizados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

While you can create an Azure virtual network, Kafka, and Spark clusters manually, it's easier to use an Azure Resource Manager template. Use the following steps to deploy an Azure virtual network, Kafka, and Spark clusters to your Azure subscription.

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no Portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    The Azure Resource Manager template is located in the GitHub repository for this project ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Este modelo cria os seguintes recursos:

   * Um cluster do Kafka no HDInsight 3.6.

   * A Spark on HDInsight 3.6 cluster.

   * Uma Rede Virtual do Azure, que contém os clusters do HDInsight. The virtual network created by the template uses the 10.0.0.0/16 address space.

   * An Azure Cosmos DB SQL API database.

    > [!IMPORTANT]  
    > O bloco de notas de transmissão em fluxo estruturada utilizado neste exemplo requer o Spark no HDInsight 3.6. Se utilizar uma versão anterior do Spark no HDInsight, irá receber mensagens de erro ao utilizar o bloco de notas.

1. Use the following information to populate the entries on the **Custom deployment** section:

    |Propriedade |Valor |
    |---|---|
    |Subscrição|Selecione a sua subscrição do Azure.|
    |Grupo de recursos|Create a group or select an existing one. This group contains the HDInsight cluster.|
    |Cosmos DB Account Name|This value is used as the name for the Cosmos DB account. The name can only contain lowercase letters, numbers, and the hyphen (-) character. It must be between 3-31 characters in length.|
    |Base Cluster Name|This value is used as the base name for the Spark and Kafka clusters. For example, entering **myhdi** creates a Spark cluster named __spark-myhdi__ and a Kafka cluster named **kafka-myhdi**.|
    |Cluster Version|The HDInsight cluster version. This example is tested with HDInsight 3.6, and may not work with other cluster types.|
    |Nome de Utilizador de Início de Sessão do Cluster|The admin user name for the Spark and Kafka clusters.|
    |Palavra-passe de Início de Sessão do Cluster|The admin user password for the Spark and Kafka clusters.|
    |Ssh User Name|The SSH user to create for the Spark and Kafka clusters.|
    |Ssh Password|The password for the SSH user for the Spark and Kafka clusters.|

    ![HDInsight custom deployment values](./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png)

1. Leia os **Termos e Condições** e selecione **Aceito os temos e as condições apresentados acima**.

1. Finally, select **Purchase**. It may take up to 45 minutes to create the clusters, virtual network, and Cosmos DB account.

## <a name="create-the-cosmos-db-database-and-collection"></a>Create the Cosmos DB database and collection

The project used in this document stores data in Cosmos DB. Before running the code, you must first create a _database_ and _collection_ in your Cosmos DB instance. You must also retrieve the document endpoint and the _key_ used to authenticate requests to Cosmos DB.

One way to do this is to use the [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). The following script will create a database named `kafkadata` and a collection named `kafkacollection`. It then returns the primary key.

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter notebook
databaseName='kafkadata'
collectionName='kafkacollection'

# Create the database
az cosmosdb sql database create --account-name $name --name $databaseName --resource-group $resourceGroupName

# Create the collection
az cosmosdb sql container create --account-name $name --database-name $databaseName --name $collectionName --partition-key-path "/my/path" --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb keys list --name $name --resource-group $resourceGroupName --type keys
```

The document endpoint and primary key information is similar to the following text:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Save the endpoint and key values, as they are needed in the Jupyter Notebooks.

## <a name="get-the-notebooks"></a>Get the notebooks

O código de exemplo descrito neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb).

## <a name="upload-the-notebooks"></a>Carregar os blocos de notas

Use the following steps to upload the notebooks from the project to your Spark on HDInsight cluster:

1. No seu browser, ligue ao bloco de notas Jupyter no cluster do Spark. No seguinte URL, substitua `CLUSTERNAME` pelo nome do seu cluster do __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Quando lhe for pedido, introduza o início de sessão do cluster (admin) e a palavra-passe utilizada quando criou o cluster.

2. From the upper right side of the page, use the __Upload__ button to upload the __Stream-taxi-data-to-kafka.ipynb__ file to the cluster. Selecione __Abrir__ para iniciar o carregamento.

3. Find the __Stream-taxi-data-to-kafka.ipynb__ entry in the list of notebooks, and select __Upload__ button beside it.

4. Repeat steps 1-3 to load the __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ notebook.

## <a name="load-taxi-data-into-kafka"></a>Load taxi data into Kafka

Once the files have been uploaded, select the __Stream-taxi-data-to-kafka.ipynb__ entry to open the notebook. Follow the steps in the notebook to load data into Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Process taxi data using Spark Structured Streaming

From the [Jupyter Notebook](https://jupyter.org/) home page, select the __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ entry. Follow the steps in the notebook to stream data from Kafka and into Azure Cosmos DB using Spark Structured Streaming.

## <a name="next-steps"></a>Passos seguintes

Now that you've learned how to use Apache Spark Structured Streaming, see the following documents to learn more about working with Apache Spark, Apache Kafka, and Azure Cosmos DB:

* [How to use Apache Spark streaming (DStream) with Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Start with Jupyter Notebook and Apache Spark on HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Welcome to Azure Cosmos DB](../cosmos-db/introduction.md)
