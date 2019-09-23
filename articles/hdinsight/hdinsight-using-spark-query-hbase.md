---
title: Usar o Spark para ler e gravar dados do HBase – Azure HDInsight
description: Use o conector HBase do Spark para ler e gravar dados de um cluster Spark em um cluster HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.openlocfilehash: e6b3fc4f9badeedbed55f89702933b41a952977b
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180798"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Utilizar o Apache Spark para ler e escrever dados do Apache HBase

O Apache HBase é normalmente consultado com sua API de nível inferior (verificações, gets e colocam) ou com uma sintaxe SQL usando Apache Phoenix. O Apache também fornece o Apache Spark conector HBase, que é uma alternativa conveniente e de alto desempenho para consultar e modificar dados armazenados pelo HBase.

## <a name="prerequisites"></a>Pré-requisitos

* Dois clusters HDInsight separados implantados na mesma rede virtual. Um HBase e um Spark com pelo menos o Spark 2,1 (HDInsight 3,6) instalado. Para obter mais informações, consulte [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](hdinsight-hadoop-linux-use-ssh-unix.md).

* O [esquema de URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) para o armazenamento primário de clusters. Isso seria wasb://para o armazenamento de BLOBs do Azure, abfs://para Azure Data Lake Storage Gen2 ou adl://para Azure Data Lake Storage Gen1. Se a transferência segura estiver habilitada para o armazenamento de BLOB, `wasbs://`o URI será.  Consulte também a [transferência segura](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Processo geral

O processo de alto nível para habilitar o cluster Spark para consultar o cluster HDInsight é o seguinte:

1. Prepare alguns dados de exemplo no HBase.
2. Adquira o arquivo HBase-site. XML da pasta de configuração do cluster do HBase (/etc/HBase/conf).
3. Coloque uma cópia de HBase-site. xml na pasta de configuração do Spark 2 (/etc/spark2/conf).
4. Execute `spark-shell` a referência ao conector HBase do Spark por suas coordenadas `packages` do Maven na opção.
5. Defina um catálogo que mapeia o esquema do Spark para o HBase.
6. Interaja com os dados do HBase usando as APIs RDD ou dataframe.

## <a name="prepare-sample-data-in-apache-hbase"></a>Preparar dados de exemplo no Apache HBase

Nesta etapa, você cria e preenche uma tabela no Apache HBase que você pode consultar usando o Spark.

1. Use o `ssh` comando para se conectar ao cluster HBase. Edite o comando a seguir `HBASECLUSTER` substituindo pelo nome do cluster HBase e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Use o `hbase shell` comando para iniciar o shell interativo do HBase. Digite o seguinte comando em sua conexão SSH:

    ```bash
    hbase shell
    ```

3. Use o `create` comando para criar uma tabela do HBase com famílias de duas colunas. Introduza o seguinte comando:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Use o `put` comando para inserir valores em uma coluna especificada em uma linha especificada em uma tabela específica. Introduza o seguinte comando:

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

5. Use o `exit` comando para interromper o shell interativo do HBase. Introduza o seguinte comando:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Copiar HBase-site. xml para o cluster Spark

Copie o HBase-site. XML do armazenamento local para a raiz do armazenamento padrão do seu cluster Spark.  Edite o comando a seguir para refletir sua configuração.  Em seguida, na sessão SSH aberta para o cluster HBase, digite o comando:

| Valor da sintaxe | Novo valor|
|---|---|
|[Esquema de URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Modifique para refletir seu armazenamento.  A sintaxe abaixo é para o armazenamento de BLOBs com a transferência segura habilitada.|
|`SPARK_STORAGE_CONTAINER`|Substitua pelo nome do contêiner de armazenamento padrão usado para o cluster Spark.|
|`SPARK_STORAGE_ACCOUNT`|Substitua pelo nome da conta de armazenamento padrão usada para o cluster Spark.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Em seguida, saia da conexão SSH para o cluster HBase.

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Colocar HBase-site. xml em seu cluster Spark

1. Conecte-se ao nó principal do seu cluster Spark usando SSH.

2. Insira o comando a seguir para `hbase-site.xml` copiar do armazenamento padrão do seu cluster Spark para a pasta de configuração do Spark 2 no armazenamento local do cluster:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Executar o Shell do Spark referenciando o conector HBase do Spark

1. Na sessão SSH aberta para o cluster Spark, digite o comando a seguir para iniciar um shell do Spark:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Mantenha essa instância do shell do Spark aberta e continue na próxima etapa.

## <a name="define-a-catalog-and-query"></a>Definir um catálogo e uma consulta

Nesta etapa, você define um objeto de catálogo que mapeia o esquema do Apache Spark para o Apache HBase.  

1. No Shell do Spark aberto, insira as seguintes `import` instruções:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

2. Digite o comando a seguir para definir um catálogo para a tabela de contatos que você criou no HBase:

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

     a. Defina um esquema de catálogo para a tabela do `Contacts`HBase denominada.  
     b. Identifique o RowKey como `key`e mapeie os nomes de coluna usados no Spark para a família de colunas, o nome da coluna e o tipo de coluna como usado no HBase.  
     c. O RowKey também precisa ser definido em detalhes como uma coluna nomeada (`rowkey`), que tem uma família `cf` de colunas específica do `rowkey`.  

3. Insira o comando a seguir para definir um método que fornece um dataframe em `Contacts` toda a tabela no HBase:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

4. Crie uma instância do dataframe:

    ```scala
    val df = withCatalog(catalog)
    ```  

5. Consultar o dataframe:

    ```scala
    df.show()
    ```

6. Você deve ver duas linhas de dados:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Registre uma tabela temporária para que você possa consultar a tabela do HBase usando o Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

8. Emitir uma consulta SQL na `contacts` tabela:

    ```scala
    val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
    query.show()
    ```

9. Você deverá ver os resultados como estes:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Inserir novos dados

1. Para inserir um novo registro de contato, defina `ContactRecord` uma classe:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

2. Crie uma instância do `ContactRecord` e coloque-a em uma matriz:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

3. Salve a matriz de novos dados no HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

4. Examine os resultados:

    ```scala  
    df.show()
    ```

5. Deverá ver um resultado como este:

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

6. Feche o Shell do Spark digitando o seguinte comando:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Passos seguintes

* [Conector HBase Apache Spark](https://github.com/hortonworks-spark/shc)
