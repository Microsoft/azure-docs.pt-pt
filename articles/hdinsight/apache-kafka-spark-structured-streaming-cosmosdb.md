---
title: Transmissão em fluxo estruturada do Apache Kafka para Azure Cosmos DB - Azure HDInsight do Apache Spark
description: Saiba como utilizar a transmissão estruturada do Apache Spark para ler dados do Apache Kafka e, em seguida, armazene-o para o Azure Cosmos DB. Neste exemplo, vai transmitir dados através de um bloco de notas Jupyter do Spark no HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 9d29608ded920b14af1be6d4d68ab1d77c3c8cb5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099159"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Utilizar o Apache Spark estruturado de transmissão em fluxo com o Apache Kafka e Azure Cosmos DB

Aprenda a usar [Apache Spark](https://spark.apache.org/) [transmissão em fluxo estruturada](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) para ler dados a partir de [Apache Kafka](https://kafka.apache.org/) no Azure HDInsight e, em seguida, arquivo de dados para o Azure Cosmos DB.

[O Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é uma base de dados distribuída globalmente com vários modelo. Este exemplo utiliza um modelo de base de dados da API de SQL. Para obter mais informações, consulte a [bem-vindo ao Azure Cosmos DB](../cosmos-db/introduction.md) documento.

A transmissão em fluxo estruturada do Spark é um motor de processamento de fluxos incorporado no SQL do Spark. Permite-lhe expressar computações de transmissão em fluxo, tal como a computação em lotes o faz em dados estáticos. Para obter mais informações sobre a transmissão em fluxo estruturada, consulte a [guia de programação de transmissão em fluxo estruturada](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) em Apache.org.

> [!IMPORTANT]  
> Neste exemplo utilizado o Spark 2.2 no HDInsight 3.6.
>
> Os passos neste documento criam um grupo de recursos do Azure que contém um cluster do Spark no HDInsight e um cluster do Kafka no HDInsight. Estes dois clusters estão localizados numa Rede Virtual do Azure, o que permite que o cluster do Spark comunique diretamente com o cluster do Kafka.
>
> Quando tiver concluído os passos neste documento, elimine os clusters para evitar encargos em excesso.

## <a name="create-the-clusters"></a>Criar os clusters

O Apache Kafka no HDInsight não fornece acesso aos mediadores Kafka através da Internet pública. Tudo o que se comunica com o Kafka tem de estar na mesma rede virtual do Azure que os nós do cluster do Kafka. Neste exemplo, os clusters de Kafka e Spark estão localizados numa rede virtual do Azure. O diagrama seguinte mostra como a comunicação flui entre os clusters:

![Diagrama de clusters do Spark e Kafka numa rede virtual do Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> O serviço Kafka está limitado à comunicação na rede virtual. Outros serviços em cluster, como SSH e Ambari, podem ser acedidos através da Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, veja [Portas e URIs utilizados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Embora pode criar uma rede virtual do Azure, Kafka, e clusters do Spark manualmente, é mais fácil de usar um modelo Azure Resource Manager. Utilize os seguintes passos para implementar uma rede virtual do Azure, Kafka e do Spark clusters para a sua subscrição do Azure.

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no Portal do Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

    O modelo do Azure Resource Manager está localizado no repositório do GitHub para este projeto ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Este modelo cria os seguintes recursos:

   * Um cluster do Kafka no HDInsight 3.6.

   * Spark no cluster de HDInsight 3.6.

   * Uma Rede Virtual do Azure, que contém os clusters do HDInsight.

       > [!NOTE]  
       > A rede virtual criada pelo modelo utiliza o espaço de endereços 10.0.0.0/16.

   * Uma base de dados do Azure Cosmos DB SQL API.

     > [!IMPORTANT]  
     > O bloco de notas de transmissão em fluxo estruturada utilizado neste exemplo requer o Spark no HDInsight 3.6. Se utilizar uma versão anterior do Spark no HDInsight, irá receber mensagens de erro ao utilizar o bloco de notas.

2. Utilize as seguintes informações para preencher as entradas a **implementação personalizada** secção:
   
    ![Implantação personalizada do HDInsight](./media/apache-kafka-spark-structured-streaming-cosmosdb/parameters.png)

    * **Subscrição**: Selecione a sua subscrição do Azure.
   
    * **Grupo de recursos**: Crie um grupo ou selecione um existente. Este grupo contém o cluster do HDInsight.

    * **Localização**: Selecione um local geograficamente próximo de.

    * **Nome da conta do cosmos DB**: Este valor é utilizado como o nome de conta do Cosmos DB.

    * **Nome do Cluster de base**: Este valor é utilizado como o nome de base para os clusters do Spark e Kafka. Por exemplo, introduzir **myhdi** cria um cluster do Spark com o nome __spark myhdi__ e um cluster de Kafka com o nome **kafka myhdi**.

    * **Versão do cluster**: A versão de cluster do HDInsight.

        > [!IMPORTANT]  
        > Neste exemplo é testado com o HDInsight 3.6 e poderá não funcionar com outros tipos de cluster.

    * **Nome de utilizador de início de sessão do cluster**: O nome de utilizador de administrador para os clusters do Spark e Kafka.

    * **Palavra-passe de início de sessão do cluster**: A senha do usuário de administrador para os clusters do Spark e Kafka.

    * **Nome de utilizador SSH**: O utilizador SSH para criar para os clusters do Spark e Kafka.

    * **SSH palavra-passe**: A palavra-passe do utilizador SSH para os clusters do Spark e Kafka.

3. Leia os **Termos e Condições** e selecione **Aceito os temos e as condições apresentados acima**.

4. Por fim, selecione **Compra**. Demora cerca de 20 minutos para criar os clusters.

> [!IMPORTANT]  
> Poderá demorar até 45 minutos para criar os clusters, a rede virtual e a conta do Cosmos DB.

## <a name="create-the-cosmos-db-database-and-collection"></a>Criar a coleção e a base de dados do Cosmos DB

O projeto utilizado neste documento armazena os dados no Cosmos DB. Antes de executar o código, primeiro tem de criar uma _base de dados_ e _coleção_ na sua instância de Cosmos DB. Também tem de obter o ponto de final do documento e o _chave_ utilizado para autenticar pedidos para o Cosmos DB. 

Uma forma de fazer isso é usar o [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). O script seguinte irá criar uma base de dados com o nome `kafkadata` e uma coleção designada `kafkacollection`. Em seguida, devolve a chave primária.

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
az cosmosdb database create --name $name --db-name $databaseName --resource-group $resourceGroupName
# Create the collection
az cosmosdb collection create --collection-name $collectionName --name $name --db-name $databaseName --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb list-keys --name $name --resource-group $resourceGroupName --query primaryMasterKey
```

O ponto final do documento e informações da chave primária é semelhante ao seguinte texto:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Guarde o ponto final e os valores de chave, uma vez que são necessários em blocos de notas do Jupyter.

## <a name="get-the-apache-kafka-brokers"></a>Obter mediadores Apache Kafka

O código neste exemplo liga-se a anfitriões de Mediador Kafka no cluster do Kafka. Para localizar os endereços dos dois anfitriões do Mediador Kafka, utilize o seguinte exemplo de PowerShell ou Bash:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds `
    -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

> [!NOTE]  
> O exemplo de Bash espera `$CLUSTERNAME` para conter o nome do Kafka cluster.
>
> Este exemplo utiliza a [jq](https://stedolan.github.io/jq/) utilitário para analisar dados fora do documento JSON.

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

Quando lhe for pedido, introduza a palavra-passe para a conta de início de sessão (admin) do cluster

O resultado é semelhante ao seguinte texto:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Guarde esta informação, como é usado nas secções seguintes deste documento.

## <a name="get-the-notebooks"></a>Obter os blocos de notas

O código de exemplo descrito neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb).

## <a name="upload-the-notebooks"></a>Carregar os blocos de notas

Utilize os seguintes passos para carregar os blocos de notas do projeto para o Spark num cluster do HDInsight:

1. No seu browser, ligue ao bloco de notas Jupyter no cluster do Spark. No seguinte URL, substitua `CLUSTERNAME` pelo nome do seu cluster do __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Quando lhe for pedido, introduza o início de sessão do cluster (admin) e a palavra-passe utilizada quando criou o cluster.

2. A partir do canto superior direito da página, utilize o __carregue__ botão para carregar o __Stream-táxis-data-para-ipynb__ ficheiro para o cluster. Selecione __Abrir__ para iniciar o carregamento.

3. Encontrar o __Stream-táxis-data-para-ipynb__ entrada na lista de blocos de notas e selecione __carregar__ botão junto à mesma.

4. Repita os passos 1 a 3 para carregar os __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ bloco de notas.

## <a name="load-taxi-data-into-kafka"></a>Dados de táxis de carga para o Kafka

Depois dos ficheiros foram carregados, selecione o __Stream-táxis-data-para-ipynb__ entrada para abrir o bloco de notas. Siga os passos no bloco de notas para carregar dados para o Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Processar dados de táxis com Spark Structured Streaming

Do [bloco de notas do Jupyter](https://jupyter.org/) página inicial, selecione a __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ entrada. Siga os passos no bloco de notas para transmitir dados do Kafka e para o Azure Cosmos DB com Spark Structured Streaming.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como utilizar a transmissão estruturada do Apache Spark, consulte os seguintes documentos para obter mais informações sobre como trabalhar com o Apache Spark, Apache Kafka e Azure Cosmos DB:

* [Como utilizar o Apache Spark, transmissão em fluxo (DStream) com o Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Comece com o bloco de notas do Jupyter e Apache Spark no HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Bem-vindo ao Azure Cosmos DB](../cosmos-db/introduction.md)
