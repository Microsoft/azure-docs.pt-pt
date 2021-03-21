---
title: Operações apache spark apoiadas pelo Conector do Armazém da Colmeia em Azure HDInsight
description: Conheça as diferentes capacidades do Conector do Armazém da Colmeia no Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: 20567a1e38686b5d452a5353bc459e7e1125f499
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98941307"
---
# <a name="apache-spark-operations-supported-by-hive-warehouse-connector-in-azure-hdinsight"></a>Operações apache spark apoiadas pelo Conector do Armazém da Colmeia em Azure HDInsight

Este artigo mostra operações baseadas em faíscas apoiadas pelo Conector do Armazém da Colmeia (HWC). Todos os exemplos apresentados abaixo serão executados através da concha de Faísca Apache.

## <a name="prerequisite"></a>Pré-requisito

Complete os passos de [configuração do Conector do Armazém da Colmeia.](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup)

## <a name="getting-started"></a>Introdução

Para iniciar uma sessão de conchas de faísca, faça os seguintes passos:

1. Use [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu aglomerado de faíscas Apache. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A partir da sua sessão de sessão, execute o seguinte comando para notar a `hive-warehouse-connector-assembly` versão:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Edite o código abaixo com a `hive-warehouse-connector-assembly` versão acima identificada. Em seguida, execute o comando para iniciar a concha de faísca:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Depois de iniciar a camada de faísca, uma instância do Conector do Armazém da Colmeia pode ser iniciada utilizando os seguintes comandos:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

## <a name="creating-spark-dataframes-using-hive-queries"></a>Criação de DataFrames de Faíscas usando consultas de Hive

Os resultados de todas as consultas que utilizam a biblioteca HWC são devolvidos como DataFrame. Os exemplos a seguir demonstram como criar uma consulta básica da colmeia.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Os resultados da consulta são Spark DataFrames, que podem ser usados com bibliotecas Spark como MLIB e SparkSQL.

## <a name="writing-out-spark-dataframes-to-hive-tables"></a>Escrevendo DataFrames de Faísca para tabelas de colmeias

Spark não apoia a escrita para as tabelas de ÁCIDO geridas da Hive. No entanto, usando o HWC, pode escrever qualquer DataFrame para uma tabela de Colmeia. Pode ver esta funcionalidade no trabalho no seguinte exemplo:

1. Criar uma tabela chamada `sampletable_colorado` e especificar as suas colunas utilizando o seguinte comando:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtrar a tabela `hivesampletable` onde a coluna é igual `state` `Colorado` . Esta consulta de colmeia devolve uma sis de Spark DataFrame guardada na tabela Hive `sampletable_colorado` utilizando a `write` função.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table","sampletable_colorado").save()
    ```

1. Ver os resultados com o seguinte comando:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    ![conector de armazém de colmeia mostra tabela de colmeia](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)


## <a name="structured-streaming-writes"></a>Escreve streaming estruturado

Utilizando o Conector Hive Warehouse, pode utilizar o streaming spark para escrever dados em tabelas de Colmeia.

> [!IMPORTANT]
> As escritas de streaming estruturadas não são suportadas em clusters de faíscas ativados por ESP 4.0.

Siga os passos abaixo para ingerir dados de uma corrente de faísca na porta local 9999 em uma tabela de Colmeia via. Conector do Armazém da Colmeia.

1. A partir da sua concha de faísca aberta, inicie uma corrente de faísca com o seguinte comando:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Gerei dados para o fluxo spark que criou, fazendo os seguintes passos:
    1. Abra uma segunda sessão de SSH no mesmo cluster Spark.
    1. Na solicitação de comando, escreva `nc -lk 9999` . Este comando utiliza o utilitário netcat para enviar dados da linha de comando para a porta especificada.

1. Volte à primeira sessão de SSH e crie uma nova tabela De Colmeia para conter os dados de streaming. Na casca de faísca, insira o seguinte comando:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Em seguida, escreva os dados de streaming para a tabela recém-criada utilizando o seguinte comando:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource").option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > As `metastoreUri` `database` opções devem ser definidas manualmente devido a um problema conhecido em Apache Spark. Para mais informações sobre este assunto, consulte [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Volte à segunda sessão de SSH e insira os seguintes valores:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Volte à primeira sessão de SSH e tome nota da breve atividade. Utilize o seguinte comando para visualizar os dados:

    ```scala
    hive.table("stream_table").show()
    ```

Utilize **ctrl + C** para parar o netcat na segunda sessão de SSH. Use `:q` para sair da casca de faísca na primeira sessão de SSH.

## <a name="next-steps"></a>Passos seguintes

* [Integração do HWC no Apache Spark e no Apache Hive](./apache-hive-warehouse-connector.md)
* [Utilizar uma Consulta Interativa com o HDInsight](./apache-interactive-query-get-started.md).
* [Integração do HWC no Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)
