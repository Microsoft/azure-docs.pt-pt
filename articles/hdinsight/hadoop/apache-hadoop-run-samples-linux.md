---
title: Executar exemplos do Apache Hadoop MapReduce no HDInsight - Azure
description: Começar a utilizar os exemplos de MapReduce nos ficheiros jar incluídos no HDInsight. Utilizar o SSH para ligar ao cluster e, em seguida, utilize o comando de Hadoop para executar tarefas de exemplo.
keywords: jar de exemplo do hadoop, hadoop exemplos jar, exemplos de mapreduce do hadoop, exemplos de mapreduce
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 7ff91a4fdc4bf0118c231a1ac18ebb521530a980
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447020"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Executar os exemplos de MapReduce incluídos no HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Saiba como executar os exemplos de MapReduce incluídos com o Apache Hadoop no HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster do HDInsight**: Consulte [começar a utilizar o Apache Hadoop com o Apache Hive no HDInsight no Linux](apache-hadoop-linux-tutorial-get-started.md)

    > [!IMPORTANT]  
    > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* **Um cliente SSH**: Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Os exemplos de MapReduce

**Localização**: Os exemplos estão localizados no cluster do HDInsight em `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`.

**Conteúdo**: Os exemplos seguintes estão contidos deste arquivo:

* `aggregatewordcount`: Um agregado com base em programa mapreduce que conte as palavras nos ficheiros de entrada.
* `aggregatewordhist`: Um agregado com base em programa mapreduce que computa o histograma das palavras nos ficheiros de entrada.
* `bbp`: Um programa mapreduce que utiliza Bailey-Borwein-Plouffe para dígitos exatos do instalador de plataforma de computação.
* `dbcount`: Uma tarefa de exemplo que conte os registos de visualização de página armazenados numa base de dados.
* `distbbp`: Um programa mapreduce que utiliza uma fórmula de tipo BBP para bits exatas de instalador de plataforma de computação.
* `grep`: Um programa mapreduce que conte as correspondências de um regex na entrada.
* `join`: Uma tarefa que executa uma junção em conjuntos de dados classificados, igualmente particionados.
* `multifilewc`: Uma tarefa que conte de palavras de vários arquivos.
* `pentomino`: Programa de apresentação de mensagens em fila para encontrar soluções para problemas de pentomino do mosaico de um mapreduce.
* `pi`: Um programa mapreduce que as estimativas de Pi usando um quasi-Monte de método Carlo.
* `randomtextwriter`: Um programa mapreduce que escreve 10 GB de dados textuais aleatórios por nó.
* `randomwriter`: Um programa mapreduce que escreve 10 GB de dados aleatórios por nó.
* `secondarysort`: Um exemplo de definir um tipo secundário para a fase de redução.
* `sort`: Um programa mapreduce que classifica os dados escritos pelo escritor do aleatório.
* `sudoku`: Um solucionador do sudoku.
* `teragen`: Gere dados para o terasort.
* `terasort`: Execute o terasort.
* `teravalidate`: A verificar os resultados de terasort.
* `wordcount`: Um programa mapreduce que conte as palavras nos ficheiros de entrada.
* `wordmean`: Um programa mapreduce que conte a duração média das palavras nos ficheiros de entrada.
* `wordmedian`: Um programa mapreduce que conte o comprimento mediano das palavras nos ficheiros de entrada.
* `wordstandarddeviation`: Um programa mapreduce que conte o desvio-padrão de comprimento de palavras nos ficheiros de entrada.

**O código-fonte**: Estes exemplos de código-fonte está incluído no cluster do HDInsight em `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

## <a name="run-the-wordcount-example"></a>Executar o exemplo de wordcount

1. Ligar ao HDInsight através de SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Do `username@#######:~$` pedem, utilize o seguinte comando para listar os exemplos:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Este comando cria a lista de exemplo da seção anterior deste documento.

3. Utilize o seguinte comando para obter ajuda sobre um exemplo específico. Neste caso, o **wordcount** exemplo:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Recebe a seguinte mensagem:

        Usage: wordcount <in> [<in>...] <out>

    Esta mensagem indica que pode fornecer vários caminhos de entrada para os documentos de origem. O caminho final é onde está armazenada a saída (contagem de palavras nos documentos de origem).

4. Utilize o seguinte para a contagem de todas as palavras nos blocos de anotações de Leonardo Da Vinci, que são fornecidos como dados de exemplo com o seu cluster:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Para esta tarefa é lida a partir de entrada `/example/data/gutenberg/davinci.txt`. Saída para este exemplo é armazenado no `/example/data/davinciwordcount`. Os dois caminhos estão localizados no armazenamento de predefinido para o cluster, não o sistema de arquivos local.

   > [!NOTE]  
   > Conforme observado na ajuda para o exemplo de wordcount, também pode especificar vários ficheiros de entrada. Por exemplo, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` contabilizaria palavras em davinci.txt e ulysses.txt.

5. Depois de concluída a tarefa, utilize o seguinte comando para ver o resultado:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Este comando concatena todos os ficheiros de saída produzidos pela tarefa. Ele apresenta o resultado no Console. O resultado é semelhante ao seguinte texto:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Cada linha representa uma palavra e o número de vezes que ocorreu nos dados de entrada.

## <a name="the-sudoku-example"></a>O exemplo do Sudoku

[O Sudoku](https://en.wikipedia.org/wiki/Sudoku) é um quebra-cabeça de lógica composto de nove 3x3 grades. Algumas células da grade têm números, enquanto outros estão em branco e o objetivo é solucionar para as células em branco. A ligação anterior tem mais informações sobre o quebra-cabeça, mas o objetivo deste exemplo é resolver para as células em branco. Portanto, nossa entrada deve ser um ficheiro que está no seguinte formato:

* Nove linhas de colunas de nove
* Cada coluna pode conter um número ou `?` (que indica uma célula em branco)
* Células são separadas por um espaço

Existe uma determinada maneira para construir o quebra-cabeças Sudoku; Não é possível repetir um número numa coluna ou linha. Há um exemplo no cluster do HDInsight que foi construído corretamente. Está localizado em `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` e contém o seguinte texto:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Para executar esse problema de exemplo através do exemplo do Sudoku, utilize o seguinte comando:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Os resultados são apresentados semelhantes ao seguinte texto:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Exemplo de PI (π)

O exemplo de instalador de plataforma usa uma estatística (quasi-Monte Carlo) método para calcular o valor de pi. Pontos são colocados aleatoriamente num quadrado de unidade. O quadrado contém também um círculo. A probabilidade de que os pontos se inserem num círculo são iguais para a área do círculo, pi/4. O valor de pi pode ser estimado do valor da 4R. R é a proporção entre o número de pontos que estão dentro do círculo para o número total de pontos que estejam no quadrado. Quanto maior for o exemplo de pontos de utilizados, melhor será a estimativa é.

Utilize o seguinte comando para executar este exemplo. Este comando utiliza o 16 mapas com 10.000.000 exemplos para estimar o valor de pi:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

O valor devolvido por este comando é semelhante à **3.14159155000000000000**. Para referências, as primeiras 10 casas decimais de pi são 3.1415926535.

## <a name="10-gb-greysort-example"></a>Exemplo de Greysort de 10 GB

GraySort é uma espécie de benchmark. A métrica é a taxa de ordenação (TB por minuto) que é obtida ao ordenar grandes quantidades de dados, normalmente, um 100 TB Mínimo.

Este exemplo utiliza um modesto 10 GB de dados para que podem ser executada relativamente rapidamente. Ele usa os aplicativos de MapReduce desenvolvidos pela Owen O'Malley e a Arun Murthy. Esses aplicativos venceram o parâmetro de comparação de ordenação de terabytes de fins gerais ("daytona") anual em 2009, com uma taxa de 0.578 TB por minuto (100 TB, em minutos 173). Para obter mais informações sobre este e outros parâmetros de comparação de classificação, consulte a [Sortbenchmark](https://sortbenchmark.org/) site.

Este exemplo utiliza três conjuntos de programas MapReduce:

* **TeraGen**: Um programa MapReduce que gera linhas de dados para classificar

* **TeraSort**: Os dados de entrada de exemplo e usa o MapReduce para classificar os dados para um total do pedido

    TeraSort é uma espécie de MapReduce padrão, exceto um particionador personalizado. O particionador utiliza uma lista ordenada de chaves do objeto de amostragem de n-1 que definem o intervalo de chaves para cada reduza. Em particular, todas as chaves desse tipo que fornecem uma amostra [i-1] < = chave < exemplo [i] são enviadas para reduzir a i. Este particionador são todas as garantias de que as saídas de reduzem i menores do que a saída de reduzir i + 1.

* **TeraValidate**: Um programa MapReduce que valida que a saída globalmente está classificada

    Ele cria um mapa por ficheiro no diretório de saída, e cada mapa garante que cada chave é menor ou igual ao anterior. A função map gera registos das chaves próprio e apelidos de cada arquivo. A função de reduza garante que a primeira chave de ficheiro i é maior do que a última chave do i-1 de ficheiro. Todos os problemas são apresentados como uma saída da fase de reduza, com as chaves que estão fora de ordem.

Utilize os seguintes passos para gerar dados, ordenar e, em seguida, validar o resultado:

1. Gerar 10 GB de dados, o que são armazenados para armazenamento de predefinido do cluster do HDInsight em `/example/data/10GB-sort-input`:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    O `-Dmapred.map.tasks` informa ao Hadoop quantas tarefas de mapa a utilizar para esta tarefa. Os dois parâmetros finais instruir a tarefa para criar 10 GB de dados e armazená-los em `/example/data/10GB-sort-input`.

2. Utilize o seguinte comando para classificar os dados:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    O `-Dmapred.reduce.tasks` informa ao Hadoop tarefas a utilizar para a tarefa de reduzir o número. Os dois parâmetros finais são apenas os entrada e saídos locais para dados.

3. Utilize o seguinte para validar os dados gerados pela classificação:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a executar os exemplos incluídos com os clusters do HDInsight baseado em Linux. Para tutoriais sobre como utilizar o Pig, Hive e o MapReduce com o HDInsight, consulte os tópicos seguintes:

* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-introduction]:apache-hadoop-introduction.md

