---
title: Desenvolver trabalhos de MapReduce com o HDInsight - Azure de transmissão de Python
description: Saiba como utilizar o Python em tarefas de MapReduce de transmissão em fluxo. Apache Hadoop fornece uma API de transmissão em fluxo para o MapReduce para escrever numa linguagem diferente de Java.
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 4c1d7357dc78ca6c9387fc594f3d49f043008631
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120581"
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>Desenvolver programas MapReduce de transmissão para HDInsight de Python

Saiba como utilizar o Python em operações de MapReduce de transmissão em fluxo. Apache Hadoop fornece uma API de transmissão em fluxo para MapReduce que permite-lhe escrever o mapa e reduzir as funções em linguagens diferentes de Java. Os passos neste documento implementam o mapa e reduzem os componentes em Python.

## <a name="prerequisites"></a>Pré-requisitos

* Um Apache Hadoop baseado em Linux num cluster do HDInsight

  > [!IMPORTANT]
  > Os passos neste documento exigem um cluster do HDInsight que utilize o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Um editor de texto

  > [!IMPORTANT]
  > O editor de texto tem de utilizar LF como o fim da linha. Utilizar um fim da linha de CRLF faz com que erros ao executar a tarefa de MapReduce em clusters do HDInsight baseado em Linux.

* O `ssh` e `scp` comandos, ou [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-3.8.0)

## <a name="word-count"></a>Contagem de palavras

Neste exemplo é que uma contagem de palavras básica implementado num python um mapeador de pontos e reducer. O mapeador de pontos divide as frases em palavras individuais e o reducer contagens produzir a saída e agrega as palavras.

O fluxograma a seguir ilustra o que acontece durante o mapa e reduzir fases.

![Ilustração do processo de mapreduce](./media/apache-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>MapReduce de transmissão em fluxo

Hadoop permite-lhe especificar um ficheiro que contém o mapa e reduzir a lógica que é utilizada por uma tarefa. Os requisitos específicos para o mapa e reduzir a lógica são:

* **Entrada**: O mapa e reduzir componentes devem ler os dados de entrada do STDIN.
* **Saída**: O mapa e reduzir componentes devem gravar dados de saída para STDOUT.
* **Formato de dados**: Os dados consumidos e produzidos tem de ser um par chave/valor, separado por um caractere de tabulação.

Python pode facilmente lidar com esses requisitos ao utilizar o `sys` módulo para ler a partir do STDIN e usando `print` para imprimir para STDOUT. A tarefa restante é simplesmente os dados com um separador de formatação (`\t`) caráter entre a chave e valor.

## <a name="create-the-mapper-and-reducer"></a>Criar o mapeador de pontos e reducer

1. Crie um ficheiro denominado `mapper.py` e utilize o seguinte código como o conteúdo:

   ```python
   #!/usr/bin/env python

   # Use the sys module
   import sys

   # 'file' in this case is STDIN
   def read_input(file):
       # Split each line into words
       for line in file:
           yield line.split()

   def main(separator='\t'):
       # Read the data using read_input
       data = read_input(sys.stdin)
       # Process each word returned from read_input
       for words in data:
           # Process each word
           for word in words:
               # Write to STDOUT
               print '%s%s%d' % (word, separator, 1)

   if __name__ == "__main__":
       main()
   ```

2. Crie um ficheiro denominado **reducer.py** e utilize o seguinte código como o conteúdo:

   ```python
   #!/usr/bin/env python

   # import modules
   from itertools import groupby
   from operator import itemgetter
   import sys

   # 'file' in this case is STDIN
   def read_mapper_output(file, separator='\t'):
       # Go through each line
       for line in file:
           # Strip out the separator character
           yield line.rstrip().split(separator, 1)

   def main(separator='\t'):
       # Read the data using read_mapper_output
       data = read_mapper_output(sys.stdin, separator=separator)
       # Group words and counts into 'group'
       #   Since MapReduce is a distributed process, each word
       #   may have multiple counts. 'group' will have all counts
       #   which can be retrieved using the word as the key.
       for current_word, group in groupby(data, itemgetter(0)):
           try:
               # For each word, pull the count(s) for the word
               #   from 'group' and create a total count
               total_count = sum(int(count) for current_word, count in group)
               # Write to stdout
               print "%s%s%d" % (current_word, separator, total_count)
           except ValueError:
               # Count was not a number, so do nothing
               pass

   if __name__ == "__main__":
       main()
   ```

## <a name="run-using-powershell"></a>Executar com o PowerShell

Para garantir que seus arquivos possuem o direito de linha fins, utilize o seguinte script do PowerShell:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

Utilize o seguinte script do PowerShell para carregar os ficheiros, execute a tarefa e ver o resultado:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>Execute a partir de uma sessão SSH

1. Do seu ambiente de desenvolvimento, no mesmo diretório `mapper.py` e `reducer.py` ficheiros, utilize o seguinte comando:

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    Substitua `username` com o nome de utilizador SSH para o seu cluster, e `clustername` com o nome do cluster.

    Este comando copia os ficheiros do sistema local para o nó principal.

    > [!NOTE]  
    > Se utilizou uma palavra-passe para proteger a sua conta do SSH, lhe for pedida a palavra-passe. Se utilizou uma chave SSH, poderá ter de utilizar o `-i` parâmetro e o caminho para a chave privada. Por exemplo, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

2. Ligar ao cluster através de SSH:

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    Para obter mais informações sobre, consulte [utilizar o SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Para verificar se o mapper.py e reducer.py tem fins a linha correta, utilize os seguintes comandos:

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. Utilize o seguinte comando para iniciar a tarefa de MapReduce.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    Este comando tem as seguintes partes:

   * **hadoop-streaming.jar**: Utilizado quando executar operações de MapReduce de transmissão em fluxo. Ela faz interface Hadoop com o código externo do MapReduce que fornecer.

   * **-files**: Adiciona os ficheiros especificados para a tarefa de MapReduce.

   * **-mapper**: Informa qual arquivo a ser usado como o mapeador de pontos de Hadoop.

   * **-reducer**: Informa qual arquivo a ser usado como o reducer ao Hadoop.

   * **-entrada**: O ficheiro de entrada que deve Contamos palavras de.

   * **-output**: O diretório que o resultado é escrito.

     Como funciona a tarefa de MapReduce, o processo é apresentado como percentagens.

        15/02/05 19:01:04 mapreduce de informações. Tarefa: mapa 0% reduzir 0% 15/02/05 19:01:16 mapreduce de informações. Tarefa: mapa 100% reduzir 0% 15/02/05 19:01:27 mapreduce de informações. Tarefa: mapa 100% reduzir 100%


5. Para ver o resultado, utilize o seguinte comando:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Este comando mostra uma lista de palavras e o número de vezes que a palavra ocorreu.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como utilizar tarefas de MapRedcue transmissão em fluxo com o HDInsight, utilize as seguintes ligações para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Utilizar o Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o HDInsight](hdinsight-use-pig.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
