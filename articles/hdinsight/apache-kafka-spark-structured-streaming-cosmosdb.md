---
title: Apache Spark streaming estruturado de Apache Kafka para Azure Cosmos DB-Azure HDInsight
description: Saiba como usar Apache Spark streaming estruturado para ler dados de Apache Kafka e, em seguida, armazená-los em Azure Cosmos DB. Neste exemplo, vai transmitir dados através de um bloco de notas Jupyter do Spark no HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: c2d2f56f2a0686b23a3a907686dcee0760d37365
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947057"
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

O Apache Kafka no HDInsight não fornece acesso aos mediadores Kafka através da Internet pública. Tudo que se comunica com Kafka deve estar na mesma rede virtual do Azure que os nós no cluster Kafka. Para este exemplo, os clusters Kafka e Spark estão localizados em uma rede virtual do Azure. O diagrama a seguir mostra como a comunicação flui entre os clusters:

![Diagrama de clusters do Spark e Kafka numa rede virtual do Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> O serviço Kafka está limitado à comunicação na rede virtual. Outros serviços em cluster, como SSH e Ambari, podem ser acedidos através da Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, veja [Portas e URIs utilizados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Embora você possa criar uma rede virtual do Azure, os clusters Kafka e Spark manualmente, é mais fácil usar um modelo de Azure Resource Manager. Use as etapas a seguir para implantar uma rede virtual do Azure, Kafka e clusters Spark em sua assinatura do Azure.

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no Portal do Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png" alt="Deploy to Azure"/>
    </a>

    O modelo de Azure Resource Manager está localizado no repositório GitHub para este projeto ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Este modelo cria os seguintes recursos:

   * Um cluster do Kafka no HDInsight 3.6.

   * Um cluster Spark no HDInsight 3,6.

   * Uma Rede Virtual do Azure, que contém os clusters do HDInsight.

       > [!NOTE]  
       > A rede virtual criada pelo modelo usa o espaço de endereço 10.0.0.0/16.

   * Um banco de dados de API do SQL Azure Cosmos DB.

     > [!IMPORTANT]  
     > O bloco de notas de transmissão em fluxo estruturada utilizado neste exemplo requer o Spark no HDInsight 3.6. Se utilizar uma versão anterior do Spark no HDInsight, irá receber mensagens de erro ao utilizar o bloco de notas.

2. Use as informações a seguir para preencher as entradas na seção **implantação personalizada** :
   
    ![Implantação personalizada do HDInsight](./media/apache-kafka-spark-structured-streaming-cosmosdb/parameters.png)

    * **Assinatura**: Selecione a sua subscrição do Azure.
   
    * **Grupo de recursos**: Crie um grupo ou selecione um existente. Esse grupo contém o cluster HDInsight.

    * **Local**: Selecione um local geograficamente perto de você.

    * **Nome da conta de Cosmos DB**: Esse valor é usado como o nome da conta de Cosmos DB.

    * **Nome do cluster de base**: Esse valor é usado como o nome de base para os clusters Spark e Kafka. Por exemplo, a inserção de **myhdi** cria um cluster Spark chamado __Spark-myhdi__ e um cluster Kafka chamado **Kafka-myhdi**.

    * **Versão do cluster**: A versão do cluster HDInsight.

        > [!IMPORTANT]  
        > Este exemplo é testado com o HDInsight 3,6 e pode não funcionar com outros tipos de cluster.

    * **Nome de usuário de logon do cluster**: O nome de usuário do administrador para os clusters Spark e Kafka.

    * **Senha de logon do cluster**: A senha de usuário do administrador para os clusters Spark e Kafka.

    * **Nome de usuário SSH**: O usuário SSH a ser criado para os clusters Spark e Kafka.

    * **Senha ssh**: A senha para o usuário SSH para os clusters Spark e Kafka.

3. Leia os **Termos e Condições** e selecione **Aceito os temos e as condições apresentados acima**.

4. Por fim, selecione **comprar**. Leva cerca de 20 minutos para criar os clusters.

> [!IMPORTANT]  
> Pode levar até 45 minutos para criar os clusters, a rede virtual e a conta de Cosmos DB.

## <a name="create-the-cosmos-db-database-and-collection"></a>Criar o banco de dados Cosmos DB e a coleção

O projeto usado neste documento armazena dados no Cosmos DB. Antes de executar o código, você deve primeiro criar um _banco de dados_ e uma _coleção_ em sua instância de Cosmos DB. Você também deve recuperar o ponto de extremidade do documento e a _chave_ usada para autenticar solicitações para Cosmos DB. 

Uma maneira de fazer isso é usar o [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). O script a seguir criará um banco `kafkadata` de dados chamado e `kafkacollection`uma coleção chamada. Em seguida, ele retorna a chave primária.

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

O ponto de extremidade do documento e as informações da chave primária são semelhantes ao seguinte texto:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Salve os valores de ponto de extremidade e chave, pois eles são necessários nos notebooks Jupyter.

## <a name="get-the-apache-kafka-brokers"></a>Obter os agentes de Apache Kafka

O código neste exemplo se conecta aos hosts do agente do Kafka no cluster Kafka. Para localizar os endereços dos dois hosts do agente do Kafka, use o seguinte exemplo de PowerShell ou bash:

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
> O exemplo de bash `$CLUSTERNAME` espera conter o nome do cluster Kafka.
>
> Este exemplo usa o utilitário [JQ](https://stedolan.github.io/jq/) para analisar dados fora do documento JSON.

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

Quando solicitado, insira a senha para a conta de logon (administrador) do cluster

O resultado é semelhante ao seguinte texto:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Salve essas informações, pois elas são usadas nas seções a seguir deste documento.

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

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu a usar Apache Spark streaming estruturado, consulte os documentos a seguir para saber mais sobre como trabalhar com Apache Spark, Apache Kafka e Azure Cosmos DB:

* [Como usar o DStream (streaming de Apache Spark) com Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Iniciar com Jupyter Notebook e Apache Spark no HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Bem-vindo ao Azure Cosmos DB](../cosmos-db/introduction.md)
