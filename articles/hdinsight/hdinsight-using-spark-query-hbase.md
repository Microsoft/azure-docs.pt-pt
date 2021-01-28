---
title: Use spark para ler e escrever dados HBase - Azure HDInsight
description: Utilize o Conector Spark HBase para ler e escrever dados de um cluster Spark para um cluster HBase.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/12/2020
ms.openlocfilehash: 344caf4080380f5d9dfdaf452798ada6d1dc9f1c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931216"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Utilizar o Apache Spark para ler e escrever dados do Apache HBase

O Apache HBase é tipicamente consultado com a sua API de baixo nível (digitalizações, gets e puts) ou com uma sintaxe SQL usando Apache Phoenix. A Apache também fornece o Conector Apache Spark HBase. O Conector é uma alternativa conveniente e eficiente para consultar e modificar dados armazenados pela HBase.

## <a name="prerequisites"></a>Pré-requisitos

* Dois clusters HDInsight separados implantados na mesma [rede virtual.](./hdinsight-plan-virtual-network-deployment.md) Um HBase e um Spark com pelo menos faísca 2.1 (HDInsight 3.6) instalados. Para obter mais informações, consulte [os clusters baseados em Linux em HDInsight utilizando o portal Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

* O esquema URI para o armazenamento primário dos seus clusters. Este esquema seria wasb:// para o Azure Blob Storage, `abfs://` para a Azure Data Lake Storage Gen2 ou adl:// para a Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o Armazenamento Blob, o URI será `wasbs://` .  Consulte também, [transferência segura.](../storage/common/storage-require-secure-transfer.md)

## <a name="overall-process"></a>Processo geral

O processo de alto nível para permitir que o seu cluster Spark questione o seu cluster HBase é o seguinte:

1. Prepare alguns dados da amostra na Base H.
2. Adquirir o ficheiro hbase-site.xml da sua pasta de configuração do cluster HBase (/etc/hbase/conf) e colocar uma cópia de hbase-site.xml na sua pasta de configuração Spark 2 (/etc/spark2/conf). (OPCIONAL: utilizar o script fornecido pela equipa hdInsight para automatizar este processo)
4. Executar `spark-shell` referência ao Conector Spark HBase pelas coordenadas Maven na `packages` opção.
5. Defina um catálogo que mapeie o esquema de Spark para HBase.
6. Interaja com os dados HBase utilizando as APIs RDD ou DataFrame.

## <a name="prepare-sample-data-in-apache-hbase"></a>Preparar dados de amostra em Apache HBase

Neste passo, você cria e povoa uma tabela em Apache HBase que você pode então consultar usando Spark.

1. Utilize o `ssh` comando para ligar ao seu cluster HBase. Edite o comando abaixo substituindo pelo `HBASECLUSTER` nome do seu cluster HBase e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Utilize o `hbase shell` comando para iniciar a concha interativa HBase. Introduza o seguinte comando na sua ligação SSH:

    ```bash
    hbase shell
    ```

3. Utilize o `create` comando para criar uma tabela HBase com famílias de duas colunas. Introduza o seguinte comando:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Utilize o `put` comando para inserir valores numa coluna especificada numa linha especificada numa tabela específica. Introduza o seguinte comando:

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

5. Utilize o `exit` comando para parar a concha interativa HBase. Introduza o seguinte comando:

    ```hbase
    exit
    ```
    
## <a name="run-scripts-to-set-up-connection-between-clusters"></a>Executar scripts para configurar a ligação entre clusters

Para configurar a comunicação entre clusters, siga os passos abaixo para executar dois scripts nos seus clusters. Estes scripts automatizarão o processo de cópia de ficheiros descrito na secção 'Configurar a comunicação manualmente' abaixo. 

* O script que executar a partir do cluster HBase irá carregar `hbase-site.xml` e hBase ip-mapping informações para o armazenamento padrão anexado ao seu cluster Spark. 
* O guião que você corre do cluster Spark cria dois cron jobs para executar dois scripts ajudantes periodicamente:  
    1.  HBase cron job – descarregue novos `hbase-site.xml` ficheiros e mapeamento IP HBase da conta de armazenamento padrão Spark para nó local
    2.  Spark cron job – verifique se ocorreu uma escala de faísca e se o cluster está seguro. Em caso afirmativo, edite `/etc/hosts` para incluir mapeamento IP HBase armazenado localmente

__NOTA:__ Antes de prosseguir, certifique-se de que adicionou a conta de armazenamento do cluster Spark ao seu cluster HBase como conta de armazenamento secundário. Certifique-se de que os scripts estão em ordem, conforme indicado abaixo.


1. Utilize [a Ação do Script](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) no seu cluster HBase para aplicar as alterações com as seguintes considerações: 


    |Propriedade | Valor |
    |---|---|
    |URI de guião de bash|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-hbase.sh`|
    |Tipo de nó(s)|Region|
    |Parâmetros|`-s SECONDARYS_STORAGE_URL`|
    |Persistiu|sim|

    * `SECONDARYS_STORAGE_URL` é a url do armazenamento padrão do lado da Faísca. Exemplo de parâmetro: `-s wasb://sparkcon-2020-08-03t18-17-37-853z@sparkconhdistorage.blob.core.windows.net`


2.  Utilize a Ação do Script no seu cluster Spark para aplicar as alterações com as seguintes considerações:

    |Propriedade | Valor |
    |---|---|
    |URI de guião de bash|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-spark.sh`|
    |Tipo de nó(s)|Cabeça, Trabalhador, Zookeeper|
    |Parâmetros|`-s "SPARK-CRON-SCHEDULE"` (opcional) `-h "HBASE-CRON-SCHEDULE"` (opcional)|
    |Persistiu|sim|


    * Pode especificar com que frequência pretende que este cluster verifique automaticamente se a atualização é atualizada. Predefinido: -s "*/1 * * * *" -h 0 (Neste exemplo, o cron Spark corre a cada minuto, enquanto o cron HBase não funciona)
    * Uma vez que o cron HBase não está configurado por padrão, tem de repetir este script quando executa a escala do seu cluster HBase. Se o seu cluster HBase for frequentemente, pode optar por configurar automaticamente o cron da HBase. Por exemplo: `-h "*/30 * * * *"` configura o script para efetuar verificações a cada 30 minutos. Isto executará periodicamente o cron cron da HBase para automatizar o descarregamento de novas informações HBase na conta de armazenamento comum para o nó local.
    
    

## <a name="set-up-communication-manually-optional-if-provided-script-in-above-step-fails"></a>Configurar a comunicação manualmente (Opcional, se o script fornecido em cima do passo falhar)

__NOTA:__ Estes passos precisam de ser atuados sempre que um dos aglomerados passa por uma atividade de escala.

1. Copie o hbase-site.xml do armazenamento local para a raiz do armazenamento padrão do seu cluster Spark.  Edite o comando abaixo para refletir a sua configuração.  Em seguida, desde a sua sessão SSH aberta até ao cluster HBase, insira o comando:

    | Valor de sintaxe | Valor novo|
    |---|---|
    |[Regime URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Modifique para refletir o seu armazenamento.  A sintaxe abaixo é para armazenamento de bolhas com transferência segura ativada.|
    |`SPARK_STORAGE_CONTAINER`|Substitua-o pelo nome do recipiente de armazenamento predefinido utilizado para o cluster Spark.|
    |`SPARK_STORAGE_ACCOUNT`|Substitua-a pelo nome da conta de armazenamento predefinido utilizada para o cluster Spark.|

    ```bash
    hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
    ```

2. Em seguida, saia da sua ligação ssh para o seu cluster HBase.

    ```bash
    exit
    ```


3. Ligue-se ao nó de cabeça do seu cluster Spark utilizando SSH. Edite o comando abaixo substituindo `SPARKCLUSTER` pelo nome do seu cluster Spark e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

4. Introduza o comando abaixo para copiar `hbase-site.xml` do armazenamento predefinido do seu cluster Spark para a pasta de configuração Spark 2 no armazenamento local do cluster:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Executar Spark Shell referindo-se ao Conector Spark HBase

Depois de completar o passo anterior, deverá ser capaz de executar a shell Spark, referindo-se à versão apropriada do Conector Spark HBase. Para encontrar a versão central do conector Spark HBase mais recente para o seu cenário de cluster, consulte o [Repositório do Núcleo SHC](https://repo.hortonworks.com/content/groups/public/com/hortonworks/shc/shc-core/).

Como exemplo, a tabela a seguir lista duas versões e os comandos correspondentes que a equipa HDInsight utiliza atualmente. Pode utilizar as mesmas versões para os seus clusters se as versões HBase e Spark forem as mesmas indicadas na tabela. 


1. Na sessão SSH aberta ao cluster Spark, insira o seguinte comando para iniciar uma concha de faísca:

    |Versão spark| Versão HDI HBase  | Versão SHC    |  Comando  |
    | :-----------:| :----------: | :-----------: |:----------- |
    |      2.1    | HDI 3.6 (HBase 1.1) | 1.1.1-2.1-s_2.11    | `spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/` |
    |      2,4    | HDI 4.0 (HBase 2.0) | 1.1.0.3.1.2.2-1  | `spark-shell --packages com.hortonworks.shc:shc-core:1.1.0.3.1.2.2-1 --repositories http://repo.hortonworks.com/content/groups/public/` |

2. Mantenha esta instância de concha spark aberta e continue a [definir um catálogo e consulta.](#define-a-catalog-and-query) Se não encontrar os frascos que correspondem às suas versões no repositório SHC Core, continue a ler. 

Pode construir os frascos diretamente a partir do ramo [GitHub, conector de faíscas.](https://github.com/hortonworks-spark/shc) Por exemplo, se estiver a correr com Spark 2.3 e HBase 1.1, complete estes passos:

1. Clone o repositório:

    ```bash
    git clone https://github.com/hortonworks-spark/shc
    ```
    
2. Vá para a filial-2.3:

    ```bash
    git checkout branch-2.3
    ```

3. Construir a partir do ramo (cria um ficheiro .jar):

    ```bash
    mvn clean package -DskipTests
    ```
    
3. Executar o seguinte comando (certifique-se de alterar o nome .jar que corresponde ao ficheiro .jar que construiu):

    ```bash
    spark-shell --jars <path to your jar>,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar,/usr/hdp/current/hbase-client/lib/hbase-client.jar,/usr/hdp/current/hbase-client/lib/hbase-common.jar,/usr/hdp/current/hbase-client/lib/hbase-server.jar,/usr/hdp/current/hbase-client/lib/hbase-protocol.jar,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar
    ```
    
4. Mantenha esta instância de concha de faísca aberta e continue para a secção seguinte. 



## <a name="define-a-catalog-and-query"></a>Defina um catálogo e consulta

Neste passo, você define um objeto de catálogo que mapeia o esquema de Apache Spark para Apache HBase.  

1. Na sua Spark Shell aberta, insira as `import` seguintes declarações:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Introduza o comando abaixo para definir um catálogo para a tabela Contactos que criou na Base H:

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

    O código:  

    1. Define um esquema de catálogo para a tabela HBase denominada `Contacts` .  
    1. Identifica a linha como `key` , e mapeia os nomes das colunas usadas em Spark para a família da coluna, nome da coluna e tipo de coluna como usado na Base H.  
    1. Define o rowkey em detalhe como uma coluna nomeada ( `rowkey` ), que tem uma família de colunas `cf` específicas de `rowkey` .  

1. Introduza o comando abaixo para definir um método que forneça um DataFrame em torno da sua `Contacts` tabela na Base H:

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

1. Emita uma consulta SQL contra a `contacts` tabela:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    Deve ver resultados como estes:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Inserir novos dados

1. Para inserir um novo registo de contacto, defina uma `ContactRecord` classe:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. Criar um exemplo `ContactRecord` de e colocá-lo em uma matriz:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Guarde a matriz de novos dados para a Base H:

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

1. Feche a concha de faísca introduzindo o seguinte comando:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Próximos passos

* [Conector Apache Spark HBase](https://github.com/hortonworks-spark/shc)
