---
title: Ligação MapReduce e SSH com Apache Hadoop - Azure HDInsight
description: Aprenda a usar o SSH para executar trabalhos mapReduce usando Apache Hadoop em HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 2736d0cfe514252e36ba6d7e0d71efe09da15aae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86076305"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Use MapReduce com Apache Hadoop em HDInsight com SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Saiba como submeter os trabalhos do MapReduce a partir de uma ligação Secure Shell (SSH) ao HDInsight.

> [!NOTE]
> Se já está familiarizado com a utilização de servidores Apache Hadoop baseados em Linux, mas é novo em HDInsight, consulte [as dicas HDInsight baseadas em Linux](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Pré-requisitos

Um aglomerado Apache Hadoop em HDInsight. Consulte [os clusters Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Use comandos Hadoop

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Depois de estar ligado ao cluster HDInsight, utilize o seguinte comando para iniciar uma tarefa MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Este comando inicia a `wordcount` classe, que está contida no `hadoop-mapreduce-examples.jar` ficheiro. Utiliza o `/example/data/gutenberg/davinci.txt` documento como entrada e a saída é armazenada em `/example/data/WordCountOutput` .

    > [!NOTE]
    > Para obter mais informações sobre este trabalho mapreduce e os dados de exemplo, consulte [Use MapReduce in Apache Hadoop em HDInsight](hdinsight-use-mapreduce.md).

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

    Este comando mostra dois ficheiros `_SUCCESS` e `part-r-00000` . O `part-r-00000` ficheiro contém a saída para este trabalho.

    > [!NOTE]  
    > Alguns trabalhos do MapReduce podem dividir os resultados em vários ficheiros **part-r-####** Em caso afirmativo, utilize o sufixo #### para indicar a ordem dos ficheiros.

1. Para visualizar a saída, utilize o seguinte comando:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Este comando apresenta uma lista das palavras contidas no ficheiro **wasbs://example/data/gutenberg/davinci.txt** e o número de vezes que cada palavra ocorreu. O texto a seguir é um exemplo dos dados contidos no ficheiro:

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

Como pode ver, os comandos Hadoop fornecem uma maneira fácil de executar trabalhos MapReduce num cluster HDInsight e, em seguida, ver a saída do trabalho. Para obter informações sobre outras formas de trabalhar com Hadoop em HDInsight:

* [Use MapReduce em HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Use a Colmeia Apache com Apache Hadoop em HDInsight](hdinsight-use-hive.md)
