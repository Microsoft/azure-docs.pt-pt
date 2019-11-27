---
title: Apache Spark & Apache Kafka com Cosmos DB-Azure HDInsight
description: Saiba como usar Apache Spark streaming estruturado para ler dados de Apache Kafka e, em seguida, armazená-los em Azure Cosmos DB. Neste exemplo, vai transmitir dados através de um bloco de notas Jupyter do Spark no HDInsight.
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
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Use Apache Spark streaming estruturado com Apache Kafka e Azure Cosmos DB

Saiba como usar [Apache Spark](https://spark.apache.org/) [streaming estruturado](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) para ler dados de [Apache Kafka](https://kafka.apache.org/) no Azure HDInsight e, em seguida, armazená-los em Azure Cosmos DB.

O [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é um banco de dados de vários modelos distribuído globalmente. Este exemplo usa um modelo de banco de dados da API do SQL. Para obter mais informações, consulte o documento [Bem-vindo ao Azure Cosmos DB](../cosmos-db/introduction.md) .

A transmissão em fluxo estruturada do Spark é um motor de processamento de fluxos incorporado no SQL do Spark. Permite-lhe expressar computações de transmissão em fluxo, tal como a computação em lotes o faz em dados estáticos. Para obter mais informações sobre o streaming estruturado, consulte o [Guia de programação de streaming estruturado](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) em Apache.org.

> [!IMPORTANT]  
> Este exemplo usou o Spark 2,2 no HDInsight 3,6.
>
> Os passos neste documento criam um grupo de recursos do Azure que contém um cluster do Spark no HDInsight e um cluster do Kafka no HDInsight. Estes dois clusters estão localizados numa Rede Virtual do Azure, o que permite que o cluster do Spark comunique diretamente com o cluster do Kafka.
>
> Quando tiver concluído os passos neste documento, elimine os clusters para evitar encargos em excesso.

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka no HDInsight não fornece acesso aos agentes Kafka pela Internet pública. Tudo que se comunica com Kafka deve estar na mesma rede virtual do Azure que os nós no cluster Kafka. Para este exemplo, os clusters Kafka e Spark estão localizados em uma rede virtual do Azure. O diagrama a seguir mostra como a comunicação flui entre os clusters:

![Diagrama de clusters do Spark e Kafka numa rede virtual do Azure](./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png)

> [!NOTE]  
> O serviço Kafka está limitado à comunicação na rede virtual. Outros serviços em cluster, como SSH e Ambari, podem ser acedidos através da Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, veja [Portas e URIs utilizados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Embora você possa criar uma rede virtual do Azure, os clusters Kafka e Spark manualmente, é mais fácil usar um modelo de Azure Resource Manager. Use as etapas a seguir para implantar uma rede virtual do Azure, Kafka e clusters Spark em sua assinatura do Azure.

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no Portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    O modelo de Azure Resource Manager está localizado no repositório GitHub para este projeto ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Este modelo cria os seguintes recursos:

   * Um cluster do Kafka no HDInsight 3.6.

   * Um cluster Spark no HDInsight 3,6.

   * Uma Rede Virtual do Azure, que contém os clusters do HDInsight. A rede virtual criada pelo modelo usa o espaço de endereço 10.0.0.0/16.

   * Um banco de dados de API do SQL Azure Cosmos DB.

    > [!IMPORTANT]  
    > O bloco de notas de transmissão em fluxo estruturada utilizado neste exemplo requer o Spark no HDInsight 3.6. Se utilizar uma versão anterior do Spark no HDInsight, irá receber mensagens de erro ao utilizar o bloco de notas.

1. Use as informações a seguir para preencher as entradas na seção **implantação personalizada** :

    |Propriedade |Valor |
    |---|---|
    |Subscrição|Selecione a sua subscrição do Azure.|
    |Grupo de recursos|Crie um grupo ou selecione um existente. Esse grupo contém o cluster HDInsight.|
    |Nome da conta de Cosmos DB|Esse valor é usado como o nome da conta de Cosmos DB. O nome só pode conter letras minúsculas, números e o caractere de hífen (-). Deve ter entre 3-31 caracteres de comprimento.|
    |Nome do cluster de base|Esse valor é usado como o nome de base para os clusters Spark e Kafka. Por exemplo, a inserção de **myhdi** cria um cluster Spark chamado __Spark-myhdi__ e um cluster Kafka chamado **Kafka-myhdi**.|
    |Versão do cluster|A versão do cluster HDInsight. Este exemplo é testado com o HDInsight 3,6 e pode não funcionar com outros tipos de cluster.|
    |Nome de Utilizador de Início de Sessão do Cluster|O nome de usuário do administrador para os clusters Spark e Kafka.|
    |Palavra-passe de Início de Sessão do Cluster|A senha de usuário do administrador para os clusters Spark e Kafka.|
    |Nome de usuário SSH|O usuário SSH a ser criado para os clusters Spark e Kafka.|
    |Senha ssh|A senha para o usuário SSH para os clusters Spark e Kafka.|

    ![Valores de implantação personalizada do HDInsight](./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png)

1. Leia os **Termos e Condições** e selecione **Aceito os temos e as condições apresentados acima**.

1. Por fim, selecione **comprar**. Pode levar até 45 minutos para criar os clusters, a rede virtual e a conta de Cosmos DB.

## <a name="create-the-cosmos-db-database-and-collection"></a>Criar o banco de dados Cosmos DB e a coleção

O projeto usado neste documento armazena dados no Cosmos DB. Antes de executar o código, você deve primeiro criar um _banco de dados_ e uma _coleção_ em sua instância de Cosmos DB. Você também deve recuperar o ponto de extremidade do documento e a _chave_ usada para autenticar solicitações para Cosmos DB.

Uma maneira de fazer isso é usar o [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). O script a seguir criará um banco de dados chamado `kafkadata` e uma coleção chamada `kafkacollection`. Em seguida, ele retorna a chave primária.

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

O ponto de extremidade do documento e as informações da chave primária são semelhantes ao seguinte texto:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Salve os valores de ponto de extremidade e chave, pois eles são necessários nos notebooks Jupyter.

## <a name="get-the-notebooks"></a>Obter os blocos de anotações

O código de exemplo descrito neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb).

## <a name="upload-the-notebooks"></a>Carregar os blocos de notas

Use as etapas a seguir para carregar os blocos de anotações do projeto para o Spark no cluster HDInsight:

1. No seu browser, ligue ao bloco de notas Jupyter no cluster do Spark. No seguinte URL, substitua `CLUSTERNAME` pelo nome do seu cluster do __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Quando lhe for pedido, introduza o início de sessão do cluster (admin) e a palavra-passe utilizada quando criou o cluster.

2. No canto superior direito da página, use o botão __carregar__ para carregar o arquivo __Stream-táxi-data-to-Kafka. ipynb__ para o cluster. Selecione __Abrir__ para iniciar o carregamento.

3. Localize a entrada __Stream-táxi-data-to-Kafka. ipynb__ na lista de blocos de anotações e selecione o botão __carregar__ ao lado dele.

4. Repita as etapas 1-3 para carregar o bloco de anotações __Stream-Data-from-Kafka-to-Cosmos-DB. ipynb__ .

## <a name="load-taxi-data-into-kafka"></a>Carregar dados de táxi no Kafka

Depois que os arquivos tiverem sido carregados, selecione a entrada __Stream-táxi-data-to-Kafka. ipynb__ para abrir o bloco de anotações. Siga as etapas no bloco de anotações para carregar dados no Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Processar dados de táxi usando o streaming estruturado do Spark

Na home page de [Jupyter Notebook](https://jupyter.org/) , selecione a entrada __Stream-Data-from-Kafka-to-Cosmos-DB. ipynb__ . Siga as etapas no bloco de anotações para transmitir dados do Kafka e para Azure Cosmos DB usando o streaming estruturado do Spark.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a usar Apache Spark streaming estruturado, consulte os documentos a seguir para saber mais sobre como trabalhar com Apache Spark, Apache Kafka e Azure Cosmos DB:

* [Como usar o DStream (streaming de Apache Spark) com Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Iniciar com Jupyter Notebook e Apache Spark no HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Bem-vindo ao Azure Cosmos DB](../cosmos-db/introduction.md)
