---
title: Gerar recomendações usando Apache Mahout em Azure HDInsight
description: Aprenda a usar a biblioteca de machine learning Apache Mahout para gerar recomendações de filme com HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/14/2020
ms.openlocfilehash: c31ffaf094801bdd49e5800bd338a15d8b8315f6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946494"
---
# <a name="generate-recommendations-using-apache-mahout-in-azure-hdinsight"></a>Gerar recomendações usando Apache Mahout em Azure HDInsight

Aprenda a usar a biblioteca de machine learning [Apache Mahout](https://mahout.apache.org) com Azure HDInsight para gerar recomendações de filme.

Mahout é uma biblioteca [de aprendizagem automática](https://en.wikipedia.org/wiki/Machine_learning) para Apache Hadoop. Mahout contém algoritmos para o processamento de dados, tais como filtragem, classificação e agrupamento. Neste artigo, você usa um motor de recomendação para gerar recomendações de filme que são baseadas em filmes que seus amigos viram.

Para obter mais informações sobre a versão do Mahout em HDInsight, consulte as [versões HDInsight e os componentes Apache Hadoop](../hdinsight-component-versioning.md).

## <a name="prerequisites"></a>Pré-requisitos

Um aglomerado Apache Hadoop em HDInsight. Ver [Começar com HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="understanding-recommendations"></a>Recomendações de compreensão

Uma das funções que é fornecida pela Mahout é um motor de recomendação. Este motor aceita dados no formato de `userID` `itemId` , e `prefValue` (a preferência pelo item). A Mahout pode então realizar análises de coocorrência para determinar: *os utilizadores que têm preferência por um item também têm preferência por estes outros itens.* A Mahout determina então os utilizadores com preferências de artigos similares, que podem ser usados para fazer recomendações.

O seguinte fluxo de trabalho é um exemplo simplificado que utiliza dados de filmes:

* **Coocorrência:** Joe, Alice e Bob todos gostaram de *Star Wars*, The *Empire Strikes Back*, e Return of the *Jedi.* Mahout determina que os utilizadores que gostam de qualquer um destes filmes também gostam dos outros dois.

* **Coocorrência**: Bob e Alice também gostaram de *A Ameaça Fantasma,* *Ataque dos Clones,* e *Vingança dos Sith.* Mahout determina que os utilizadores que gostaram dos três filmes anteriores também gostam destes três filmes.

* **Recomendação de semelhança**: Porque Joe gostou dos três primeiros filmes, Mahout olha para filmes que outros com preferências semelhantes gostavam, mas Joe não assistiu (gosto/classificado). Neste caso, Mahout recomenda *A Ameaça Fantasma,* *Ataque dos Clones,* e *Vingança dos Sith.*

### <a name="understanding-the-data"></a>Compreender os dados

Convenientemente, [o GroupLens Research](https://grouplens.org/datasets/movielens/) fornece dados de classificação para filmes num formato compatível com o Mahout. Estes dados estão disponíveis no armazenamento predefinido do seu cluster em `/HdiSamples/HdiSamples/MahoutMovieData` .

Há dois ficheiros, `moviedb.txt` `user-ratings.txt` e. O `user-ratings.txt` ficheiro é utilizado durante a análise. O `moviedb.txt` é utilizado para fornecer informações de texto fáceis de utilizar ao visualizar os resultados.

Os dados contidos `user-ratings.txt` têm uma estrutura `userID` `movieID` de, e , , que `userRating` indica o `timestamp` quão altamente cada utilizador avaliou um filme. Aqui está um exemplo dos dados:

```output
    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596
```

## <a name="run-the-analysis"></a>Executar a análise

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

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

1. Uma vez concluído o trabalho, utilize o seguinte comando para visualizar a saída gerada:

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

    A primeira coluna é `userID` a. Os valores contidos em «[» e `movieId` «»» são: `recommendationScore` .

2. Pode utilizar a saída, juntamente com o moviedb.txt, para fornecer mais informações sobre as recomendações. Primeiro, copie os ficheiros localmente utilizando os seguintes comandos:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Este comando copia os dados de saída de um ficheiro nomeado **recommendations.txt** no diretório atual, juntamente com os ficheiros de dados do filme.

3. Use o seguinte comando para criar um script Python que procure nomes de filmes para os dados na saída das recomendações:

    ```bash
    nano show_recommendations.py
    ```

    Quando o editor abrir, utilize o seguinte texto como o conteúdo do ficheiro:

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

    Prima **Ctrl-X,** **Y,** e finalmente **introduza** para guardar os dados.

4. Executar o roteiro python. O seguinte comando pressupõe que está no diretório onde todos os ficheiros foram descarregados:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Este comando analisa as recomendações geradas para o ID 4 do utilizador.

   * O **ficheirouser-ratings.txt** é usado para recuperar filmes que foram classificados.

   * O **ficheiromoviedb.txt** é usado para recuperar os nomes dos filmes.

   * O **recommendations.txt** é usado para recuperar as recomendações do filme para este utilizador.

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

Os trabalhos de Mahout não removem dados temporários que são criados durante o processamento do trabalho. O `--tempDir` parâmetro é especificado no trabalho de exemplo para isolar os ficheiros temporários em um caminho específico para uma fácil eliminação. Para remover os ficheiros temporários, utilize o seguinte comando:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Se quiser voltar a executar o comando, também deve eliminar o diretório de saída. Utilize o seguinte para eliminar este diretório:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu a usar o Mahout, descubra outras formas de trabalhar com dados em HDInsight:

* [Colmeia Apache com HDInsight](hdinsight-use-hive.md)
* [MapReduce com HDInsight](hdinsight-use-mapreduce.md)
