---
title: Utilize o conector de Explorador de dados do Azure para o Apache Spark para mover dados entre clusters do Explorador de dados do Azure e do Spark.
description: Este tópico mostra como mover dados entre clusters do Explorador de dados do Azure e do Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 8cb1489a0663556f9dd9e6026a036df2468d656d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928336"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Conector do Explorador de dados do Azure para o Apache Spark (pré-visualização)

[Apache Spark](https://spark.apache.org/) são um motor de análise unificada para processamento de dados em grande escala. Explorador de dados do Azure é um serviço de análise de dados rápido, totalmente gerido para análise em tempo real em grandes volumes de dados. 

Conector do Explorador de dados do Azure para o Spark implementa a origem de dados e de sink de dados para mover dados entre clusters do Explorador de dados do Azure e do Spark para usar as respetivas capacidades. Utilizar o Explorador de dados do Azure e do Apache Spark, pode criar aplicações rápidas e escaláveis, destinado a cenários, tais como a aprendizagem automática (ML), Extract-Transform-Load (ETL) e do Log Analytics de orientados a dados. Escrever no Explorador de dados do Azure pode ser feito em lote e o modo de transmissão em fluxo.
Ler a partir do Explorador de dados do Azure suporta a remoção de coluna e propagação de predicado, que reduz o volume de dados transferidos ao filtrar os dados no Explorador de dados do Azure.

Conector do Spark do Explorador de dados do Azure é um [projeto de código-fonte aberto](https://github.com/Azure/azure-kusto-spark) que podem ser executados em qualquer cluster do Spark.

> [!NOTE]
> Embora alguns dos exemplos abaixo consultar a uma [Azure Databricks](https://docs.azuredatabricks.net/) cluster do Spark, o conector do Spark de Explorador de dados do Azure não tem dependências diretas no Databricks ou qualquer outra distribuição de Spark.

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um cluster do Explorador de dados do Azure e a base de dados](/azure/data-explorer/create-cluster-database-portal) 
* Criar um cluster do Spark
* Instalar a biblioteca de conectores do Explorador de dados do Azure e as bibliotecas listadas na [dependências](https://github.com/Azure/azure-kusto-spark#dependencies) incluindo o seguinte procedimento [SDK de Java do Kusto](/azure/kusto/api/java/kusto-java-client-library) bibliotecas:
    * [Kusto Data Client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Cliente de ingestão de Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Pré-criadas bibliotecas para [2.4 do Spark, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>Como criar o conector do Spark

Conector do Spark pode ser criado a partir [origens](https://github.com/Azure/azure-kusto-spark) conforme detalhado abaixo.

> [!NOTE]
> Este passo é opcional. Se estiver a utilizar bibliotecas pré-criados aceda a [a configuração de cluster do Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Pré-requisitos de compilação

* SDK do Java 1.8 instalado
* [Maven 3.x](https://maven.apache.org/download.cgi) instalado
* Versão do Apache Spark 2.4.0 ou superior

> [!TIP]
> versões de 2.3.x também são suportadas, mas podem exigir algumas alterações nas dependências pom. XML.

Para aplicações de Scala/Java com definições de projeto Maven, ligue a sua aplicação com o artefacto seguinte (versão mais recente pode ser diferente):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>Criar comandos de introdução

Para criar jar e executar todos os testes:

```
mvn clean package
```

Para criar jar, execute todos os testes e instalar jar ao seu repositório local do Maven:

```
mvn clean install
```

Para obter mais informações, consulte [utilização de conector](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Configuração de cluster do Spark

> [!NOTE]
> Recomenda-se para utilizar a versão mais recente de conector do Spark do Explorador de dados do Azure, ao efetuar os seguintes passos:

1. Defina o Spark seguinte definições de cluster, com base num cluster do Azure Databricks com o Spark 2.4 e Scala 2.11: 

    ![Definições de cluster do Databricks](media/spark-connector/databricks-cluster.png)

1. Importe a biblioteca de conectores do Explorador de dados do Azure:

    ![Biblioteca do Explorador de dados do Azure de importação](media/spark-connector/db-create-library.png)

1. Adicione dependências adicionais:

    ![Adicionar dependências](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > Encontra-se a versão de lançamento de java correta para cada versão do Spark [aqui](https://github.com/Azure/azure-kusto-spark#dependencies).

1. Certifique-se de que todas as necessárias bibliotecas são instaladas:

    ![Certifique-se de bibliotecas instaladas](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Autenticação

Conector do Spark do Explorador de dados do Azure permite-lhe autenticar com a utilização do Azure Active Directory (Azure AD) um [aplicação do Azure AD](#azure-ad-application-authentication), [token de acesso do Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [autenticação do dispositivo ](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (para cenários de não produção), ou [do Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). O utilizador tem de instalar o pacote do Cofre de chaves do azure e forneça as credenciais de aplicação para aceder ao recurso do Key Vault.

### <a name="azure-ad-application-authentication"></a>Autenticação de aplicação do Azure AD

Método de autenticação simples e comuns a maioria dos. Este método é recomendado para utilização de conector do Spark de Explorador de dados do Azure.

|Propriedades  |Descrição  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identificador de aplicação (cliente) do Azure AD.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Autoridade de autenticação do Azure AD. ID de diretório do AD (inquilino) do Azure.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Existe uma chave da aplicação de AD do Azure para o cliente.     |

### <a name="azure-data-explorer-privileges"></a>Privilégios de Explorador de dados do Azure

Os seguintes privilégios devem ser concedidos num Cluster do Explorador de dados do Azure:

* Para ler (origem de dados), a aplicação Azure AD tem de ter *Visualizador* privilégios no banco de dados de destino, ou *administrador* privilégios na tabela de destino.
* Para escrever (sink de dados), a aplicação Azure AD tem de ter *ingestor* privilégios no banco de dados de destino. Ele também tem de ter *utilizador* privilégios no banco de dados de destino para criar novas tabelas. Se a tabela de destino já existir, *administrador* privilégios na tabela de destino podem ser configurados.
 
Para obter mais informações sobre funções de principais do Explorador de dados do Azure, consulte [autorização baseada em funções](/azure/kusto/management/access-control/role-based-authorization). Para gerir funções de segurança, veja [gerenciamento de funções de segurança](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Sink do Spark: Escrever no Explorador de dados do Azure

1. Configure parâmetros de coletor:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47"
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Escreva DataFrame do Spark para o cluster do Explorador de dados do Azure como o batch:

    ```scala
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoOptions.KUSTO_CLUSTER, cluster)
      .option(KustoOptions.KUSTO_DATABASE, database)
      .option(KustoOptions.KUSTO_TABLE, table)
      .option(KustoOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey) 
      .option(KustoOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .save()
    ```

1. Escrever dados de transmissão em fluxo:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    
    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false")
    
    // Write to a Kusto table fro streaming source
    val kustoQ = csvDf
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(Map(
            KustoOptions.KUSTO_CLUSTER -> cluster,
            KustoOptions.KUSTO_TABLE -> table,
            KustoOptions.KUSTO_DATABASE -> database,
            KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId))
          .trigger(Trigger.Once)
    
    kustoQ.start().awaitTermination(TimeUnit.MINUTES.toMillis(8))
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Origem do Spark: Ler a partir do Explorador de dados do Azure

1. Durante a leitura de pequenas quantidades de dados, defina a consulta de dados:

    ```scala
    val conf: Map[String, String] = Map(
          KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
          KustoOptions.KUSTO_QUERY -> s"$table | where (ColB % 1000 == 0) | distinct ColA"      
        )
    
    // Simplified syntax flavor
    import org.apache.spark.sql._
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    import org.apache.spark.SparkConf
    
    val df = spark.read.kusto(cluster, database, "", conf)
    display(df)
    ```

1. Quando Lemos grandes quantidades de dados, tem de fornecer armazenamento de BLOBs transitório. Forneça a chave SAS do contentor de armazenamento, ou nome de conta de armazenamento, a chave da conta e nome do contentor. 

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    No exemplo acima, podemos não aceder ao Cofre de chave usando a interface de conector. Em alternativa, podemos usar um método mais simples de usar os segredos do Databricks.

1. Ler a partir do Explorador de dados do Azure:

    ```scala
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```
