---
title: Operações Apache Spark apoiadas por Hive Warehouse Connector em Azure HDInsight
description: Conheça as diferentes capacidades do Conector hive warehouse no Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 999c58871ed811d91fd96d158ea6f65db6c718f3
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853842"
---
# <a name="apache-spark-operations-supported-by-hive-warehouse-connector-in-azure-hdinsight"></a>Operações Apache Spark apoiadas por Hive Warehouse Connector em Azure HDInsight

Este artigo mostra operações baseadas em faíscas apoiadas pelo Hive Warehouse Connector (HWC). Todos os exemplos abaixo mostrados serão executados através da concha apache Spark.

## <a name="prerequisite"></a>Pré-requisito

Complete os passos de configuração do [Conector do Armazém hive.](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup)

## <a name="getting-started"></a>Introdução

Para iniciar uma sessão de spark-shell, faça os seguintes passos:

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu aglomerado de Faíscas Apache. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A partir da sua sessão ssh, execute o seguinte comando para observar a `hive-warehouse-connector-assembly` versão:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Editar o código abaixo com a `hive-warehouse-connector-assembly` versão acima identificada. Em seguida, execute o comando para iniciar a casca de faísca:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Depois de iniciar a casca de faísca, uma instância de Conector de Armazém hive pode ser iniciada usando os seguintes comandos:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

## <a name="creating-spark-dataframes-using-hive-queries"></a>Criação de DataFrames de Faísca suster consultas da Hive

Os resultados de todas as consultas que utilizam a biblioteca HWC são devolvidos como DataFrame. Os seguintes exemplos demonstram como criar uma consulta básica de colmeia.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Os resultados da consulta são Spark DataFrames, que podem ser usados com bibliotecas spark como MLIB e SparkSQL.

## <a name="writing-out-spark-dataframes-to-hive-tables"></a>Escrever Spark DataFrames para as mesas da Colmeia

A Faísca não apoia a escrita nas mesas acid geridas da Hive. No entanto, utilizando o HWC, pode escrever qualquer DataFrame para uma tabela DaHiv. Pode ver esta funcionalidade a trabalhar no seguinte exemplo:

1. Crie uma tabela chamada `sampletable_colorado` e especifique as suas colunas utilizando o seguinte comando:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtrar a tabela `hivesampletable` onde a coluna é igual `state` `Colorado` . Esta consulta de colmeia devolve uma sis Spark DataFrame e sis guardada na tabela Da Colmeia `sampletable_colorado` utilizando a `write` função.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table","sampletable_colorado").save()
    ```

1. Veja os resultados com o seguinte comando:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    ![conector de armazém de colmeia mostrar mesa de colmeia](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)


## <a name="structured-streaming-writes"></a>Streaming estruturado escreve

Utilizando o Conector de Armazém Hive, pode utilizar o streaming Spark para escrever dados em tabelas da Hive.

> [!IMPORTANT]
> Os escritos de streaming estruturados não são suportados em clusters ESP ativados spark 4.0.

Siga os passos abaixo para ingerir dados de um fluxo de faíscas no porto local 9999 para uma tabela Hive via. Conector de Armazém hive.

1. A partir da sua concha de faísca aberta, inicie uma corrente de faísca com o seguinte comando:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Gere dados para o fluxo Spark que criou, fazendo os seguintes passos:
    1. Abra uma segunda sessão de SSH no mesmo cluster Spark.
    1. No pedido de comando, escreva `nc -lk 9999` . Este comando utiliza o utilitário netcat para enviar dados da linha de comando para a porta especificada.

1. Volte à primeira sessão de SSH e crie uma nova tabela Hive para segurar os dados de streaming. Na casca de faísca, insira o seguinte comando:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Em seguida, escreva os dados de streaming para a tabela recém-criada usando o seguinte comando:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource").option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > As `metastoreUri` `database` opções e opções devem ser definidas manualmente devido a um problema conhecido em Apache Spark. Para mais informações sobre este assunto, consulte [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Volte à segunda sessão de SSH e introduza os seguintes valores:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Volte à primeira sessão de SSH e note a breve atividade. Utilize o seguinte comando para visualizar os dados:

    ```scala
    hive.table("stream_table").show()
    ```

Utilize **ctrl + C** para parar o netcat na segunda sessão SSH. Utilize `:q` para sair de uma casca de faísca na primeira sessão sHH.

## <a name="next-steps"></a>Passos seguintes

* [Integração de HWC com Apache Spark e Apache Hive](./apache-hive-warehouse-connector.md)
* [Utilizar uma Consulta Interativa com o HDInsight](./apache-interactive-query-get-started.md).
* [Integração de HWC com Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)