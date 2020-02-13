---
title: Use uma concha de faísca interativa em Azure HDInsight
description: Uma Spark Shell interativa fornece um processo de impressão de leitura-execução para executar comandos Spark um de cada vez e ver os resultados.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/10/2020
ms.openlocfilehash: f8737f645df2aefbf9ce544199f0cc45ce6a3d60
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162808"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Executar Faísca Apache da Faísca

Um [Apache Spark](https://spark.apache.org/) Shell interativo fornece um ambiente REPL (ciclo de impressão de leitura-execução) para executar os comandos Spark um de cada vez e ver os resultados. Este processo é útil para o desenvolvimento e depuração. A faísca fornece uma concha para cada uma das suas línguas apoiadas: Scala, Python e R.

## <a name="run-an-apache-spark-shell"></a>Executar uma membrana de faísca Apache

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu cluster. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A faísca fornece conchas para Scala (casca de faísca) e Python (pyspark). Na sua sessão de SSH, insira *um* dos seguintes comandos:

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

1. Consultar um ficheiro CSV. Note que a língua abaixo funciona para `spark-shell` e `pyspark`.

    ```scala
    spark.read.csv("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv").show()
    ```

1. Consulta de um ficheiro CSV e a loja resulta em variável:

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

## <a name="sparksession-and-sparkcontext-instances"></a>Casos SparkSession e SparkContext

Por predefinição, quando executa a Spark Shell, as instâncias de SparkSession e SparkContext são automaticamente instantâneas para si.

Para aceder à instância SparkSession, insira `spark`. Para aceder à instância SparkContext, introduza `sc`.

## <a name="important-shell-parameters"></a>Parâmetros importantes da concha

O comando Spark Shell (`spark-shell`ou `pyspark`) suporta muitos parâmetros da linha de comando. Para ver uma lista completa de parâmetros, inicie a Membrana de Faísca com o interruptor `--help`. Alguns destes parâmetros só podem aplicar-se a `spark-submit`, que a Spark Shell envolve.

| mudar | descrição | Exemplo |
| --- | --- | --- |
| --mestre MASTER_URL | Especifica o URL principal. No HDInsight, este valor é sempre `yarn`. | `--master yarn`|
| --jars JAR_LIST | Lista separada da vírvia de frascos locais para incluir no condutor e executor de classes. No HDInsight, esta lista é composta por caminhos para o sistema de ficheiros predefinido no Armazenamento de Azure ou armazenamento de data lake. | `--jars /path/to/examples.jar` |
| -pacotes MAVEN_COORDS | Lista separada da vírcula de coordenadas maven de frascos para incluir no condutor e executor classpaths. Procura o repo maven local, depois maven central, em seguida, quaisquer repositórios remotos adicionais especificados com `--repositories`. O formato para as coordenadas é *o grupoId*:*artifactId*:*versão*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LIST | Apenas para Python, uma lista separada da vírvia de .zip, .egg ou .py files para colocar no PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Passos seguintes

- Consulte [a introdução à Faísca Apache no Azure HDInsight](apache-spark-overview.md) para uma visão geral.
- Consulte [Criar um cluster Apache Spark em Azure HDInsight](apache-spark-jupyter-spark-sql.md) para trabalhar com clusters Spark e SparkSQL.
- Veja [o que é Apache Spark Structured Streaming?](apache-spark-streaming-overview.md)
