---
title: Ligação de MapReduce e o SSH com o Apache Hadoop no HDInsight - Azure
description: Saiba como utilizar o SSH para executar tarefas de MapReduce com o Apache Hadoop no HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 3448a5e89f6930a5bdcb7d0d77b92576e58fc90b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687301"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Utilizar o MapReduce com o Apache Hadoop no HDInsight com SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Aprenda a submeter tarefas de MapReduce de uma ligação de Secure Shell (SSH) para o HDInsight.

> [!NOTE]
> Se já estiver familiarizado com o uso de servidores do Apache Hadoop baseado em Linux, mas estiver familiarizado com o HDInsight, consulte [Dicas do HDInsight baseado em Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Pré-requisitos

* Um cluster do HDInsight baseado em Linux (Hadoop no HDInsight)

  > [!IMPORTANT]
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Um cliente SSH. Para obter mais informações, consulte [utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a id="ssh"></a>Ligar com SSH

Ligar ao cluster através de SSH. Por exemplo, o comando seguinte liga a um cluster com o nome **myhdinsight** como o **sshuser** conta:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Se utilizar uma chave de certificado para autenticação SSH**, poderá ter de especificar a localização da chave privada no seu sistema de cliente, por exemplo:

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Se utilizar uma palavra-passe para autenticação SSH**, tem de fornecer a palavra-passe quando lhe for pedido.

Para obter mais informações sobre como utilizar o SSH com HDInsight, consulte [utilizar o SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Utilizar comandos do Hadoop

1. Assim que estiver ligado ao cluster do HDInsight, utilize o seguinte comando para iniciar uma tarefa de MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Este comando inicia o `wordcount` classe, que está contido no `hadoop-mapreduce-examples.jar` ficheiro. Ele usa o `/example/data/gutenberg/davinci.txt` documento como entrada e saída está armazenado em `/example/data/WordCountOutput`.

    > [!NOTE]
    > Para obter mais informações sobre esta tarefa de MapReduce e os dados de exemplo, consulte [utilizar o MapReduce no Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md).

2. A tarefa emite detalhes à medida que processa e devolve informações semelhantes ao seguinte texto quando a tarefa é concluída:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Quando a tarefa estiver concluída, utilize o seguinte comando para listar os ficheiros de saída:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Este comando apresenta dois ficheiros, `_SUCCESS` e `part-r-00000`. O `part-r-00000` ficheiro contém a saída para esta tarefa.

    > [!NOTE]  
    > Algumas tarefas de MapReduce podem dividir os resultados em vários **parte-r-# # #** ficheiros. Se assim for, utilize o # # # sufixo para indicar a ordem dos ficheiros.

4. Para ver o resultado, utilize o seguinte comando:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Este comando apresenta uma lista das palavras contidas no **wasb://example/data/gutenberg/davinci.txt** ficheiro e o número de vezes que ocorreu cada palavra. O texto seguinte é um exemplo dos dados que estão contidos no ficheiro:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Resumo

Como pode ver, comandos de Hadoop fornecem uma forma fácil de executar tarefas de MapReduce num cluster do HDInsight e, em seguida, ver o resultado da tarefa.

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre tarefas de MapReduce no HDInsight:

* [Utilizar o MapReduce no HDInsight Hadoop](hdinsight-use-mapreduce.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
