---
title: Usar um shell do Spark interativo no Azure HDInsight
description: Um shell do Spark interativo fornece um processo de leitura-execução-impressão para executar comandos do Spark um de cada vez e ver os resultados.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/12/2019
ms.openlocfilehash: f088b8210b8170d22e84d131f0a72f5f8caa3b92
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435214"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Executar Apache Spark do shell do Spark

Um shell [Apache Spark](https://spark.apache.org/) interativo fornece um ambiente repl (Read-execute-Print loop) para executar comandos do Spark um de cada vez e ver os resultados. Esse processo é útil para desenvolvimento e depuração. O Spark fornece um shell para cada um de seus idiomas com suporte: escala, Python e R.

## <a name="run-an-apache-spark-shell"></a>Executar um Apache Spark Shell

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando a seguir substituindo CLUSTERname pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. O Spark fornece shells para escala (Spark-Shell) e Python (pyspark). Em sua sessão SSH, insira um dos seguintes comandos:

    ```bash
    spark-shell
    pyspark
    ```

    Agora você pode inserir comandos do Spark no idioma apropriado.

1. Alguns comandos de exemplo básicos:

    ```scala
    // Load data
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")

    // Show data
    data.show()

    // Select certain columns
    data.select($"BuildingID", $"Country").show(10)

    // exit shell
    :q
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>Instâncias de SparkSession e SparkContext

Por padrão, quando você executa o Shell do Spark, as instâncias de SparkSession e SparkContext são instanciadas automaticamente para você.

Para acessar a instância do SparkSession, digite `spark`. Para acessar a instância do SparkContext, digite `sc`.

## <a name="important-shell-parameters"></a>Parâmetros de shell importantes

O comando do shell do Spark (`spark-shell`ou `pyspark`) dá suporte a muitos parâmetros de linha de comando. Para ver uma lista completa de parâmetros, inicie o Shell do Spark com a opção `--help`. Alguns desses parâmetros só podem ser aplicados a `spark-submit`, que o Shell do Spark encapsula.

| trocar | descrição | Exemplo |
| --- | --- | --- |
| --Mestre MASTER_URL | Especifica a URL mestra. No HDInsight, esse valor é sempre `yarn`. | `--master yarn`|
| --jars JAR_LIST | Lista separada por vírgulas de JARs locais para incluir nos classpaths do driver e do executor. No HDInsight, essa lista é composta de caminhos para o sistema de arquivos padrão no armazenamento do Azure ou Data Lake Storage. | `--jars /path/to/examples.jar` |
| --pacotes MAVEN_COORDS | Lista separada por vírgulas de coordenadas do Maven de jars para incluir nos classpaths do driver e do executor. Pesquisa o repositório Maven local e, em seguida, o Maven central, em seguida, todos os repositórios remotos adicionais especificados com `--repositories`. O formato das coordenadas é *GroupId*:*artefatoid*:*versão*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-lista de arquivos | Somente para Python, uma lista separada por vírgulas de arquivos. zip,. ovo ou. py a serem colocados no PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Passos seguintes

- Consulte [introdução ao Apache Spark no Azure HDInsight](apache-spark-overview.md) para obter uma visão geral.
- Consulte [criar um cluster apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md) para trabalhar com clusters Spark e SparkSQL.
- Veja [o que é Apache Spark o streaming estruturado?](apache-spark-streaming-overview.md) para escrever aplicativos que processam dados de streaming com o Spark.
