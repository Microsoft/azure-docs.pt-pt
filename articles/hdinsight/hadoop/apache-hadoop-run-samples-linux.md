---
title: Executar Apache Hadoop MapReduce exemplos em HDInsight - Azure
description: Começa a usar amostras do MapReduce em ficheiros de frascos incluídos no HDInsight. Use o SSH para ligar ao cluster e, em seguida, use o comando Hadoop para executar trabalhos de amostragem.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: 5e75a39b1f9e8503097914b0c9e735915f9ae667
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943219"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Executar os exemplos MapReduce incluídos no HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Aprenda a executar os exemplos mapReduce incluídos com Apache Hadoop em HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache Hadoop em HDInsight. Ver [Começar com HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Um cliente SSH. Para obter mais informações, veja [Ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Os exemplos mapReduce

As amostras estão localizadas no aglomerado HDInsight em `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar` . O código-fonte destas amostras está incluído no cluster HDInsight em `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples` .

Neste arquivo estão contidas as seguintes amostras:

|Sample |Descrição |
|---|---|
|agregação de palavras|Conta as palavras nos ficheiros de entrada.|
|agregadowordhist|Calcula o histograma das palavras nos ficheiros de entrada.|
|bbp|Usa Bailey-Borwein-Plouffe para calcular os dígitos exatos de Pi.|
|dbcount|Conta os registos de visão de página armazenados numa base de dados.|
|distbbp|Usa uma fórmula do tipo BBP para calcular partes exatas de Pi.|
|grep|Conta os fósforos de um regex na entrada.|
|aderir|Executa uma junção sobre conjuntos de dados classificados, igualmente divididos.|
|multifilewc|Conta palavras de vários ficheiros.|
|pentomino|Programa de colocação de azulejos para encontrar soluções para problemas pentominos.|
|pi|Estima pi usando um método quasi-Monte Carlo.|
|escritor detexto aleatório|Escreve 10 GB de dados texódricos aleatórios por nó.|
|escritor aleatório|Escreve 10 GB de dados aleatórios por nó.|
|secundário|Define um tipo secundário para a fase de redução.|
|ordenar|Classifica os dados escritos pelo escritor aleatório.|
|sudoku|Um solucionador de sudoku.|
|teragen|Gere dados para o terasort.|
|terasort|Executar o terasort.|
|teravalidate|Verificando os resultados do terasort.|
|contagem de palavras|Conta as palavras nos ficheiros de entrada.|
|wordmean|Conta o comprimento médio das palavras nos ficheiros de entrada.|
|wordmedian|Conta o comprimento médio das palavras nos ficheiros de entrada.|
|palavras padrãovial|Conta o desvio padrão do comprimento das palavras nos ficheiros de entrada.|

## <a name="run-the-wordcount-example"></a>Executar o exemplo de contagem de palavras

1. Ligue-se ao HDInsight utilizando SSH. `CLUSTER`Substitua-o pelo nome do seu cluster e, em seguida, introduza o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. A partir da sessão SSH, utilize o seguinte comando para listar as amostras:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Este comando gera a lista de amostras da secção anterior deste documento.

3. Utilize o seguinte comando para obter ajuda numa amostra específica. Neste caso, a amostra **de wordcount:**

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Recebe a seguinte mensagem:

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    Esta mensagem indica que pode fornecer vários caminhos de entrada para os documentos de origem. O caminho final é onde a saída (contagem de palavras nos documentos de origem) é armazenada.

4. Utilize o seguinte para contar todas as palavras nos Cadernos de Leonardo da Vinci, que são fornecidos como dados de amostra com o seu cluster:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    A entrada para este trabalho é lida a partir de `/example/data/gutenberg/davinci.txt` . A saída para este exemplo é armazenada em `/example/data/davinciwordcount` . Ambos os caminhos estão localizados no armazenamento predefinido para o cluster, não no sistema de ficheiros local.

   > [!NOTE]  
   > Como indicado na ajuda para a amostra de wordcount, também pode especificar vários ficheiros de entrada. Por exemplo, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` contaria palavras tanto em davinci.txt como ulysses.txt.

5. Uma vez concluído o trabalho, utilize o seguinte comando para visualizar a saída:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Este comando concatena todos os ficheiros de saída produzidos pelo trabalho. Apresenta a saída para a consola. O resultado é semelhante ao seguinte texto:

    ```output
    zum     1
    zur     1
    zwanzig 1
    zweite  1
    ```

    Cada linha representa uma palavra e quantas vezes ocorreu nos dados de entrada.

## <a name="the-sudoku-example"></a>O exemplo de Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) é um puzzle lógico composto por nove redes 3x3. Algumas células na grelha têm números, enquanto outras estão em branco, e o objetivo é resolver para as células em branco. O link anterior tem mais informações sobre o puzzle, mas o objetivo desta amostra é resolver para as células em branco. Portanto, a nossa entrada deve ser um ficheiro que está no seguinte formato:

* Nove filas de nove colunas
* Cada coluna pode conter um número ou `?` (o que indica uma célula em branco)
* As células são separadas por um espaço

Há uma certa maneira de construir puzzles sudoku; não se pode repetir um número numa coluna ou numa linha. Há um exemplo no cluster HDInsight que é devidamente construído. Está localizado `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` e contém o seguinte texto:

```output
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

Para passar este exemplo do problema através do exemplo de Sudoku, utilize o seguinte comando:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Os resultados parecem semelhantes ao seguinte texto:

```output
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

## <a name="pi--example"></a>Pi (π) exemplo

A amostra pi utiliza um método estatístico (quasi-Monte Carlo) para estimar o valor de pi. Os pontos são colocados aleatoriamente num quadrado de unidade. O quadrado também contém um círculo. A probabilidade de os pontos se enquadrarem dentro do círculo é igual à área do círculo, pi/4. O valor de pi pode ser estimado a partir do valor de 4R. R é a razão do número de pontos que estão dentro do círculo para o número total de pontos que estão dentro do quadrado. Quanto maior for a amostra de pontos utilizados, melhor será a estimativa.

Utilize o seguinte comando para executar esta amostra. Este comando usa 16 mapas com 10.000.000 amostras cada para estimar o valor de pi:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

O valor devolvido por este comando é semelhante a **3.141591550000000000000000000.** Para referências, as primeiras 10 casas decimais de pi são 3.1415926535.

## <a name="10-gb-graysort-example"></a>10 GB exemplo GraySort

GraySort é uma espécie de referência. A métrica é a taxa de classificação (TB/minuto) que é alcançada ao separar grandes quantidades de dados, geralmente um mínimo de 100 TB.

Esta amostra utiliza um modesto 10 GB de dados para que possa ser executado com relativa rapidez. Utiliza as aplicações MapReduce desenvolvidas por Owen O'Malley e Arun Murthy. Estas aplicações ganharam o benchmark anual de terabyte ("Daytona") em 2009, com uma taxa de 0,578 TB/min (100 TB em 173 minutos). Para obter mais informações sobre esta e outras referências de triagem, consulte o site ['Sort Benchmark'.](https://sortbenchmark.org/)

Esta amostra utiliza três conjuntos de programas MapReduce:

* **TeraGen**: Um programa MapReduce que gera linhas de dados para classificar

* **TeraSort**: Amostra os dados de entrada e utiliza o MapReduce para classificar os dados numa ordem total

    TeraSort é uma espécie de MapReduce padrão, exceto para um divisor personalizado. O divisidor usa uma lista ordenada de chaves amostradas N-1 que definem a gama de chaves para cada redução. Em especial, todas as chaves tais que a amostra[i-1] <= chave < amostra[i] são enviadas para reduzir i. Este divise garante que as saídas de redução i são todas inferiores à saída de redução i+1.

* **TeraValidate**: Um programa MapReduce que valida que a saída está globalmente ordenada

    Cria um mapa por ficheiro no diretório de saída, e cada mapa garante que cada chave é inferior ou igual à anterior. A função do mapa gera registos das primeira e últimas teclas de cada ficheiro. A função de redução garante que a primeira chave do ficheiro i é maior do que a última chave do ficheiro i-1. Quaisquer problemas são reportados como uma saída da fase de redução, com as chaves que estão fora de ordem.

Utilize os seguintes passos para gerar dados, classificar e, em seguida, validar a saída:

1. Gere 10 GB de dados, que é armazenado no armazenamento padrão do cluster HDInsight `/example/data/10GB-sort-input` em:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    O `-Dmapred.map.tasks` diz ao Hadoop quantas tarefas de mapas usar para este trabalho. Os dois parâmetros finais instruem o trabalho a criar 10 GB de dados e a armazená-lo em `/example/data/10GB-sort-input` .

2. Utilize o seguinte comando para classificar os dados:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    O `-Dmapred.reduce.tasks` diz ao Hadoop quantas tarefas reduzem para o trabalho. Os dois parâmetros finais são apenas os locais de entrada e saída para os dados.

3. Utilize o seguinte para validar os dados gerados pelo tipo:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Próximos passos

A partir deste artigo, aprendeu a executar as amostras incluídas com os clusters HDInsight baseados em Linux. Para tutoriais sobre a utilização de Porco, Colmeia e MapReduce com HDInsight, consulte os seguintes tópicos:

* [Use a Colmeia Apache com Apache Hadoop em HDInsight](hdinsight-use-hive.md)
* [Use MapReduce com Apache Hadoop em HDInsight](hdinsight-use-mapreduce.md)
