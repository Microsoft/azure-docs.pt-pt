---
title: Use a Spark para ler e escrever dados HBase - Azure HDInsight
description: Utilize o Conector Spark HBase para ler e escrever dados de um cluster Spark para um cluster HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/24/2020
ms.openlocfilehash: 888f24e13ce67c878592068927383dd8cbfefa60
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623094"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Utilizar o Apache Spark para ler e escrever dados do Apache HBase

Apache HBase é tipicamente consultado com a sua API de baixo nível (digitalizações, recebe e coloca) ou com uma sintaxe SQL usando Apache Phoenix. A Apache também fornece o Conector HBase Apache Spark, que é uma alternativa conveniente e performante para consulta e modificação de dados armazenados pela HBase.

## <a name="prerequisites"></a>Pré-requisitos

* Dois clusters HDInsight separados implantados na mesma [rede virtual](./hdinsight-plan-virtual-network-deployment.md). Uma HBase e uma Faísca com pelo menos spark 2.1 (HDInsight 3.6) instalada. Para obter mais informações, consulte [Create Linux clusters em HDInsight utilizando o portal Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

* Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* O [esquema URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) para o armazenamento primário dos seus clusters. Este esquema seria wasb:// para o Armazenamento de Blob Azure, abfs:// para o Azure Data Lake Storage Gen2 ou adl:// para o Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o Armazenamento Blob, o URI será `wasbs://`.  Ver também, [transferência segura.](../storage/common/storage-require-secure-transfer.md)

## <a name="overall-process"></a>Processo geral

O processo de alto nível para permitir que o seu cluster Spark questione o seu cluster HDInsight é o seguinte:

1. Prepare alguns dados da amostra na Base HBase.
2. Adquira o ficheiro hbase-site.xml da sua pasta de configuração de cluster HBase (/etc/hbase/conf).
3. Coloque uma cópia do hbase-site.xml na sua pasta de configuração Spark 2 (/etc/spark2/conf).
4. Execute `spark-shell` referindo-se ao Conector Spark HBase pelas suas coordenadas Maven na opção `packages`.
5. Defina um catálogo que mapeie o esquema de Spark para HBase.
6. Interaja com os dados da Base H utilizando as APIs RDD ou DataFrame.

## <a name="prepare-sample-data-in-apache-hbase"></a>Preparar dados da amostra em Apache HBase

Neste passo, cria-se e povoa-se uma tabela em Apache HBase que pode consultar usando o Spark.

1. Utilize o comando `ssh` para ligar ao seu cluster HBase. Editar o comando abaixo substituindo `HBASECLUSTER` com o nome do seu cluster HBase e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Utilize o comando `hbase shell` para iniciar a concha interativa HBase. Introduza o seguinte comando na sua ligação SSH:

    ```bash
    hbase shell
    ```

3. Utilize o comando `create` para criar uma tabela HBase com famílias de duas colunas. Introduza o seguinte comando:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Utilize o comando `put` para inserir valores numa coluna especificada numa determinada linha numa determinada tabela. Introduza o seguinte comando:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. Utilize o comando `exit` para parar a concha interativa HBase. Introduza o seguinte comando:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Copiar hbase-site.xml para cluster Spark

Copie o hbase-site.xml do armazenamento local para a raiz do armazenamento padrão do seu cluster Spark.  Edite o comando abaixo para refletir a sua configuração.  Em seguida, da sua sessão aberta sSH para o cluster HBase, insira o comando:

| Valor da sintaxe | Novo valor|
|---|---|
|[Regime URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Modifique para refletir o seu armazenamento.  A sintaxe abaixo é para armazenamento de blob com transferência segura ativada.|
|`SPARK_STORAGE_CONTAINER`|Substitua-a com o nome do recipiente de armazenamento predefinido utilizado para o cluster Spark.|
|`SPARK_STORAGE_ACCOUNT`|Substitua-a com o nome da conta de armazenamento predefinida utilizado para o cluster Spark.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Em seguida, saia da sua ligação ssh ao seu cluster HBase.

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Coloque hbase-site.xml no seu cluster Spark

1. Ligue-se ao nó da cabeça do seu cluster Spark utilizando SSH. Edite o comando abaixo substituindo `SPARKCLUSTER` com o nome do seu cluster Spark e, em seguida, introduza o comando:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. Introduza o comando abaixo para copiar `hbase-site.xml` do armazenamento predefinido do cluster Spark para a pasta de configuração Spark 2 no armazenamento local do cluster:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Executar Spark Shell referindo-se ao Conector HBase spark

1. Da sua sessão sSH aberta até ao cluster Spark, insira o comando abaixo para iniciar uma casca de faísca:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Mantenha esta instância Spark Shell aberta e continue até ao próximo passo.

## <a name="define-a-catalog-and-query"></a>Definir um Catálogo e Consulta

Neste passo, você define um objeto de catálogo que mapeia o esquema de Apache Spark para Apache HBase.  

1. Na sua Spark Shell aberta, insira as seguintes declarações `import`:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Insira o comando abaixo para definir um catálogo para a tabela Contactos que criou na HBase:

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    O código faz o seguinte:  

     a. Defina um esquema de catálogo para a tabela HBase chamada `Contacts`.  
     b. Identifique a tecla de remo como `key`, e mapeie os nomes da coluna utilizados em Spark para a família da coluna, nome da coluna e tipo de coluna como utilizado na Base H.  
     c. A chave de linha também deve ser definida em detalhe como uma coluna nomeada (`rowkey`), que tem uma família de colunas específica `cf` de `rowkey`.  

1. Introduza o comando abaixo para definir um método que forneça um DataFrame em torno da tabela `Contacts` em HBase:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. Criar uma instância do DataFrame:

    ```scala
    val df = withCatalog(catalog)
    ```  

1. Consulta do DataFrame:

    ```scala
    df.show()
    ```

    Deve ver duas linhas de dados:

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. Registe uma tabela temporária para que possa consultar a tabela HBase utilizando o Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

1. Emita uma consulta SQL contra a tabela `contacts`:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    Devia ver resultados como estes:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Inserir novos dados

1. Para inserir um novo registo de contacto, defina uma classe `ContactRecord`:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. Crie um exemplo de `ContactRecord` e coloque-o numa matriz:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Guarde o conjunto de novos dados para a HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. Examinar os resultados:

    ```scala  
    df.show()
    ```

    Deverá ver um resultado como este:

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. Feche a casca de faísca entrando no seguinte comando:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Passos seguintes

* [Conector Apache Spark HBase](https://github.com/hortonworks-spark/shc)
