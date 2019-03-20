---
title: Gerar recomendações com o Apache Mahout e HDInsight (SSH) - Azure
description: Saiba como utilizar a biblioteca de aprendizagem automática de Apache Mahout para gerar recomendações de filmes com o HDInsight (Hadoop).
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: 6e27d00e4b12ade82cfde6b3a4927edc7d69798a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58075821"
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-apache-hadoop-in-hdinsight-ssh"></a>Gerar recomendações de filmes com o Apache Mahout com baseado em Linux Apache Hadoop no HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Saiba como utilizar o [Apache Mahout](https://mahout.apache.org) biblioteca de machine learning com o Azure HDInsight para gerar recomendações de filmes.

O manhout é uma [machine learning] [ ml] biblioteca para o Apache Hadoop. Mahout contém algoritmos para processamento de dados, como filtragem, classificação e clustering. Neste artigo, vai utilizar um motor de recomendação para gerar recomendações de filmes que se baseiam em filmes que viu seus amigos.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do HDInsight baseado em Linux. Para obter informações sobre como criar um, consulte [começar a utilizar o Hadoop baseado em Linux no HDInsight][getstarted].

> [!IMPORTANT]  
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Um cliente SSH. Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="apache-mahout-versioning"></a>Controle de versão do Apache Mahout

Para obter mais informações sobre a versão do Mahout no HDInsight, consulte [HDInsight versões e componentes do Apache Hadoop](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Recomendações de compreensão

Uma das funções que é fornecida pelo Mahout é um motor de recomendação. Esse mecanismo aceita dados no formato `userID`, `itemId`, e `prefValue` (a preferência para o item). Mahout, em seguida, pode realizar a análise de ocorrência conjunta para determinar: *os utilizadores que têm uma preferência de um item tem também uma preferência para estes outros itens*. Mahout, em seguida, determina os utilizadores com as preferências do item de tipo, que podem ser utilizados para fazer recomendações.

O seguinte fluxo de trabalho é um exemplo simplificado que utiliza dados do filme:

* **Ocorrência conjunta**: Joe, Alice e Bob, todos os gostos *estrela personagens da guerra*, *The império Strikes volta*, e *retorno do Jedi*. Mahout determina que os utilizadores que, como qualquer um desses filmes também como os outros dois.

* **Ocorrência conjunta**: Também gostaram BOB e Alice *o fantasma Menace*, *ataque dos Clones*, e *Revenge do Sith*. Mahout determina que os utilizadores que gostaram três filmes anteriores também como estes três filmes.

* **Recomendação de semelhança**: Uma vez que o João gostou os primeiros três filmes, Mahout analisa filmes que outras pessoas com as preferências semelhante que gostou, mas Joe não já viu (gostou/classificados). Neste caso, recomenda a Mahout *o fantasma Menace*, *ataque dos Clones*, e *Revenge do Sith*.

### <a name="understanding-the-data"></a>Compreender os dados

Convenientemente, [GroupLens Research] [ movielens] fornece dados de classificação de filmes num formato compatível com o Mahout. Estes dados estão disponíveis no armazenamento de predefinido do seu cluster em `/HdiSamples/HdiSamples/MahoutMovieData`.

Existem dois ficheiros, `moviedb.txt` e `user-ratings.txt`. O `user-ratings.txt` ficheiro é utilizado durante a análise. O `moviedb.txt` é utilizado para fornecer informações de um texto amigável ao visualizar os resultados.

Os dados contidos no utilizador ratings.txt tem uma estrutura de `userID`, `movieID`, `userRating`, e `timestamp`, que indica a forma como cada utilizador classificada um filme. Eis um exemplo dos dados:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Executar a análise

A partir de uma ligação SSH ao cluster, utilize o seguinte comando para executar a tarefa de recomendação:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]  
> A tarefa pode demorar vários minutos a concluir e pode executar várias tarefas de MapReduce.

## <a name="view-the-output"></a>Ver o resultado

1. Depois de concluída a tarefa, utilize o seguinte comando para ver o resultado gerado:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    A saída será assim:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    A primeira coluna é o `userID`. Os valores contidos em ' [' e ']' são `movieId`:`recommendationScore`.

2. Pode utilizar a saída, juntamente com o moviedb.txt, para fornecer mais informações sobre as recomendações. Em primeiro lugar, copie os ficheiros localmente, utilizando os seguintes comandos:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Este comando copia os dados de saída para um ficheiro denominado **recommendations.txt** no diretório atual, juntamente com os arquivos de dados do filme.

3. Utilize o seguinte comando para criar um script de Python que procura os nomes de filme para os dados na saída recomendações:

    ```bash
    nano show_recommendations.py
    ```

    Quando o editor estiver aberto, utilize o seguinte texto como o conteúdo do ficheiro:

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

    Prima **Ctrl-X**, **Y**e finalmente **Enter** para guardar os dados.

4. Execute o script de Python. O seguinte comando pressupõe que esteja no diretório em que todos os ficheiros foram transferidos:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Este comando analisa as recomendações geradas para 4 de ID de utilizador.

   * O **ratings.txt utilizador** arquivo é usado para recuperar a filmes que estejam classificados.

   * O **moviedb.txt** arquivo é usado para recuperar os nomes dos filmes.

   * O **recommendations.txt** é usada para recuperar as recomendações de filmes para este utilizador.

     O resultado deste comando é semelhante ao seguinte texto:

       Pontuação de sete anos na Tibet (1997), = 5.0 Indiana Jones e o último Crusade (1989), pontuação = 5.0 Jaws (1975), a pontuação = 5.0 sentido e Sensibility (1995), a pontuação = 5.0 independência de dia (ID4) (1996), pontuação = 5.0 do meu amigo melhor Wedding (1997), a pontuação = 5.0 Jerry Maguire (1996), pontuação = 5.0 Scream 2 (1997), pontuação = 5.0 tempo para eliminação, um (1996), pontuação = 5.0

## <a name="delete-temporary-data"></a>Eliminar dados temporários

Mahout tarefas remove os dados temporários que são criados ao processar a tarefa. O `--tempDir` parâmetro for especificado na tarefa de exemplo para isolar os ficheiros temporários num caminho específico para eliminação fácil. Para remover os arquivos temporários, utilize o seguinte comando:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Se quiser executar o comando novamente, também tem de eliminar o diretório de saída. Utilize o seguinte para eliminar este diretório:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como utilizar o Mahout, descobrir outras formas de trabalhar com dados no HDInsight:

* [Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Apache Pig com o HDInsight](hdinsight-use-pig.md)
* [MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

[build]: https://mahout.apache.org/developers/buildingmahout.html
[movielens]: https://grouplens.org/datasets/movielens/
[100k]: https://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: https://en.wikipedia.org/wiki/Machine_learning
[forest]: https://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
