---
title: Gere recomendações usando Apache Mahout no Azure HDInsight
description: Aprenda a usar a biblioteca de machine learning Apache Mahout para gerar recomendações de filmes com o HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/14/2020
ms.openlocfilehash: ab4c2984bbaef84684432c660baadc78f3ef8e16
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656325"
---
# <a name="generate-recommendations-using-apache-mahout-in-azure-hdinsight"></a>Gere recomendações usando Apache Mahout no Azure HDInsight

Aprenda a usar a biblioteca de machine learning [Apache Mahout](https://mahout.apache.org) com o Azure HDInsight para gerar recomendações de filmes.

Mahout é uma biblioteca de [machine learning](https://en.wikipedia.org/wiki/Machine_learning) para Apache Hadoop. Mahout contém algoritmos para processar dados, tais como filtragem, classificação e agrupamento. Neste artigo, você usa um motor de recomendação para gerar recomendações de filmes que são baseados em filmes que os seus amigos viram.

Para obter mais informações sobre a versão de Mahout no HDInsight, consulte [as versões HDInsight e os componentes Apache Hadoop](../hdinsight-component-versioning.md).

## <a name="prerequisites"></a>Pré-requisitos

Um aglomerado Apache Hadoop no HDInsight. Ver [Começar com HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="understanding-recommendations"></a>Compreender recomendações

Uma das funções que é fornecida por Mahout é um motor de recomendação. Este motor aceita dados no formato de `userID` `itemId` , e `prefValue` (a preferência pelo item). Mahout pode então realizar análises de coocorrência para determinar: *os utilizadores que têm preferência por um item também têm preferência por estes outros itens*. Mahout determina então os utilizadores com preferências similares, que podem ser usadas para fazer recomendações.

O seguinte fluxo de trabalho é um exemplo simplificado que utiliza dados de filmes:

* **Coocorrência**: Joe, Alice e Bob todos gostaram de *Star Wars*, The *Empire Strikes Back*, e Return of the *Jedi*. Mahout determina que os utilizadores que gostam de qualquer um destes filmes também gostam dos outros dois.

* **Coocorrência**: Bob e Alice também gostaram de *A Ameaça Fantasma,* *Ataque dos Clones*, e *Revenge of the Sith*. Mahout determina que os utilizadores que gostaram dos três filmes anteriores também gostam destes três filmes.

* **Recomendação de semelhança**: Como Joe gostou dos três primeiros filmes, Mahout olha para filmes que outros com preferências semelhantes gostavam, mas Joe não assistiu (gosto/avaliado). Neste caso, Mahout recomenda *A Ameaça Fantasma*, Ataque dos *Clones*, e *Vingança do Sith*.

### <a name="understanding-the-data"></a>Compreender os dados

Convenientemente, a [GroupLens Research](https://grouplens.org/datasets/movielens/) fornece dados de classificação para filmes num formato compatível com Mahout. Estes dados estão disponíveis no armazenamento predefinido do seu cluster em `/HdiSamples/HdiSamples/MahoutMovieData` .

Há dois `moviedb.txt` ficheiros, `user-ratings.txt` e. O `user-ratings.txt` ficheiro é utilizado durante a análise. O `moviedb.txt` utilizador é utilizado para fornecer informações de texto fáceis de utilizar ao visualizar os resultados.

Os dados contidos `user-ratings.txt` têm uma estrutura `userID` `movieID` de, , e , que `userRating` indica o `timestamp` quão altamente cada utilizador classificou um filme. Aqui está um exemplo dos dados:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Executar a análise

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu cluster. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Utilize o seguinte comando para executar o trabalho de recomendação:

    ```bash
    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
    ```

> [!NOTE]  
> O trabalho pode demorar vários minutos a ser concluído, e pode executar vários trabalhos mapReduce.

## <a name="view-the-output"></a>Ver a saída

1. Uma vez concluída a função, utilize o seguinte comando para visualizar a saída gerada:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    A saída aparece da seguinte forma:

    ```output
    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
    ```

    A primeira coluna é a `userID` . Os valores contidos em «[» e "]" `movieId` são: `recommendationScore` .

2. Pode utilizar a saída, juntamente com o moviedb.txt, para fornecer mais informações sobre as recomendações. Em primeiro lugar, copie os ficheiros localmente utilizando os seguintes comandos:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Este comando copia os dados de saída para um ficheiro chamado **recomendações.txt** no diretório atual, juntamente com os ficheiros de dados do filme.

3. Utilize o seguinte comando para criar um script Python que procure nomes de filmes para os dados na saída de recomendações:

    ```bash
    nano show_recommendations.py
    ```

    Quando o editor abrir, utilize o seguinte texto como conteúdo do ficheiro:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Prima **CTRL-X,** **Y,** e finalmente **introduza** para guardar os dados.

4. Executa o guião python. O seguinte comando assume que você está no diretório onde todos os ficheiros foram descarregados:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Este comando analisa as recomendações geradas para o ID 4 do utilizador.

   * O ficheiro **user-ratings.txt** é usado para recuperar filmes que foram avaliados.

   * O ficheiro **moviedb.txt** é usado para recuperar os nomes dos filmes.

   * As **recomendações.txt** são usadas para recuperar as recomendações do filme para este utilizador.

     A saída deste comando é semelhante ao seguinte texto:

        ```output
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        ```

## <a name="delete-temporary-data"></a>Eliminar dados temporários

Os empregos de Mahout não removem dados temporários que são criados durante o processamento do trabalho. O parâmetro é especificado no trabalho de `--tempDir` exemplo para isolar os ficheiros temporários num caminho específico para facilitar a supressão. Para remover os ficheiros temporários, utilize o seguinte comando:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Se quiser voltar a executar o comando, deve também eliminar o diretório de saída. Utilize o seguinte para eliminar este diretório:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar Mahout, descubra outras formas de trabalhar com dados no HDInsight:

* [Colmeia Apache com HDInsight](hdinsight-use-hive.md)
* [MapReduce com HDInsight](hdinsight-use-mapreduce.md)
