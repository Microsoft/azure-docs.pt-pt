---
title: Utilize o conector Azure Data Explorer para a Apache Spark para mover dados entre o Azure Data Explorer e os clusters Spark.
description: Este tópico mostra-lhe como mover dados entre o Azure Data Explorer e os clusters Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: b358287664ac6d6a3b641e1ab63073810ceb4c40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208617"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Conector azure data explorer para Apache Spark

[Apache Spark](https://spark.apache.org/) é um motor de análise unificado para o processamento de dados em larga escala. O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerido para análise em tempo real em grandes volumes de dados. 

O conector Azure Data Explorer para a Spark é um [projeto de código aberto](https://github.com/Azure/azure-kusto-spark) que pode funcionar em qualquer cluster Spark. Implementa fonte de dados e afunda dados para mover dados através de clusters Azure Data Explorer e Spark. Utilizando o Azure Data Explorer e o Apache Spark, pode construir aplicações rápidas e escaláveis que visem cenários orientados para dados. Por exemplo, machine learning (ML), Extract-Transform-Load (ETL) e Log Analytics. Com o conector, o Azure Data Explorer torna-se uma loja de dados válida para operações padrão de fonte spark e sink, tais como escrever, ler e escreverStream.

Pode escrever para o Azure Data Explorer em modo de lote ou streaming. A leitura do Azure Data Explorer suporta a poda de colunas e predicado pushdown, que filtra os dados no Azure Data Explorer, reduzindo o volume de dados transferidos.

Este tópico descreve como instalar e configurar o conector Azure Data Explorer Spark e mover dados entre o Azure Data Explorer e os clusters Apache Spark.

> [!NOTE]
> Embora alguns dos exemplos abaixo se refiram a um cluster [Azure Databricks](https://docs.azuredatabricks.net/) Spark, o conector Azure Data Explorer Spark não assume dependências diretas de Databricks ou qualquer outra distribuição spark.

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um cluster e base de dados azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) 
* Criar um cluster do Spark
* Instale a biblioteca de conector Azure Data Explorer:
    * Bibliotecas pré-construídas para [Faísca 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Maven repo](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3.x](https://maven.apache.org/download.cgi) instalado

> [!TIP]
> As versões 2.3.x também são suportadas, mas podem exigir algumas alterações nas dependências de pom.xml.

## <a name="how-to-build-the-spark-connector"></a>Como construir o conector Spark

> [!NOTE]
> Este passo é opcional. Se estiver a usar bibliotecas pré-construídas, vá para a [configuração](#spark-cluster-setup)do cluster Spark .

### <a name="build-prerequisites"></a>Construir pré-requisitos

1. Instale as bibliotecas listadas em [dependências,](https://github.com/Azure/azure-kusto-spark#dependencies) incluindo as [seguintes bibliotecas Kusto Java SDK:](/azure/kusto/api/java/kusto-java-client-library)
    * [Cliente de Dados Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Cliente Kusto Ingest](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Consulte [esta fonte](https://github.com/Azure/azure-kusto-spark) para a construção do Conector de Faíscas.

1. Para aplicações Scala/Java utilizando definições de projeto Maven, ligue a sua aplicação com o seguinte artefacto (versão mais recente pode diferir):
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>Construir comandos

Para construir um frasco e fazer todos os testes:

```
mvn clean package
```

Para construir frascos, faça todos os testes e instale o jarro no seu repositório local de Maven:

```
mvn clean install
```

Para mais informações, consulte a [utilização do conector](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Configuração do cluster de faíscas

> [!NOTE]
> Recomenda-se utilizar o mais recente conector Azure Data Explorer Spark ao executar os seguintes passos.

1. Configure as seguintes definições de cluster spark, com base no cluster Azure Databricks utilizando o Spark 2.4.4 e o Scala 2.11:

    ![Definições de cluster de tijolos de dados](media/spark-connector/databricks-cluster.png)
    
1. Instale a mais recente biblioteca de conectores spark-kusto de Maven:
    
    ![Importa](media/spark-connector/db-libraries-view.png) ![bibliotecas Selecione Spark-Kusto-Connector](media/spark-connector/db-dependencies.png)

1. Verifique se todas as bibliotecas necessárias estão instaladas:

    ![Verificar bibliotecas instaladas](media/spark-connector/db-libraries-view.png)

1. Para a instalação utilizando um ficheiro JAR, verifique se foram instaladas dependências adicionais:

    ![Adicionar dependências](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Autenticação

O conector Azure Data Explorer Spark permite-lhe autenticar com o Azure Ative Directory (Azure AD) utilizando um dos seguintes métodos:
* Uma [aplicação Azure AD](#azure-ad-application-authentication)
* Um símbolo de [acesso da AD Azure](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [Autenticação do dispositivo](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (para cenários de não produção)
* Um [cofre de chave Azure](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) Para aceder ao recurso Key Vault, instale o pacote de cofre sinuoso e forneça credenciais de aplicação.

### <a name="azure-ad-application-authentication"></a>Autenticação da aplicação Azure AD

A autenticação da aplicação Azure AD é o método de autenticação mais simples e comum e é recomendado para o conector Azure Data Explorer Spark.

|Propriedades  |Descrição  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identificador de aplicação Azure AD (cliente).      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Autoridade de autenticação da AD Azure. Id azure AD Diretório (inquilino).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Chave de aplicação Azure AD para o cliente.     |

### <a name="azure-data-explorer-privileges"></a>Privilégios azure Data Explorer

Conceda os seguintes privilégios num cluster Azure Data Explorer:

* Para a leitura (fonte de dados), a identidade Azure AD deve ter privilégios de *espectador na* base de dados alvo, ou privilégios *administrativos* na tabela alvo.
* Para a escrita (data sink), a identidade Azure AD deve ter privilégios *ingestornamente* na base de dados-alvo. Deve também ter privilégios *de utilizador* na base de dados-alvo para criar novas tabelas. Se a tabela-alvo já existir, deve configurar os privilégios *administrativos* na tabela-alvo.
 
Para obter mais informações sobre as principais funções do Azure Data Explorer, consulte [a autorização baseada em papéis.](/azure/kusto/management/access-control/role-based-authorization) Para gerir funções de segurança, consulte a gestão de papéis de [segurança.](/azure/kusto/management/security-roles)

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Pia de faísca: escrita para Azure Data Explorer

1. Configurar parâmetros de pia:

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

1. Escreva Spark DataFrame para o cluster Do Explorador de Dados do Azure como lote:

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
   
1. Escreva dados de streaming:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
        
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Fonte de faísca: leitura do Azure Data Explorer

1. Ao ler [pequenas quantidades de dados,](/azure/kusto/concepts/querylimits)defina a consulta de dados:

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

1. Opcional: **Se** fornecer o armazenamento transitório de blob (e não o Azure Data Explorer) as bolhas são criadas são da responsabilidade do chamador. Isto inclui o fornecimento do armazenamento, as chaves de acesso rotativas e a asolação de artefactos transitórios. 
    O módulo KustoBlobStorageUtils contém funções de ajudante para apagar bolhas com base em coordenadas de conta e contentores e credenciais de conta, ou um URL SAS completo com permissões de escrita, leitura e lista. Quando o RDD correspondente já não é necessário, cada transação armazena artefactos de bolha transitórianum diretório separado. Este diretório é capturado como parte dos registos de informação de transação de leitura reportados no nó do Spark Driver.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    No exemplo acima, o Cofre chave não é acedido usando a interface do conector; é utilizado um método mais simples de utilização dos segredos databricks.

1. Leia no Azure Data Explorer.

    * Se **fornecer** o armazenamento transitório de blob, leia a partir do Azure Data Explorer da seguinte forma:

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

    * Se o **Azure Data Explorer** fornecer o armazenamento transitório de blob, leia o Azure Data Explorer da seguinte forma:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Conectador de Faíscas do Explorador](https://github.com/Azure/azure-kusto-spark/tree/master/docs) de Dados Azure
* [Código de amostra para Java e Python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
