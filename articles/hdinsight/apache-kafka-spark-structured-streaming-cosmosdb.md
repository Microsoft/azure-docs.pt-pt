---
title: Apache Spark & Apache Kafka com Cosmos DB - Azure HDInsight
description: Aprenda a usar o Apache Spark Structured Streaming para ler dados de Apache Kafka e depois guarde-os em Azure Cosmos DB. Neste exemplo, vai transmitir dados através de um bloco de notas Jupyter do Spark no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/18/2019
ms.openlocfilehash: 04faafca0811e60ded47d1e91a82054a1c1cdb25
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74406160"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Use Apache Spark Structured Streaming com Apache Kafka e Azure Cosmos DB

Aprenda a usar o [Apache Spark](https://spark.apache.org/) [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) para ler dados de Apache [Kafka](https://kafka.apache.org/) no Azure HDInsight e, em seguida, armazenar os dados em Azure Cosmos DB.

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é uma base de dados multimodelo distribuída globalmente. Este exemplo utiliza um modelo de base de dados SQL API. Para mais informações, consulte o documento [Welcome to Azure Cosmos DB.](../cosmos-db/introduction.md)

A transmissão em fluxo estruturada do Spark é um motor de processamento de fluxos incorporado no SQL do Spark. Permite-lhe expressar computações de transmissão em fluxo, tal como a computação em lotes o faz em dados estáticos. Para mais informações sobre o Streaming Estruturado, consulte o Guia de Programação de [Streaming Estruturado](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) no Apache.org.

> [!IMPORTANT]  
> Este exemplo utilizou o Spark 2.2 no HDInsight 3.6.
>
> Os passos neste documento criam um grupo de recursos do Azure que contém um cluster do Spark no HDInsight e um cluster do Kafka no HDInsight. Estes dois clusters estão localizados numa Rede Virtual do Azure, o que permite que o cluster do Spark comunique diretamente com o cluster do Kafka.
>
> Quando tiver concluído os passos neste documento, elimine os clusters para evitar encargos em excesso.

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka no HDInsight não dá acesso aos corretores Kafka através da internet pública. Tudo o que fala com Kafka deve estar na mesma rede virtual Azure que os nós do aglomerado de Kafka. Para este exemplo, tanto os clusters Kafka como spark estão localizados numa rede virtual Azure. O diagrama que se segue mostra como a comunicação flui entre os clusters:

![Diagrama de clusters do Spark e Kafka numa rede virtual do Azure](./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png)

> [!NOTE]  
> O serviço Kafka está limitado à comunicação na rede virtual. Outros serviços em cluster, como SSH e Ambari, podem ser acedidos através da Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, veja [Portas e URIs utilizados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Enquanto você pode criar uma rede virtual Azure, Kafka, e clusters Spark manualmente, é mais fácil usar um modelo de Gestor de Recursos Azure. Utilize os seguintes passos para implantar uma rede virtual Azure, kafka e clusters Spark para a sua subscrição Azure.

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no Portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    O modelo do Gestor de Recursos Azure está localizado no[https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)repositório GitHub para este projeto ( ).

    Este modelo cria os seguintes recursos:

   * Um cluster do Kafka no HDInsight 3.6.

   * Uma Faísca no cluster HDInsight 3.6.

   * Uma Rede Virtual do Azure, que contém os clusters do HDInsight. A rede virtual criada pelo modelo utiliza o espaço de endereço10.0.0.0/16.

   * Uma base de dados Azure Cosmos DB SQL API.

    > [!IMPORTANT]  
    > O bloco de notas de transmissão em fluxo estruturada utilizado neste exemplo requer o Spark no HDInsight 3.6. Se utilizar uma versão anterior do Spark no HDInsight, irá receber mensagens de erro ao utilizar o bloco de notas.

1. Utilize as seguintes informações para preencher as entradas na secção de **implementação personalizada:**

    |Propriedade |Valor |
    |---|---|
    |Subscrição|Selecione a sua subscrição do Azure.|
    |Grupo de recursos|Crie um grupo ou selecione um existente. Este grupo contém o cluster HDInsight.|
    |Nome da conta Cosmos DB|Este valor é usado como nome para a conta Cosmos DB. O nome só pode conter letras minúsculas, números e o caráter de hífen (-). Tem de ter entre 3 e 31 carateres.|
    |Nome do cluster de base|Este valor é usado como o nome base para os aglomerados Spark e Kafka. Por exemplo, a entrada em **myhdi** cria um aglomerado de faíscas chamado __spark-myhdi__ e um aglomerado kafka chamado **kafka-myhdi**.|
    |Versão cluster|A versão do cluster HDInsight. Este exemplo é testado com HDInsight 3.6, e pode não funcionar com outros tipos de cluster.|
    |Nome de Utilizador de Início de Sessão do Cluster|O nome de utilizador administrativo dos aglomerados Spark e Kafka.|
    |Palavra-passe de Início de Sessão do Cluster|A senha de utilizador administrador dos clusters Spark e Kafka.|
    |Nome do utilizador SSH|O utilizador sSH para criar para os clusters Spark e Kafka.|
    |Palavra-passe ssh|A palavra-passe para o utilizador SSH para os clusters Spark e Kafka.|

    ![Valores de implementação personalizados HDInsight](./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png)

1. Leia os **Termos e Condições**, e depois selecione **concordo com os termos e condições acima indicados**.

1. Finalmente, selecione **Comprar**. Pode levar até 45 minutos para criar os clusters, rede virtual e conta Cosmos DB.

## <a name="create-the-cosmos-db-database-and-collection"></a>Crie a base de dados e recolha cosmos DB

O projeto utilizado neste documento armazena dados na Cosmos DB. Antes de executar o código, primeiro deve criar uma _base de dados_ e _recolha_ na sua instância Cosmos DB. Você também deve recuperar o ponto final do documento e a _chave_ usada para autenticar pedidos para cosmos DB.

Uma maneira de fazer isto é usar o [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). O seguinte script criará `kafkadata` uma base `kafkacollection`de dados com o nome e uma coleção chamada . Em seguida, devolve a chave primária.

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

O ponto final do documento e as informações principais são semelhantes ao seguinte texto:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Guarde o ponto final e os valores-chave, pois são necessários nos Cadernos Jupyter.

## <a name="get-the-notebooks"></a>Pegue os cadernos

O código do exemplo descrito neste [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)documento está disponível em .

## <a name="upload-the-notebooks"></a>Carregar os blocos de notas

Utilize os seguintes passos para fazer o upload dos cadernos do projeto para o seu cluster Spark on HDInsight:

1. No seu browser, ligue ao bloco de notas Jupyter no cluster do Spark. No seguinte URL, substitua `CLUSTERNAME` pelo nome do seu cluster do __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Quando lhe for pedido, introduza o início de sessão do cluster (admin) e a palavra-passe utilizada quando criou o cluster.

2. Do lado superior direito da página, utilize o botão __Upload__ para carregar o ficheiro __Stream-taxi-data-to-kafka.ipynb__ para o cluster. Selecione __Abrir__ para iniciar o carregamento.

3. Encontre a entrada __Stream-taxi-data-to-kafka.ipynb__ na lista de cadernos e selecione botão __upload__ ao lado.

4. Repita os passos 1-3 para carregar o caderno __Stream-data-from-Kafka-to-Cosmos-DB.ipynb.__

## <a name="load-taxi-data-into-kafka"></a>Carregue dados de táxi em Kafka

Uma vez que os ficheiros tenham sido carregados, selecione a entrada __Stream-taxi-data-to-kafka.ipynb__ para abrir o caderno. Siga os passos no caderno para carregar dados em Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Processar dados de táxi usando o streaming estruturado de faíscas

A partir da página inicial do [Jupyter Notebook,](https://jupyter.org/) selecione a entrada __Stream-data-from-Kafka-to-Cosmos-DB.ipynb.__ Siga os passos no caderno para transmitir dados de Kafka e entrar no Azure Cosmos DB usando o Spark Structured Streaming.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar o Apache Spark Structured Streaming, veja os seguintes documentos para saber mais sobre trabalhar com Apache Spark, Apache Kafka e Azure Cosmos DB:

* [Como utilizar o streaming Apache Spark (DStream) com Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Comece com Jupyter Notebook e Apache Spark no HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Bem-vindo ao Azure Cosmos DB](../cosmos-db/introduction.md)
