---
title: Use o conector do Data Explorer do Azure para Apache Spark para mover dados entre os clusters do Azure Data Explorer e do Spark.
description: Este tópico mostra como mover dados entre clusters do Azure Data Explorer e Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: 868e9e068244af91e218d906bee115b58906152f
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76027895"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Conector do Azure Data Explorer para Apache Spark (versão prévia)

[Apache Spark](https://spark.apache.org/) é um mecanismo de análise unificado para processamento de dados em larga escala. O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado para análise em tempo real em grandes volumes de dados. 

O Azure Data Explorer Connector para Spark implementa a fonte de dados e o coletor de dados para mover dados entre os clusters do Azure Data Explorer e Spark para usar ambos os recursos. Usando o Azure Data Explorer e o Apache Spark, você pode criar aplicativos rápidos e escalonáveis direcionados a cenários baseados em dados, como o aprendizado de máquina (ML), o ETL (Extract-Transform-Load) e o Log Analytics. A gravação no Azure Data Explorer pode ser feita em modo de fluxo e em lote.
A leitura do Azure Data Explorer dá suporte à remoção de colunas e à aplicação de predicado, o que reduz o volume de dados transferidos Filtrando dados no Azure Data Explorer.

O conector Spark do Azure Data Explorer é um [projeto](https://github.com/Azure/azure-kusto-spark) de software livre que pode ser executado em qualquer cluster Spark. O conector do Azure Data Explorer Spark torna o Azure Data Explorer um armazenamento de dados válido para operações de origem e coletor padrão do Spark, como Write, Read e writeStream. 

> [!NOTE]
> Embora alguns dos exemplos a seguir refiram-se a um cluster [Azure Databricks](https://docs.azuredatabricks.net/) Spark, o conector do Azure data Explorer Spark não assume dependências diretas no databricks ou em qualquer outra distribuição do Spark.

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um cluster de Data Explorer do Azure e um banco de dados](/azure/data-explorer/create-cluster-database-portal) 
* Criar um cluster do Spark
* Instale a biblioteca do conector do Azure Data Explorer e bibliotecas listadas em [dependências](https://github.com/Azure/azure-kusto-spark#dependencies) , incluindo as seguintes bibliotecas do [SDK do Java Kusto](/azure/kusto/api/java/kusto-java-client-library) :
    * [Cliente de dados Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Cliente de ingestão de Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Bibliotecas predefinidas para [Spark 2,4, escala 2,11](https://github.com/Azure/azure-kusto-spark/releases) e [repositório Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)

## <a name="how-to-build-the-spark-connector"></a>Como criar o conector do Spark

O conector do Spark pode ser criado a partir de [fontes](https://github.com/Azure/azure-kusto-spark) , conforme detalhado abaixo.

> [!NOTE]
> Este passo é opcional. Se você estiver usando bibliotecas predefinidas, vá para a [configuração do cluster Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Pré-requisitos de compilação

* SDK do Java 1,8 instalado
* [Maven 3. x](https://maven.apache.org/download.cgi) instalado
* Apache Spark versão 2.4.0 ou superior

> [!TIP]
> as versões 2.3. x também têm suporte, mas podem exigir algumas alterações nas dependências pom. xml.

Para aplicativos escalares/Java usando definições de projeto Maven, vincule seu aplicativo com o seguinte artefato (a versão mais recente pode ser diferente):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>Comandos de compilação

Para compilar o JAR e executar todos os testes:

```
mvn clean package
```

Para criar o Jar, execute todos os testes e instale o jar em seu repositório Maven local:

```
mvn clean install
```

Para obter mais informações, consulte [uso do conector](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Configuração do cluster Spark

> [!NOTE]
> É recomendável usar a versão mais recente do conector do Azure Data Explorer Spark ao executar as seguintes etapas:

1. Defina as seguintes configurações de cluster do Spark, com base no cluster Azure Databricks usando o Spark 2.4.4 e o escala 2,11: 

    ![Configurações de cluster do databricks](media/spark-connector/databricks-cluster.png)
    
1. Instale a biblioteca mais recente do Spark-Kusto-Connector do Maven:

    ![Importar biblioteca de Data Explorer do Azure](media/spark-connector/db-create-library.png)

1. Verifique se todas as bibliotecas necessárias estão instaladas:

    ![Verificar bibliotecas instaladas](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Autenticação

O conector Spark do Azure Data Explorer permite que você autentique com o Azure Active Directory (AD do Azure) usando um [aplicativo do Azure ad](#azure-ad-application-authentication), [token de acesso do AD do Azure](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [autenticação de dispositivo](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (para cenários de não produção) ou [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). O usuário deve instalar o pacote Azure-keyvault e fornecer credenciais de aplicativo para acessar o recurso de Key Vault.

### <a name="azure-ad-application-authentication"></a>Autenticação de aplicativo do Azure AD

Método de autenticação mais simples e comum. Esse método é recomendado para o uso do conector do Azure Data Explorer Spark.

|Propriedades  |Descrição  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identificador de aplicativo (cliente) do Azure AD.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Autoridade de autenticação do Azure AD. ID do diretório (locatário) do Azure AD.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Chave de aplicativo do Azure AD para o cliente.     |

### <a name="azure-data-explorer-privileges"></a>Privilégios de Data Explorer do Azure

Os privilégios a seguir devem ser concedidos em um cluster de Data Explorer do Azure:

* Para leitura (fonte de dados), o aplicativo do Azure AD deve ter privilégios de *Visualizador* no banco de dado de destino ou privilégios de *administrador* na tabela de destino.
* Para gravação (coletor de dados), o aplicativo do Azure AD deve ter privilégios de *ingestão* no banco de dados de destino. Ele também deve ter privilégios de *usuário* no banco de dados de destino para criar novas tabelas. Se a tabela de destino já existir, os privilégios de *administrador* na tabela de destino poderão ser configurados.
 
Para obter mais informações sobre as funções de entidade de segurança Data Explorer do Azure, consulte [autorização baseada em função](/azure/kusto/management/access-control/role-based-authorization). Para gerenciar funções de segurança, consulte [Gerenciamento de funções de segurança](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Coletor Spark: gravando no Azure Data Explorer

1. Configurar parâmetros do coletor:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Grave o dataframe do Spark no cluster de Data Explorer do Azure como lote:

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey)
      .option(KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .option(KustoSinkOptions.KUSTO_TABLE_CREATE_OPTIONS, "CreateIfNotExist")
      .mode(SaveMode.Append)
      .save()  
    ```
    
   Ou use a sintaxe simplificada:
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. Gravar dados de streaming:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false") // Use in case a NullPointerException is thrown inside codegen iterator
    
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Origem do Spark: lendo do Azure Data Explorer

1. Ao ler pequenas quantidades de dados, defina a consulta de dados:

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. Ao ler grandes quantidades de dados, o armazenamento de blob transitório deve ser fornecido. Forneça a chave SAS do contêiner de armazenamento ou o nome da conta de armazenamento, a chave da conta e o nome do contêiner. Esta etapa só é necessária para a versão de visualização atual do conector do Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    No exemplo acima, não acessamos o Key Vault usando a interface do conector. Como alternativa, usamos um método mais simples de usar os segredos do databricks.

1. Ler do Azure Data Explorer:

    ```scala
     val conf3 = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
          KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [conector do Azure data Explorer Spark](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Código de exemplo](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)

