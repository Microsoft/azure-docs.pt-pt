---
title: Use uma concha de faísca interativa em Azure HDInsight
description: Uma Spark Shell interativa fornece um processo de impressão de execução de leitura para executar o Spark comanda um de cada vez e vê os resultados.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/10/2020
ms.openlocfilehash: 324852a967b5de015a9b1e9b465d4b4703e573cb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929678"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Executar Apache Spark da Concha de Faísca

Um [Apache Spark](https://spark.apache.org/) Shell interativo fornece um ambiente REPL (leia-executar-print loop) para executar comandos Spark um de cada vez e ver os resultados. Este processo é útil para o desenvolvimento e depuração. A faísca fornece uma concha para cada uma das suas línguas apoiadas: Scala, Python e R.

## <a name="run-an-apache-spark-shell"></a>Executar uma concha de faísca Apache

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A faísca fornece conchas para Scala (spark-shell) e Python (pyspark). Na sua sessão de SSH, insira *um* dos seguintes comandos:

    ```bash
    spark-shell

    # Optional configurations
    # spark-shell --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    ```bash
    pyspark

    # Optional configurations
    # pyspark --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    Se pretender utilizar qualquer configuração opcional, certifique-se de que revê primeiro [a exceção OutOfMemoryError para Apache Spark](./apache-spark-troubleshoot-outofmemory.md).

1. Alguns comandos básicos de exemplo. Escolha a língua relevante:

    ```spark-shell
    val textFile = spark.read.textFile("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(line => line.contains("apple")).show()
    ```

    ```pyspark
    textFile = spark.read.text("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(textFile.value.contains("apple")).show()
    ```

1. Consultar um ficheiro CSV. Note que o idioma abaixo funciona para `spark-shell` e `pyspark` .

    ```scala
    spark.read.csv("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv").show()
    ```

1. Consulta de um ficheiro CSV e armazena resultados variáveis:

    ```spark-shell
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

    ```pyspark
    data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

1. Resultados do visor:

    ```spark-shell
    data.show()
    data.select($"BuildingID", $"Country").show(10)
    ```

    ```pyspark
    data.show()
    data.select("BuildingID", "Country").show(10)
    ```

1. Sair

    ```spark-shell
    :q
    ```

    ```pyspark
    exit()
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>Exemplos sparkSession e SparkContext

Por padrão, quando executar a Spark Shell, as instâncias de SparkSession e SparkContext são automaticamente instantâneas para si.

Para aceder à instância SparkSession, insira `spark` . Para aceder à instância SparkContext, insira `sc` .

## <a name="important-shell-parameters"></a>Parâmetros importantes da concha

O comando Spark Shell `spark-shell` (, ou `pyspark` ) suporta muitos parâmetros de linha de comando. Para ver uma lista completa de parâmetros, inicie a Spark Shell com o interruptor `--help` . Alguns destes parâmetros só podem aplicar-se a `spark-submit` , que a Concha de Faísca envolve.

| trocar | descrição | exemplo |
| --- | --- | --- |
| -mestre MASTER_URL | Especifica a URL principal. Em HDInsight, este valor é sempre `yarn` . | `--master yarn`|
| -jarros JAR_LIST | Lista separada por vírgula de frascos locais para incluir nos caminhos de classe do condutor e executor. No HDInsight, esta lista é composta por caminhos para o sistema de ficheiros predefinido no Armazenamento Azure ou no Armazenamento do Lago de Dados. | `--jars /path/to/examples.jar` |
| ...pacotes MAVEN_COORDS | Lista separada por vírgula de coordenadas de frascos para incluir nos caminhos de classe do condutor e do executor. Procure o repo maven local, em seguida maven central, em seguida, quaisquer repositórios remotos adicionais especificados com `--repositories` . O formato das coordenadas é *groupId*:*artifactId*:*versão*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LIST | Apenas para Python, uma lista separada por vírgula de .zip, .egg, ou .py ficheiros para colocar no PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Próximos passos

- Consulte [a Introdução ao Apache Spark em Azure HDInsight](apache-spark-overview.md) para obter uma visão geral.
- Consulte [Criar um cluster Apache Spark em Azure HDInsight](apache-spark-jupyter-spark-sql.md) para trabalhar com clusters Spark e SparkSQL.
- Veja [o que é o Apache Spark Structured Streaming?](apache-spark-streaming-overview.md)
