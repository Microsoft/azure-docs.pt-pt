---
title: Utilizar o Spark para ler e escrever dados de HBase - Azure HDInsight
description: Utilize o conector de HBase do Spark para ler e gravar dados num cluster do Spark para um cluster do HBase.
services: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 8fb0a4bb778c86ebde337a239629c412636187ec
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576689"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Utilizar o Apache Spark para ler e escrever dados do Apache HBase

O Apache HBase, normalmente, é consultado com a API de nível inferior (análises, obtém e coloca) ou com uma sintaxe SQL com o Apache Phoenix. Apache também fornece o conector de HBase do Apache Spark, que é uma prática e alternativa de alto desempenho para consultar e modificar dados armazenados pelo HBase.

## <a name="prerequisites"></a>Pré-requisitos

* Clusters do HDInsight, um HBase e Spark um dois separados com o Spark 2.1 (HDInsight 3.6) instalado.
* O cluster do Spark tem de comunicar diretamente com o cluster do HBase com uma latência mínima, para que a configuração recomendada é implantar ambos os clusters na mesma rede virtual. Para obter mais informações, consulte [baseado em Linux criar clusters no HDInsight com o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).
* Acesso SSH a cada cluster.
* Acesso ao armazenamento de predefinido de cada cluster.

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

1. Ligar ao nó principal do cluster do HBase através de SSH. Para obter mais informações, consulte [ligar ao HDInsight através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
2. Execute a shell de HBase:

        hbase shell

3. Criar uma `Contacts` tabela com as famílias de coluna `Personal` e `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Algumas linhas de exemplo de dados de carga:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

## <a name="acquire-hbase-sitexml-from-your-hbase-cluster"></a>Adquirir hbase-site do seu cluster do HBase

1. Ligar ao nó principal do cluster do HBase através de SSH.
2. Copie o site de hbase do armazenamento local para a raiz do armazenamento de predefinido do seu cluster HBase:

        hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml /

3. Navegue para o seu cluster do HBase com o [portal do Azure](https://portal.azure.com).
4. Selecione as contas de armazenamento. 

    ![Contas de armazenamento](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

5. Selecione a conta de armazenamento na lista que tem uma marca de verificação na coluna de predefinição.

    ![Conta de armazenamento predefinida](./media/hdinsight-using-spark-query-hbase/default-storage.png)

6. No painel de conta de armazenamento, selecione o mosaico de Blobs.

    ![Mosaico de BLOBs](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

7. Na lista de contentores, selecione o contentor que é utilizado pelo cluster do HBase.
8. Na lista de ficheiros, selecione `hbase-site.xml`.

    ![HBase-site.xml](./media/hdinsight-using-spark-query-hbase/hbase-site-xml.png)

9. No painel de propriedades Blob, selecione transferir e guardar `hbase-site.xml` para uma localização no seu computador local.

    ![Transferência](./media/hdinsight-using-spark-query-hbase/download.png)

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Colocar o site de hbase no seu cluster do Spark

1. Navegue para o seu cluster do Spark com o [portal do Azure](https://portal.azure.com).
2. Selecione as contas de armazenamento.

    ![Contas de armazenamento](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

3. Selecione a conta de armazenamento na lista que tem uma marca de verificação na coluna de predefinição.

    ![Conta de armazenamento predefinida](./media/hdinsight-using-spark-query-hbase/default-storage.png)

4. No painel de conta de armazenamento, selecione o mosaico de Blobs.

    ![Mosaico de BLOBs](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

5. Na lista de contentores, selecione o contentor que é utilizado pelo cluster do Spark.
6. Selecione o carregamento.

    ![Carregar](./media/hdinsight-using-spark-query-hbase/upload.png)

7. Selecione o `hbase-site.xml` ficheiro que transferiu anteriormente para o computador local.

    ![Carregar o site de hbase](./media/hdinsight-using-spark-query-hbase/upload-selection.png)

8. Selecione o carregamento.
9. Ligar ao nó principal do cluster do Spark através de SSH.
10. Cópia `hbase-site.xml` do armazenamento de predefinido do seu cluster do Spark para a pasta de configuração de Spark 2 no armazenamento local do cluster:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Execute a Shell do Spark que referencia o conector do Spark HBase

1. Ligar ao nó principal do cluster do Spark através de SSH.
2. Inicie a shell do spark, especificando o pacote de conector do Spark HBase:

        spark-shell --packages com.hortonworks:shc-core:1.1.0-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/

3. Mantenha esta instância do Shell do Spark aberta e avance para o passo seguinte.

## <a name="define-a-catalog-and-query"></a>Definir um catálogo e a consulta

Neste passo, vai definir um objeto de catálogo que mapeia o esquema do Apache Spark para o Apache HBase. 

1. Na Shell do Spark aberta, execute o seguinte `import` instruções:

        import org.apache.spark.sql.{SQLContext, _}
        import org.apache.spark.sql.execution.datasources.hbase._
        import org.apache.spark.{SparkConf, SparkContext}
        import spark.sqlContext.implicits._

2. Defina um catálogo para a tabela de contactos HBase que criou:
    1. Definir um esquema de catálogo para a tabela HBase com o nome `Contacts`.
    2. Identificar o rowkey como `key`e mapear os nomes das colunas utilizadas no Spark para a família de colunas, o nome da coluna e o tipo de coluna, conforme descrito no HBase.
    3. O rowkey também precisa de ser definido em detalhes como uma coluna com nome (`rowkey`), que tem uma família de colunas específicas `cf` de `rowkey`.

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

3. Definir um método que fornece um DataFrame em torno de seu `Contacts` tabela HBase:

            def withCatalog(cat: String): DataFrame = {
                spark.sqlContext
                .read
                .options(Map(HBaseTableCatalog.tableCatalog->cat))
                .format("org.apache.spark.sql.execution.datasources.hbase")
                .load()
            }

4. Crie uma instância do pacote de dados:

        val df = withCatalog(catalog)

5. O pacote de dados de consulta:

        df.show()

6. Deverá ver duas linhas de dados:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Registe-se uma tabela temporária que possa consultar a tabela HBase com o Spark SQL:

        df.registerTempTable("contacts")

8. Emitir uma consulta SQL em relação a `contacts` tabela:

        val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
        query.show()

9. Deverá ver resultados como estes:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Insira novos dados

1. Para inserir um novo registo de contacto, definir um `ContactRecord` classe:

        case class ContactRecord(
            rowkey: String,
            officeAddress: String,
            officePhone: String,
            personalName: String,
            personalPhone: String
            )

2. Criar uma instância do `ContactRecord` e colocá-la numa matriz:

        val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

        var newData = new Array[ContactRecord](1)
        newData(0) = newContact

3. Guarde a matriz de novos dados no HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog)).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

4. Examine os resultados:
    
        df.show()

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
