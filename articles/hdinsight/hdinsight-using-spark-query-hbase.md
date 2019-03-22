---
title: Utilizar o Spark para ler e escrever dados de HBase - Azure HDInsight
description: Utilize o conector de HBase do Spark para ler e gravar dados num cluster do Spark para um cluster do HBase.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 200691f7af16e82d554d0e1e019b6a4e5c75949f
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260141"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Utilizar o Apache Spark para ler e escrever dados do Apache HBase

O Apache HBase, normalmente, é consultado com a API de nível inferior (análises, obtém e coloca) ou com uma sintaxe SQL com o Apache Phoenix. Apache também fornece o conector de HBase do Apache Spark, que é uma prática e alternativa de alto desempenho para consultar e modificar dados armazenados pelo HBase.

## <a name="prerequisites"></a>Pré-requisitos

* Dois separados, pelo menos, clusters, um HBase e Spark um com o HDInsight Spark 2.1 (HDInsight 3.6) instalado.
* O cluster do Spark tem de comunicar diretamente com o cluster do HBase com uma latência mínima, para que a configuração recomendada é implantar ambos os clusters na mesma rede virtual. Para obter mais informações, consulte [baseado em Linux criar clusters no HDInsight com o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).
* Um cliente SSH. Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
* O [esquema de URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) para seu armazenamento primário de clusters. Isto poderá ser wasb: / / para o armazenamento de Blobs do Azure, abfs: / / para a geração 2 de armazenamento do Azure Data Lake ou adl: / / para a geração 1 de armazenamento do Azure Data Lake. Se a transferência segura é ativada para o armazenamento de BLOBs ou de geração 2 de armazenamento do Data Lake, o URI seria wasbs: / / ou abfss: / /, respectivamente Consulte também [transferência segura](../storage/common/storage-require-secure-transfer.md).


## <a name="overall-process"></a>Processo geral

O processo de alto nível para ativar o seu cluster do Spark para consultar o seu cluster do HDInsight é o seguinte:

1. Prepare-se alguns dados de exemplo no HBase.
2. Adquira já o ficheiro hbase-site da sua pasta de configuração de cluster do HBase (/ etc/hbase/conf).
3. Coloque uma cópia do hbase-site na sua pasta de configuração de Spark 2 (/ etc/spark2/conf).
4. Execute `spark-shell` que referencia o conector do Spark HBase pelo respetivo Maven coordena no `packages` opção.
5. Defina um catálogo que mapeia o esquema do Spark para HBase.
6. Interagir com os dados do HBase com o RDD ou APIs de pacote de dados.

## <a name="prepare-sample-data-in-apache-hbase"></a>Preparar os dados de exemplo no Apache HBase

Neste passo, pode criar e preencher uma tabela simples no Apache HBase, que, em seguida, pode consultar com o Spark.

1. Ligar ao nó principal do cluster do HBase através de SSH. Para obter mais informações, consulte [ligar ao HDInsight através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  Editar o comando abaixo, substituindo `HBASECLUSTER` com o nome do cluster do HBase, `sshuser` com o ssh usuário o nome de conta e, em seguida, introduza o comando.

    ```
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Introduza o comando abaixo para iniciar o shell de HBase:

        hbase shell

3. Introduza o comando abaixo para criar uma `Contacts` tabela com as famílias de coluna `Personal` e `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Introduza os comandos abaixo para carregar algumas linhas de exemplo de dados:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

5. Introduza o comando abaixo para sair da shell de HBase:

        exit 

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Copie o site do hbase para cluster do Spark
Copie o site de hbase do armazenamento local para a raiz do armazenamento de predefinido do seu cluster do Spark.  Edite o comando abaixo para refletir a configuração.  Em seguida, a abrir sessão SSH para o cluster do HBase, introduza o comando:

| Valor de sintaxe | Novo valor|
|---|---|
|[Esquema de URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Modificar para refletir o seu armazenamento.  A sintaxe abaixo é para armazenamento de Blobs com transferência segura ativada.|
|`SPARK_STORAGE_CONTAINER`|Substitua o nome de contentor de armazenamento predefinido utilizado para o cluster do Spark.|
|`SPARK_STORAGE_ACCOUNT`|Substitua o nome de conta de armazenamento predefinida utilizado para o cluster do Spark.|

```
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Colocar o site de hbase no seu cluster do Spark

1. Ligar ao nó principal do cluster do Spark através de SSH.

2. Introduza o comando abaixo para copiar `hbase-site.xml` do armazenamento de predefinido do seu cluster do Spark para a pasta de configuração de Spark 2 no armazenamento local do cluster:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Execute a Shell do Spark que referencia o conector do Spark HBase

1. Da sua sessão SSH aberto para o cluster do Spark, introduza o comando abaixo para iniciar um shell do spark:

    ```
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Mantenha esta instância do Shell do Spark aberta e avance para o passo seguinte.

## <a name="define-a-catalog-and-query"></a>Definir um catálogo e a consulta

Neste passo, vai definir um objeto de catálogo que mapeia o esquema do Apache Spark para o Apache HBase.  

1. Na Shell do Spark open, introduza o seguinte `import` instruções:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

2. Introduza o comando abaixo para definir um catálogo para a tabela de contactos HBase que criou:

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

    O código executa o seguinte:  

     a. Definir um esquema de catálogo para a tabela HBase com o nome `Contacts`.  
     b. Identificar o rowkey como `key`e mapear os nomes das colunas utilizadas no Spark para a família de colunas, o nome da coluna e o tipo de coluna, conforme descrito no HBase.  
     c. O rowkey também precisa de ser definido em detalhes como uma coluna com nome (`rowkey`), que tem uma família de colunas específicas `cf` de `rowkey`.  

3. Introduza o comando abaixo para definir um método que fornece um DataFrame em torno de seu `Contacts` tabela HBase:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

4. Crie uma instância do pacote de dados:

    ```scala
    val df = withCatalog(catalog)
    ```  

5. O pacote de dados de consulta:

    ```scala
    df.show()
    ```

6. Deverá ver duas linhas de dados:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Registe-se uma tabela temporária que possa consultar a tabela HBase com o Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

8. Emitir uma consulta SQL em relação a `contacts` tabela:

    ```scala
    val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
    query.show()
    ```

9. Deverá ver resultados como estes:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Insira novos dados

1. Para inserir um novo registo de contacto, definir um `ContactRecord` classe:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

2. Criar uma instância do `ContactRecord` e colocá-la numa matriz:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

3. Guarde a matriz de novos dados no HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

4. Examine os resultados:

    ```scala  
    df.show()
    ```

5. Deverá ver um resultado como este:

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>Passos Seguintes

* [Conector do HBase do Apache Spark](https://github.com/hortonworks-spark/shc)
