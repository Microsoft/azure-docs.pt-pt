---
title: Executar Apache Hadoop exemplos de MapReduce no HDInsight – Azure
description: Introdução ao uso de exemplos de MapReduce em arquivos jar incluídos no HDInsight. Use o SSH para se conectar ao cluster e, em seguida, use o comando do Hadoop para executar trabalhos de exemplo.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: 58f7d99af638c8d03bbce46b7fcf8204aaca11d9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435754"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Executar os exemplos de MapReduce incluídos no HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Saiba como executar os exemplos de MapReduce incluídos com Apache Hadoop no HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache Hadoop no HDInsight. Consulte [introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Os exemplos do MapReduce

Os exemplos estão localizados no cluster HDInsight em `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`. O código-fonte para esses exemplos está incluído no cluster HDInsight em `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

Os exemplos a seguir estão contidos neste arquivo morto:

|Exemplo |Descrição |
|---|---|
|aggregatewordcount|Conta as palavras nos arquivos de entrada.|
|aggregatewordhist|Computa o histograma das palavras nos arquivos de entrada.|
|BBP|Usa Bailey-Borwein-Plouffe para computar os dígitos exatos de PI.|
|dbcount|Conta os logs de Pageview armazenados em um banco de dados.|
|distbbp|Usa uma fórmula de tipo BBP para computar bits exatos de PI.|
|grep|Conta as correspondências de um Regex na entrada.|
|aderir|Executa uma junção sobre conjuntos de valores classificados e particionados igualmente.|
|multifilewc|Conta palavras de vários arquivos.|
|pentomino|Programa de layout de bloco para encontrar soluções para problemas de pentomino.|
|13,PI|Estima PI usando um método de Carlo quase monte.|
|randomtextwriter|Grava 10 GB de dados textuais aleatórios por nó.|
|randomwriter|Grava 10 GB de dados aleatórios por nó.|
|SecondarySort|Define uma classificação secundária para a fase de redução.|
|ordenar|Classifica os dados gravados pelo gravador aleatório.|
|Sudoku|Um resolvedor do Sudoku.|
|teragen|Gere dados para o terasort.|
|terasort|Execute o terasort.|
|teravalidate|Verificando os resultados de terasort.|
|WordCount|Conta as palavras nos arquivos de entrada.|
|wordmean|Conta o comprimento médio das palavras nos arquivos de entrada.|
|wordmedian|Conta o comprimento mediano das palavras nos arquivos de entrada.|
|wordstandarddeviation|Conta o desvio padrão do comprimento das palavras nos arquivos de entrada.|

## <a name="run-the-wordcount-example"></a>Executar o exemplo de WordCount

1. Conecte-se ao HDInsight usando SSH. Substitua `CLUSTER` pelo nome do cluster e, em seguida, digite o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. Na sessão SSH, use o seguinte comando para listar os exemplos:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Esse comando gera a lista de exemplos da seção anterior deste documento.

3. Use o comando a seguir para obter ajuda sobre um exemplo específico. Nesse caso, o exemplo de **WordCount** :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Você receberá a seguinte mensagem:

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    Essa mensagem indica que você pode fornecer vários caminhos de entrada para os documentos de origem. O caminho final é o local em que a saída (contagem de palavras nos documentos de origem) é armazenada.

4. Use o seguinte para contar todas as palavras nos blocos de anotações do Leonardo da Vinci, que são fornecidas como dados de exemplo com o cluster:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    A entrada para este trabalho é lida de `/example/data/gutenberg/davinci.txt`. A saída para este exemplo é armazenada em `/example/data/davinciwordcount`. Ambos os caminhos estão localizados no armazenamento padrão para o cluster, não no sistema de arquivos local.

   > [!NOTE]  
   > Conforme observado na ajuda do exemplo WordCount, você também pode especificar vários arquivos de entrada. Por exemplo, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` contaria palavras em DaVinci. txt e Ulysses. txt.

5. Quando o trabalho for concluído, use o seguinte comando para exibir a saída:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Esse comando concatena todos os arquivos de saída produzidos pelo trabalho. Ele exibe a saída para o console. O resultado é semelhante ao seguinte texto:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Cada linha representa uma palavra e quantas vezes ela ocorreu nos dados de entrada.

## <a name="the-sudoku-example"></a>O exemplo do Sudoku

O [Sudoku](https://en.wikipedia.org/wiki/Sudoku) é um quebra-cabeça lógico composto por nove grades de 3x3. Algumas células na grade têm números, enquanto outras estão em branco, e a meta é resolver as células em branco. O link anterior tem mais informações sobre o quebra-cabeça, mas a finalidade deste exemplo é resolver as células em branco. Portanto, nossa entrada deve ser um arquivo que esteja no seguinte formato:

* Nove linhas de nove colunas
* Cada coluna pode conter um número ou `?` (que indica uma célula em branco)
* As células são separadas por um espaço

Há uma determinada maneira de construir o quebra-cabeças Sudoku; Não é possível repetir um número em uma coluna ou linha. Há um exemplo no cluster HDInsight que é construído corretamente. Ele está localizado em `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` e contém o seguinte texto:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Para executar este exemplo de problema por meio do exemplo do Sudoku, use o seguinte comando:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Os resultados são semelhantes ao seguinte texto:

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

O exemplo de PI usa um método estatístico (quase Monte Carlo) para estimar o valor de PI. Os pontos são colocados aleatoriamente em um quadrado de unidade. O quadrado também contém um círculo. A probabilidade de os pontos ficarem dentro do círculo é igual à área do círculo, PI/4. O valor de PI pode ser estimado a partir do valor de 4R. R é a proporção do número de pontos que estão dentro do círculo para o número total de pontos que estão dentro do quadrado. Quanto maior a amostra de pontos usados, melhor será a estimativa.

Use o comando a seguir para executar este exemplo. Este comando usa 16 mapas com 10 milhões amostras para estimar o valor de PI:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

O valor retornado por esse comando é semelhante a **3,14159155000000000000**. Para referências, as 10 primeiras casas decimais do PI são 3,1415926535.

## <a name="10-gb-graysort-example"></a>exemplo de GraySort de 10 GB

GraySort é uma classificação de parâmetro de comparação. A métrica é a taxa de classificação (TB/minuto) que é obtida durante a classificação de grandes quantidades de dados, geralmente um mínimo de 100 TB.

Este exemplo usa um modesto de 10 GB de dados para que possa ser executado relativamente rapidamente. Ele usa os aplicativos MapReduce desenvolvidos por Owen ' Malley e Arun Murthy. Esses aplicativos ganharam o parâmetro de comparação anual de classificação de terabytes de uso geral ("Daytona") em 2009, com uma taxa de 0,578 TB/min (100 TB em 173 minutos). Para obter mais informações sobre esse e outros parâmetros de comparação de classificação, consulte o site de [comparação de classificação](https://sortbenchmark.org/) .

Este exemplo usa três conjuntos de programas MapReduce:

* **TeraGen**: um programa MapReduce que gera linhas de dados a serem classificados

* **TeraSort**: amostra os dados de entrada e usa o MapReduce para classificar os dados em uma ordem total

    TeraSort é uma classificação MapReduce padrão, exceto para um particionador personalizado. O particionador usa uma lista classificada de N-1 chaves de amostra que definem o intervalo de chaves para cada redução. Em particular, todas as chaves, como o exemplo [i-1] < = Key < Sample [i], são enviadas para reduzir i. Esse particionador garante que as saídas de redução sejam menores que a saída de reduzir i + 1.

* **TeraValidate**: um programa MapReduce que valida se a saída é classificada globalmente

    Ele cria um mapa por arquivo no diretório de saída, e cada mapa garante que cada chave seja menor ou igual à anterior. A função MAP gera registros da primeira e da última chave de cada arquivo. A função de redução garante que a primeira chave do arquivo i seja maior que a última chave do arquivo i-1. Todos os problemas são relatados como uma saída da fase de redução, com as chaves que estão fora de ordem.

Use as etapas a seguir para gerar dados, classificar e validar a saída:

1. Gere 10 GB de dados, que são armazenados no armazenamento padrão do cluster HDInsight em `/example/data/10GB-sort-input`:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    O `-Dmapred.map.tasks` informa ao Hadoop quantas tarefas de mapa usar para esse trabalho. Os dois últimos parâmetros instruem o trabalho a criar 10 GB de dados e armazená-los em `/example/data/10GB-sort-input`.

2. Use o seguinte comando para classificar os dados:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    O `-Dmapred.reduce.tasks` informa ao Hadoop quantas tarefas de redução usar para o trabalho. Os dois últimos parâmetros são apenas os locais de entrada e saída dos dados.

3. Use o seguinte para validar os dados gerados pela classificação:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a executar os exemplos incluídos nos clusters HDInsight baseados em Linux. Para obter tutoriais sobre como usar o Pig, o hive e o MapReduce com o HDInsight, consulte os seguintes tópicos:

* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)