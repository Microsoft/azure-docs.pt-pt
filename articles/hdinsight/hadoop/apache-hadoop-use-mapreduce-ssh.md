---
title: Ligação MapReduce e SSH com Apache Hadoop - Azure HDInsight
description: Aprenda a usar o SSH para executar mapReduce trabalhos usando Apache Hadoop no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 543bc29adc85bd767de9479607d067fadf7b0078
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934701"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Use mapReduce com Hadoop Apache em HDInsight com SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Saiba como submeter mapReduce jobs a partir de uma ligação Secure Shell (SSH) ao HDInsight.

> [!NOTE]
> Se já está familiarizado com a utilização de servidores Apache Hadoop baseados em Linux, mas é novo no HDInsight, consulte [as dicas HDInsight baseadas em Linux](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Pré-requisitos

Um aglomerado Apache Hadoop no HDInsight. Consulte os [clusters De Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Usar comandos Hadoop

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu cluster. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Depois de estar ligado ao cluster HDInsight, utilize o seguinte comando para iniciar um trabalho MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Este comando `wordcount` inicia a classe, que `hadoop-mapreduce-examples.jar` está contida no ficheiro. Utiliza o `/example/data/gutenberg/davinci.txt` documento como entrada e a `/example/data/WordCountOutput`saída é armazenada em .

    > [!NOTE]
    > Para obter mais informações sobre este trabalho MapReduce e os dados de exemplo, consulte [Use MapReduce in Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md).

    O trabalho emite detalhes à medida que processa, e devolve informações semelhantes ao seguinte texto quando o trabalho termina:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. Quando o trabalho estiver concluído, utilize o seguinte comando para listar os ficheiros de saída:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Este comando exibe `_SUCCESS` dois `part-r-00000`ficheiros e . O `part-r-00000` ficheiro contém a saída para este trabalho.

    > [!NOTE]  
    > Alguns trabalhos MapReduce podem dividir os resultados em vários ficheiros **part-r-#######** Em caso afirmativo, utilize o sufixo ##### para indicar a ordem dos ficheiros.

1. Para visualizar a saída, utilize o seguinte comando:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Este comando apresenta uma lista das palavras contidas no ficheiro **wasbs://example/data/gutenberg/davinci.txt** e o número de vezes que cada palavra ocorreu. O seguinte texto é um exemplo dos dados que estão contidos no ficheiro:

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

Como pode ver, os comandos Hadoop fornecem uma maneira fácil de executar mapReduce empregos em um cluster HDInsight e, em seguida, ver a saída de trabalho. Para obter informações sobre outras formas de trabalhar com Hadoop no HDInsight:

* [Utilizar mapeiaReduzir no Hadoop HDInsight](hdinsight-use-mapreduce.md)
* [Use a Colmeia Apache com Hadoop Apache no HDInsight](hdinsight-use-hive.md)
