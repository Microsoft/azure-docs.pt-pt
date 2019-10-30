---
title: Gerar recomendações usando o Apache Mahout no Azure HDInsight
description: Saiba como usar a biblioteca de aprendizado de máquina do Apache Mahout para gerar recomendações de filmes com o HDInsight (Hadoop).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 3923abd10fc3a64773d561b1f375f9e2f00a7e56
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044569"
---
# <a name="generate-movie-recommendations-using-apache-mahout-with-apache-hadoop-in-hdinsight-ssh"></a>Gerar recomendações de filme usando o Apache Mahout com o Apache Hadoop no HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Saiba como usar a biblioteca de aprendizado de máquina do [Apache Mahout](https://mahout.apache.org) com o Azure HDInsight para gerar recomendações de filmes.

Mahout é uma biblioteca de [aprendizado de máquina](https://en.wikipedia.org/wiki/Machine_learning) para Apache Hadoop. Mahout contém algoritmos para processamento de dados, como filtragem, classificação e clustering. Neste artigo, você usa um mecanismo de recomendação para gerar recomendações de filmes que se baseiam em filmes que seus amigos viram.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache Hadoop no HDInsight. Consulte [introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="apache-mahout-versioning"></a>Controle de versão do Apache Mahout

Para obter mais informações sobre a versão do Mahout no HDInsight, consulte [versões do hdinsight e componentes do Apache Hadoop](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Noções básicas sobre recomendações

Uma das funções fornecidas pelo Mahout é um mecanismo de recomendação. Esse mecanismo aceita dados no formato de `userID`, `itemId`e `prefValue` (a preferência para o item). O Mahout pode então executar uma análise de coocorrência para determinar: *os usuários que têm uma preferência para um item também têm uma preferência para esses outros itens*. Mahout, em seguida, determina os usuários com as preferências de itens semelhantes, que podem ser usadas para fazer recomendações.

O fluxo de trabalho a seguir é um exemplo simplificado que usa dados de filme:

* **Coocorrência**: Joe, Alice e Bob gostaram de *conflitos de estrela*, *as Empires são voltadas*e *retornam o Jedi*. Mahout determina que os usuários que gostam de qualquer um desses filmes também gostam de outros dois.

* **Coocorrência**: Bob e Alice também gostaram *do fantasma ameaça*, *ataque dos clones*e *vingança do Sith*. Mahout determina que os usuários que curtiram os três filmes anteriores também gostam desses três filmes.

* **Recomendação de similaridade**: como Joe gostou dos três primeiros filmes, Mahout examina filmes que outras pessoas com preferências semelhantes curtiram, mas Joe não observou (curtido/classificado). Nesse caso, o Mahout recomenda *o ameaça fantasma*, *o ataque dos clones*e a *vingança do Sith*.

### <a name="understanding-the-data"></a>Noções básicas sobre os dados

Convenientemente, a [pesquisa do GroupLens](https://grouplens.org/datasets/movielens/) fornece dados de classificação para filmes em um formato compatível com o Mahout. Esses dados estão disponíveis no armazenamento padrão do seu cluster em `/HdiSamples/HdiSamples/MahoutMovieData`.

Há dois arquivos, `moviedb.txt` e `user-ratings.txt`. O arquivo de `user-ratings.txt` é usado durante a análise. O `moviedb.txt` é usado para fornecer informações de texto amigáveis ao exibir os resultados.

Os dados contidos em User-ratings. txt têm uma estrutura de `userID`, `movieID`, `userRating`e `timestamp`, que indica o quão alto cada usuário classificou um filme. Aqui está um exemplo dos dados:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Executar a análise

Em uma conexão SSH com o cluster, use o seguinte comando para executar o trabalho de recomendação:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]  
> O trabalho pode levar vários minutos para ser concluído e pode executar vários trabalhos MapReduce.

## <a name="view-the-output"></a>Exibir a saída

1. Quando o trabalho for concluído, use o seguinte comando para exibir a saída gerada:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    A saída aparece da seguinte maneira:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    A primeira coluna é a `userID`. Os valores contidos em ' [' e '] ' são `movieId`:`recommendationScore`.

2. Você pode usar a saída, junto com o MovieDB. txt, para fornecer mais informações sobre as recomendações. Primeiro, copie os arquivos localmente usando os seguintes comandos:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Esse comando copia os dados de saída para um arquivo chamado **Recommendations. txt** no diretório atual, juntamente com os arquivos de dados de filme.

3. Use o comando a seguir para criar um script Python que pesquise nomes de filmes para os dados na saída de recomendações:

    ```bash
    nano show_recommendations.py
    ```

    Quando o editor for aberto, use o seguinte texto como o conteúdo do arquivo:

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

    Pressione **Ctrl-X**, **Y**e, por fim, **Enter** para salvar os dados.

4. Execute o script Python. O comando a seguir pressupõe que você esteja no diretório onde todos os arquivos foram baixados:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Este comando examina as recomendações geradas para a ID de usuário 4.

   * O arquivo **User-ratings. txt** é usado para recuperar filmes que foram classificados.

   * O arquivo **MovieDB. txt** é usado para recuperar os nomes dos filmes.

   * O **Recommendations. txt** é usado para recuperar as recomendações de filme para este usuário.

     A saída desse comando é semelhante ao seguinte texto:

       Sete anos em Tibet (1997), Score = 5.0 Indiana Jones e a última Crusade (1989), Score = 5,0 Jaws (1975), Score = 5,0 Sense e Sensibility (1995), pontuação = 5.0 dia da independência (ID4) (1996), pontuação = 5.0 meu melhor casamento (1997), pontuação = 5.0 Jerry Maguire (1996), Score = 5.0 Scream 2 (1997), pontuação = 5.0 tempo para eliminar, A (1996), pontuação = 5.0

## <a name="delete-temporary-data"></a>Excluir dados temporários

Os trabalhos do Mahout não removem dados temporários que são criados durante o processamento do trabalho. O parâmetro `--tempDir` é especificado no trabalho de exemplo para isolar os arquivos temporários em um caminho específico para facilitar a exclusão. Para remover os arquivos temporários, use o seguinte comando:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Se você quiser executar o comando novamente, também deverá excluir o diretório de saída. Use o seguinte para excluir este diretório:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a usar o Mahout, descubra outras maneiras de trabalhar com dados no HDInsight:

* [Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Apache Pig com HDInsight](hdinsight-use-pig.md)
* [MapReduce com HDInsight](hdinsight-use-mapreduce.md)