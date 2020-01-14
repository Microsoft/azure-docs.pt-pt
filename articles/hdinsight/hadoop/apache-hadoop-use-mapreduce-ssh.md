---
title: MapReduce e conexão SSH com Apache Hadoop-Azure HDInsight
description: Saiba como usar o SSH para executar trabalhos MapReduce usando o Apache Hadoop no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 543bc29adc85bd767de9479607d067fadf7b0078
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934701"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Usar o MapReduce com o Apache Hadoop no HDInsight com SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Saiba como enviar trabalhos MapReduce de uma conexão Secure Shell (SSH) para o HDInsight.

> [!NOTE]
> Se você já estiver familiarizado com o uso de servidores Apache Hadoop baseados em Linux, mas for novo no HDInsight, consulte [dicas do hdinsight baseadas em Linux](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Pré-requisitos

Um cluster Apache Hadoop no HDInsight. Consulte [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Usar comandos do Hadoop

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando a seguir substituindo CLUSTERname pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Depois que você estiver conectado ao cluster HDInsight, use o seguinte comando para iniciar um trabalho MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Esse comando inicia a classe `wordcount`, que está contida no arquivo `hadoop-mapreduce-examples.jar`. Ele usa o documento `/example/data/gutenberg/davinci.txt` como entrada e a saída é armazenada em `/example/data/WordCountOutput`.

    > [!NOTE]
    > Para obter mais informações sobre esse trabalho MapReduce e os dados de exemplo, consulte [usar o MapReduce no Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md).

    O trabalho emite detalhes conforme ele processa e retorna informações semelhantes ao seguinte texto quando o trabalho é concluído:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. Quando o trabalho for concluído, use o seguinte comando para listar os arquivos de saída:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Esse comando exibe dois arquivos, `_SUCCESS` e `part-r-00000`. O arquivo de `part-r-00000` contém a saída para esse trabalho.

    > [!NOTE]  
    > Alguns trabalhos MapReduce podem dividir os resultados em vários arquivos **Part-r-# # # # #** . Nesse caso, use o sufixo # # # # # para indicar a ordem dos arquivos.

1. Para exibir a saída, use o seguinte comando:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Esse comando exibe uma lista das palavras contidas no arquivo **wasbs://example/data/Gutenberg/DaVinci.txt** e o número de vezes que cada palavra ocorreu. O texto a seguir é um exemplo dos dados contidos no arquivo:

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>Passos seguintes

Como você pode ver, os comandos do Hadoop fornecem uma maneira fácil de executar trabalhos MapReduce em um cluster HDInsight e, em seguida, exibir a saída do trabalho. Para obter informações sobre outras maneiras que você pode trabalhar com o Hadoop no HDInsight:

* [Usar o MapReduce no Hadoop do HDInsight](hdinsight-use-mapreduce.md)
* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)
