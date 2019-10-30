---
title: MapReduce e conexão SSH com Apache Hadoop-Azure HDInsight
description: Saiba como usar o SSH para executar trabalhos MapReduce usando o Apache Hadoop no HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: b4075de1a184896d598c11d09ae2b2bda5e257ed
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044512"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Usar o MapReduce com o Apache Hadoop no HDInsight com SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Saiba como enviar trabalhos MapReduce de uma conexão Secure Shell (SSH) para o HDInsight.

> [!NOTE]
> Se você já estiver familiarizado com o uso de servidores Apache Hadoop baseados em Linux, mas for novo no HDInsight, consulte [dicas do hdinsight baseadas em Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Pré-requisitos

* Um cluster HDInsight baseado em Linux (Hadoop no HDInsight)

* Um cliente SSH. Para obter mais informações, consulte [usar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a id="ssh"></a>Conectar-se com SSH

Conecte-se ao cluster usando SSH. Por exemplo, o comando a seguir se conecta a um cluster chamado **myhdinsight** como a conta **sshuser** :

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Se você usar uma chave de certificado para autenticação SSH**, talvez seja necessário especificar o local da chave privada no sistema cliente, por exemplo:

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Se você usar uma senha para autenticação SSH**, precisará fornecer a senha quando solicitado.

Para obter mais informações sobre como usar SSH com o HDInsight, consulte [usar SSH com hdinsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Usar comandos do Hadoop

1. Depois que você estiver conectado ao cluster HDInsight, use o seguinte comando para iniciar um trabalho MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Esse comando inicia a classe `wordcount`, que está contida no arquivo `hadoop-mapreduce-examples.jar`. Ele usa o documento `/example/data/gutenberg/davinci.txt` como entrada e a saída é armazenada em `/example/data/WordCountOutput`.

    > [!NOTE]
    > Para obter mais informações sobre esse trabalho MapReduce e os dados de exemplo, consulte [usar o MapReduce no Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md).

2. O trabalho emite detalhes conforme ele processa e retorna informações semelhantes ao seguinte texto quando o trabalho é concluído:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Quando o trabalho for concluído, use o seguinte comando para listar os arquivos de saída:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Esse comando exibe dois arquivos, `_SUCCESS` e `part-r-00000`. O arquivo de `part-r-00000` contém a saída para esse trabalho.

    > [!NOTE]  
    > Alguns trabalhos MapReduce podem dividir os resultados em vários arquivos **Part-r-# # # # #** . Nesse caso, use o sufixo # # # # # para indicar a ordem dos arquivos.

4. Para exibir a saída, use o seguinte comando:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Esse comando exibe uma lista das palavras contidas no arquivo **WASB://example/data/Gutenberg/DaVinci.txt** e o número de vezes que cada palavra ocorreu. O texto a seguir é um exemplo dos dados contidos no arquivo:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Resumo

Como você pode ver, os comandos do Hadoop fornecem uma maneira fácil de executar trabalhos MapReduce em um cluster HDInsight e, em seguida, exibir a saída do trabalho.

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre trabalhos MapReduce no HDInsight:

* [Usar o MapReduce no Hadoop do HDInsight](hdinsight-use-mapreduce.md)

Para obter informações sobre outras maneiras que você pode trabalhar com o Hadoop no HDInsight:

* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
