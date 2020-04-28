---
title: Executar Apache Hadoop MapReduce exemplos em HDInsight - Azure
description: Começar a usar amostras MapReduce em ficheiros de frascos incluídos no HDInsight. Utilize o SSH para se ligar ao cluster e, em seguida, use o comando Hadoop para executar trabalhos de amostra.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: 58f7d99af638c8d03bbce46b7fcf8204aaca11d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75435754"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Executar os exemplos MapReduce incluídos no HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Saiba como executar os exemplos MapReduce incluídos com Apache Hadoop no HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache Hadoop no HDInsight. Ver [Começar com HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>MapReduce exemplos

As amostras estão localizadas `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`no cluster HDInsight em . O código fonte destas amostras está incluído `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`no cluster HDInsight em .

As seguintes amostras estão contidas neste arquivo:

|Sample |Descrição |
|---|---|
|contagem de palavras agregada|Conta as palavras nos ficheiros de entrada.|
|agregacionista|Calcula o histograma das palavras nos ficheiros de entrada.|
|bbp|Usa Bailey-Borwein-Plouffe para calcular os dígitos exatos de Pi.|
|dbcount|Conta os registos de página armazenados numa base de dados.|
|distbbp|Usa uma fórmula do tipo BBP para calcular partes exatas de Pi.|
|grep|Conta os fósforos de um regex na entrada.|
|aderir|Executa uma adesão sobre conjuntos de dados igualmente divididos.|
|multifilewc|Conta palavras de vários ficheiros.|
|pentomino|Programa de colocação de azulejos para encontrar soluções para problemas pentomino.|
|pi|Estima Pi usando um método quase Monte Carlo.|
|escritor de texto aleatório|Escreve 10 GB de dados texuais aleatórios por nó.|
|escritor aleatório|Escreve 10 GB de dados aleatórios por nó.|
|secundária|Define um tipo secundário para a fase de redução.|
|ordenar|Classifica os dados escritos pelo escritor aleatório.|
|sudoku|Um solucionador de sudoku.|
|teragen|Gerar dados para o terasort.|
|terasort|Executar o terasort.|
|teravavar|Verificando os resultados do terasort.|
|contagem de palavras|Conta as palavras nos ficheiros de entrada.|
|wordmean|Conta o comprimento médio das palavras nos ficheiros de entrada.|
|palavramedia|Conta o comprimento mediano das palavras nos ficheiros de entrada.|
|wordstandarddesviotion|Conta o desvio padrão do comprimento das palavras nos ficheiros de entrada.|

## <a name="run-the-wordcount-example"></a>Executar o exemplo do wordcount

1. Ligue-se ao HDInsight utilizando SSH. Substitua-o `CLUSTER` pelo nome do seu cluster e, em seguida, introduza o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. A partir da sessão SSH, utilize o seguinte comando para listar as amostras:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Este comando gera a lista de amostras da secção anterior deste documento.

3. Utilize o seguinte comando para obter ajuda numa amostra específica. Neste caso, a amostra **de contagem de palavras:**

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

    A entrada para este `/example/data/gutenberg/davinci.txt`trabalho é lida a partir de . A saída para este `/example/data/davinciwordcount`exemplo é armazenada em . Ambos os caminhos estão localizados no armazenamento padrão para o cluster, não no sistema de ficheiros local.

   > [!NOTE]  
   > Como notado na ajuda para a amostra de contagem de palavras, também pode especificar vários ficheiros de entrada. Por exemplo, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` contaria palavras em davinci.txt e ulysses.txt.

5. Uma vez concluída a função, utilize o seguinte comando para visualizar a saída:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Este comando concatena todos os ficheiros de saída produzidos pelo trabalho. Exibe a saída para a consola. O resultado é semelhante ao seguinte texto:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Cada linha representa uma palavra e quantas vezes ocorreu nos dados de entrada.

## <a name="the-sudoku-example"></a>O exemplo de Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) é um puzzle lógico composto por nove grelhas 3x3. Algumas células da grelha têm números, enquanto outras estão em branco, e o objetivo é resolver para as células em branco. O elo anterior tem mais informações sobre o puzzle, mas o propósito desta amostra é resolver para as células em branco. Assim, a nossa entrada deve ser um ficheiro que está no seguinte formato:

* Nove linhas de nove colunas
* Cada coluna pode conter `?` um número ou (o que indica uma célula em branco)
* As células são separadas por um espaço

Há uma certa maneira de construir puzzles sudoku; não se pode repetir um número numa coluna ou numa linha. Há um exemplo no cluster HDInsight que é devidamente construído. Está localizado `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` e contém o seguinte texto:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Para executar este problema de exemplo através do exemplo Sudoku, use o seguinte comando:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Os resultados parecem semelhantes ao seguinte texto:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Pi (π) exemplo

A amostra pi utiliza um método estatístico (quase-Monte Carlo) para estimar o valor do pi. Os pontos são colocados aleatoriamente num quadrado de unidade. A praça também contém um círculo. A probabilidade de os pontos cairem dentro do círculo é igual à área do círculo, pi/4. O valor do pi pode ser estimado a partir do valor de 4R. R é o rácio do número de pontos que estão dentro do círculo para o número total de pontos que estão dentro da praça. Quanto maior for a amostra de pontos usados, melhor será a estimativa.

Utilize o seguinte comando para executar esta amostra. Este comando utiliza 16 mapas com 10.000.000 amostras cada para estimar o valor do pi:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

O valor devolvido por este comando é semelhante ao **de 3.1415915500000000000000000000000000 .** Para referências, os primeiros 10 lugares decimais de pi são 3.1415926535.

## <a name="10-gb-graysort-example"></a>Exemplo de 10 GB GraySort

GraySort é um tipo de referência. A métrica é a taxa de classificação (TB/minuto) que é alcançada ao mesmo tempo que seclassifica grandes quantidades de dados, geralmente um mínimo de 100 TB.

Esta amostra utiliza um modesto 10 GB de dados para que possa ser executado com relativa rapidez. Utiliza as aplicações MapReduce desenvolvidas por Owen O'Malley e Arun Murthy. Estas aplicações ganharam o referencial anual de fim de semana ("Daytona") tipo terabyte em 2009, com uma taxa de 0,578 TB/min (100 TB em 173 minutos). Para obter mais informações sobre este e outros referenciais de classificação, consulte o site [Sort Benchmark.](https://sortbenchmark.org/)

Esta amostra utiliza três conjuntos de programas MapReduce:

* **TeraGen**: Um programa MapReduce que gera linhas de dados para classificar

* **TeraSort**: Amostraos os dados de entrada e utiliza mapReduce para classificar os dados numa ordem total

    TeraSort é um tipo padrão MapReduce, exceto para um divisória personalizado. O divisória utiliza uma lista ordenada de teclas amostradas N-1 que definem a gama de chaves para cada redução. Em particular, todas as teclas, de modo a que a amostra[i-1] <= amostra de < chave[i] sejam enviadas para reduzir i. Este divisória garante que as saídas de redução i são todas menos do que a saída de reduzir i+1.

* **TeraValidate**: Um programa MapReduce que valida que a saída está globalmente classificada

    Cria um mapa por ficheiro no diretório de saída, e cada mapa garante que cada chave é inferior ou igual à anterior. A função do mapa gera registos das primeiras e últimas teclas de cada ficheiro. A função de redução garante que a primeira chave do ficheiro i é maior do que a última chave do ficheiro i-1. Quaisquer problemas são reportados como uma saída da fase de redução, com as teclas que estão fora de ordem.

Utilize os seguintes passos para gerar dados, classificar e, em seguida, validar a saída:

1. Gere 10 GB de dados, que são armazenados no armazenamento `/example/data/10GB-sort-input`padrão do cluster HDInsight em:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    O `-Dmapred.map.tasks` Hadoop diz a Hadoop quantas tarefas de mapa usar para este trabalho. Os dois parâmetros finais instruem o trabalho a criar `/example/data/10GB-sort-input`10 GB de dados e a armazená-lo em .

2. Utilize o seguinte comando para ordenar os dados:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    O `-Dmapred.reduce.tasks` Diz a Hadoop quantas tarefas reduzem para o trabalho. Os dois parâmetros finais são apenas os locais de entrada e saída para dados.

3. Utilize o seguinte para validar os dados gerados pelo tipo:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Passos seguintes

A partir deste artigo, aprendeu a executar as amostras incluídas nos clusters HDInsight baseados em Linux. Para tutoriais sobre a utilização de Porco, Colmeia e MapReduce com HDInsight, consulte os seguintes tópicos:

* [Use a Colmeia Apache com Hadoop Apache no HDInsight](hdinsight-use-hive.md)
* [Use mapReduce com Hadoop Apache no HDInsight](hdinsight-use-mapreduce.md)