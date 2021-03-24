---
title: Apache Spark & Apache Kafka com Cosmos DB - Azure HDInsight
description: Aprenda a usar o Apache Spark Structured Streaming para ler dados da Apache Kafka e, em seguida, armazene-os em Azure Cosmos DB. Neste exemplo, transmite dados usando um Caderno Jupyter de Spark on HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 11/18/2019
ms.openlocfilehash: d78b629e90903c58b98de86f425f0c1225d90997
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867054"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Use o Fluxo Estruturado de Faíscas Apache com Apache Kafka e Azure Cosmos DB

Aprenda a usar [o Apache Spark](https://spark.apache.org/) [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) para ler dados da Apache [Kafka](https://kafka.apache.org/) no Azure HDInsight e, em seguida, armazene os dados em Azure Cosmos DB.

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é uma base de dados multi-modelo distribuída globalmente. Este exemplo utiliza um modelo de base de dados API SQL. Para mais informações, consulte o documento [DB welcome to Azure Cosmos.](../cosmos-db/introduction.md)

A transmissão em fluxo estruturada do Spark é um motor de processamento de fluxos incorporado no SQL do Spark. Permite-lhe expressar computações de transmissão em fluxo, tal como a computação em lotes o faz em dados estáticos. Para obter mais informações sobre o Streaming Estruturado, consulte o [Guia de Programação](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) de Streaming Estruturado em Apache.org.

> [!IMPORTANT]  
> Este exemplo utilizou a Spark 2.2 no HDInsight 3.6.
>
> Os passos neste documento criam um grupo de recursos do Azure que contém um cluster do Spark no HDInsight e um cluster do Kafka no HDInsight. Estes dois clusters estão localizados numa Rede Virtual do Azure, o que permite que o cluster do Spark comunique diretamente com o cluster do Kafka.
>
> Quando tiver concluído os passos neste documento, elimine os clusters para evitar encargos em excesso.

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka na HDInsight não dá acesso aos corretores Kafka através da internet pública. Qualquer coisa que fale com Kafka deve estar na mesma rede virtual Azure que os nós no cluster kafka. Para este exemplo, tanto os clusters Kafka como Spark estão localizados numa rede virtual Azure. O seguinte diagrama mostra como a comunicação flui entre os clusters:

:::image type="content" source="./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png" alt-text="Diagrama de clusters do Spark e Kafka numa rede virtual do Azure" border="false":::

> [!NOTE]  
> O serviço Kafka está limitado à comunicação na rede virtual. Outros serviços em cluster, como SSH e Ambari, podem ser acedidos através da Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, veja [Portas e URIs utilizados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Embora possa criar uma rede virtual Azure, Kafka e Spark agrupamentos manualmente, é mais fácil usar um modelo de Gestor de Recursos Azure. Use os seguintes passos para implementar uma rede virtual Azure, Kafka e Spark clusters para a sua subscrição Azure.

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no Portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    O modelo Azure Resource Manager está localizado no repositório GitHub para este projeto [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb) ( .

    Este modelo cria os seguintes recursos:

   * Um cluster do Kafka no HDInsight 3.6.

   * Um conjunto de faíscas em HDInsight 3.6.

   * Uma Rede Virtual do Azure, que contém os clusters do HDInsight. A rede virtual criada pelo modelo utiliza o espaço de endereço 10.0.0.0/16.

   * Uma base de dados Azure Cosmos DB SQL API.

    > [!IMPORTANT]  
    > O bloco de notas de transmissão em fluxo estruturada utilizado neste exemplo requer o Spark no HDInsight 3.6. Se utilizar uma versão anterior do Spark no HDInsight, irá receber mensagens de erro ao utilizar o bloco de notas.

1. Utilize as seguintes informações para preencher as entradas na secção **de implantação personalizada:**

    |Propriedade |Valor |
    |---|---|
    |Subscrição|Selecione a sua subscrição do Azure.|
    |O grupo de recursos|Crie um grupo ou selecione um existente. Este grupo contém o cluster HDInsight.|
    |Nome da conta do Cosmos DB|Este valor é usado como o nome da conta Cosmos DB. O nome só pode conter letras minúsculas, números e o caráter de hífen (-). Tem de ter entre 3 e 31 carateres.|
    |Nome do cluster base|Este valor é usado como o nome base para os clusters Spark e Kafka. Por exemplo, entrar em **myhdi** cria um cluster spark chamado __spark-myhdi__ e um cluster Kafka chamado **kafka-myhdi**.|
    |Versão cluster|A versão do cluster HDInsight. Este exemplo é testado com HDInsight 3.6, e pode não funcionar com outros tipos de cluster.|
    |Nome de Utilizador de Início de Sessão do Cluster|O nome de utilizador administrativo para os clusters Spark e Kafka.|
    |Palavra-passe de Início de Sessão do Cluster|A palavra-passe do utilizador administrativo para os clusters Spark e Kafka.|
    |Nome do utilizador Ssh|O utilizador SSH para criar para os clusters Spark e Kafka.|
    |Ssh Password|A palavra-passe para o utilizador SSH para os clusters Spark e Kafka.|

    :::image type="content" source="./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png" alt-text="Valores de implementação personalizados HDInsight":::

1. Leia os **Termos e Condições** e, em seguida, selecione **Concordo com os termos e condições acima indicados**.

1. Finalmente, selecione **Comprar**. Pode levar até 45 minutos para criar os clusters, rede virtual e conta Cosmos DB.

## <a name="create-the-cosmos-db-database-and-collection"></a>Criar a base de dados e recolha do Cosmos DB

O projeto utilizado neste documento armazena dados em Cosmos DB. Antes de executar o código, deve primeiro criar uma _base de dados_ e _uma recolha_ na sua instância de Cosmos DB. Também deve recuperar o ponto final do documento e a _chave_ usada para autenticar pedidos para a Cosmos DB.

Uma maneira de fazer isto é usar o [Azure CLI](/cli/azure/). O seguinte script criará uma base de dados nomeada `kafkadata` e uma coleção chamada `kafkacollection` . Em seguida, devolve a chave primária.

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter Notebook
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

O código para o exemplo descrito neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb) .

## <a name="upload-the-notebooks"></a>Carregar os blocos de notas

Utilize os seguintes passos para fazer o upload dos cadernos do projeto para o seu spark no cluster HDInsight:

1. No seu navegador web, ligue-se ao Bloco de Notas do Jupyter no seu cluster Spark. No seguinte URL, substitua `CLUSTERNAME` pelo nome do seu cluster do __Spark__:

    ```http
    https://CLUSTERNAME.azurehdinsight.net/jupyter
    ```

    Quando lhe for pedido, introduza o início de sessão do cluster (admin) e a palavra-passe utilizada quando criou o cluster.

2. Do lado superior direito da página, utilize o botão __Upload__ para carregar o ficheiro __Stream-taxi-data-to-kafka.ipynb__ para o cluster. Selecione __Abrir__ para iniciar o carregamento.

3. Encontre a entrada __Stream-taxi-data-to-kafka.ipynb__ na lista de cadernos e selecione o botão __upload__ ao lado.

4. Repita os passos 1-3 para carregar o caderno __stream-data-from-Kafka-to-Cosmos-DB.ipynb.__

## <a name="load-taxi-data-into-kafka"></a>Carregue os dados do táxi em Kafka

Uma vez que os ficheiros tenham sido carregados, selecione a entrada __Stream-taxi-data-to-kafka.ipynb__ para abrir o caderno. Siga os passos no caderno para carregar dados em Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Processar dados de táxi usando o Streaming Estruturado de Faísca

Na página inicial do [Jupyter Notebook,](https://jupyter.org/) selecione a entrada __Stream-data-from-Kafka-to-Cosmos-DB.ipynb.__ Siga os passos no caderno para transmitir dados de Kafka e para a Azure Cosmos DB usando o Spark Structured Streaming.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar o Apache Spark Structured Streaming, veja os seguintes documentos para saber mais sobre trabalhar com Apache Spark, Apache Kafka e Azure Cosmos DB:

* [Como utilizar o streaming Apache Spark (DStream) com Apache Kafka.](hdinsight-apache-spark-with-kafka.md)
* [Comece com Jupyter Notebook e Apache Spark em HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Bem-vindo ao Azure Cosmos DB](../cosmos-db/introduction.md)
