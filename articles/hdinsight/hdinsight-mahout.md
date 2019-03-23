---
title: Gerar recomendações com o Mahout HDInsight do PowerShell - Azure
description: Saiba como utilizar a biblioteca de aprendizagem automática de Apache Mahout para gerar recomendações de filmes com o HDInsight (Hadoop) a partir de um script do PowerShell em execução no seu cliente.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: df8e15bbde5ad60d2e5cb90ba37892c135070f41
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359301"
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-apache-hadoop-in-hdinsight-powershell"></a>Gerar recomendações de filmes com o Apache Mahout com o Apache Hadoop no HDInsight (PowerShell)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Saiba como utilizar o [Apache Mahout](https://mahout.apache.org) biblioteca de machine learning com o Azure HDInsight para gerar recomendações de filmes. O exemplo neste documento utiliza o Azure PowerShell para executar tarefas de Mahout.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Um cluster do HDInsight baseado em Linux. Para obter informações sobre como criar um, consulte [começar a utilizar o Hadoop baseado em Linux no HDInsight][getstarted].

    > [!IMPORTANT]  
    > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* [Azure PowerShell](/powershell/azure/overview)

## <a name="recommendations"></a>Gerar recomendações com o Azure PowerShell

> [!WARNING]  
> A tarefa nesta secção funciona com o Azure PowerShell. Muitas das classes fornecidas com o Mahout não trabalhar com o Azure PowerShell. Para obter uma lista de classes que não funcionam com o Azure PowerShell, consulte a [resolução de problemas](#troubleshooting) secção.
>
> Para obter um exemplo de utilização do SSH para ligar ao HDInsight e executados exemplos de Mahout diretamente no cluster, consulte [gerar recomendações de filmes com o Apache Mahout e HDInsight (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md).

Uma das funções que é fornecida pelo Mahout é um motor de recomendação. Esse mecanismo aceita dados no formato `userID`, `itemId`, e `prefValue` (a preferência dos usuários para o item). Mahout utiliza os dados para determinar os utilizadores com as preferências do item de tipo, que podem ser utilizados para fazer recomendações.

O exemplo seguinte é um passo a passo simplificada de como funciona o processo de recomendação:

* **co-occurrence**: Joe, Alice e Bob, todos os gostos *estrela personagens da guerra*, *The império Strikes volta*, e *retorno do Jedi*. Mahout determina que os utilizadores que, como qualquer um desses filmes também como os outros dois.

* **co-occurrence**: Também gostaram BOB e Alice *o fantasma Menace*, *ataque dos Clones*, e *Revenge do Sith*. Mahout determina que os utilizadores que gostaram três filmes anteriores também como esses filmes.

* **Recomendação de semelhança**: Uma vez que o João gostou os primeiros três filmes, Mahout analisa filmes que outras pessoas com as preferências semelhante que gostou, mas Joe não já viu (gostou/classificados). Neste caso, recomenda a Mahout *o fantasma Menace*, *ataque dos Clones*, e *Revenge do Sith*.

### <a name="understanding-the-data"></a>Compreender os dados

[Pesquisa de GroupLens] [ movielens] fornece dados de classificação de filmes num formato compatível com o Mahout. Estes dados estão disponíveis no armazenamento padrão para o seu cluster em `/HdiSamples/HdiSamples/MahoutMovieData`.

Existem dois ficheiros, `moviedb.txt` (informações sobre os filmes) e `user-ratings.txt`. O `user-ratings.txt` ficheiro é utilizado durante a análise. O `moviedb.txt` ficheiro é utilizado para fornecer um texto amigável ao exibir os resultados da análise.

Os dados contidos no utilizador ratings.txt tem uma estrutura de `userID`, `movieID`, `userRating`, e `timestamp`, que informa como cada utilizador classificada um filme. Eis um exemplo dos dados:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>Executar a tarefa

Utilize o seguinte script do Windows PowerShell para executar uma tarefa que utiliza o motor de recomendação Manhout com os dados do filme:

> [!NOTE]  
> Este ficheiro pede-lhe informações que são utilizadas para ligar ao cluster do HDInsight e executar tarefas. Pode demorar vários minutos das tarefas a concluir e transfira o ficheiro de output.txt.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

> [!NOTE]  
> Mahout tarefas remove os dados temporários que são criados ao processar a tarefa. O `--tempDir` parâmetro for especificado na tarefa de exemplo para isolar os ficheiros temporários para um diretório específico.

A tarefa de Mahout não devolve o resultado para STDOUT. Em vez disso, ele armazena-a no diretório de saída especificado como **parte-r-00000**. O script transfere esse arquivo para **output.txt** no diretório atual na estação de trabalho.

O texto seguinte é um exemplo do conteúdo deste ficheiro:

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

A primeira coluna é o `userID`. Os valores contidos em ' [' e ']' são `movieId`:`recommendationScore`.

O script também transfere os `moviedb.txt` e `user-ratings.txt` arquivos, que são necessárias para formatar a saída para ser mais legível.

### <a name="view-the-output"></a>Ver o resultado

Embora o resultado gerado pode ser OK para uso num aplicativo, não é fácil de utilizar. O `moviedb.txt` do servidor de podem ser utilizados para resolver o `movieId` para um nome do filme. Utilize o seguinte script do PowerShell para apresentar as recomendações com nomes de filme:

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

Utilize o seguinte comando para apresentar as recomendações num formato amigável: 

```powershell
.\show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt
```

O resultado é semelhante ao seguinte texto:

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237

## <a name="troubleshooting"></a>Resolução de Problemas

### <a name="cannot-overwrite-files"></a>Não é possível substituir ficheiros

Mahout tarefas faça, não limpe os ficheiros temporários criados durante o processamento. Além disso, as tarefas não substituir o ficheiro de saída existente.

Para evitar erros durante a execução de trabalhos de Mahout, elimine ficheiros temporários e de saída entre as execuções. Para remover os ficheiros criados pelos scripts do anteriores neste documento, utilize o seguinte script do PowerShell:

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard,
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="nopowershell"></a>Classes que não funcionam com o Azure PowerShell

Tarefas de mahout que utilizam as seguintes classes devolvem várias mensagens de erro quando utilizada a partir do Windows PowerShell:

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Para executar as tarefas que utilizem essas classes, ligue ao cluster do HDInsight através de SSH e executar as tarefas a partir da linha de comando. Para obter um exemplo de utilização do SSH para executar tarefas de Mahout, consulte [gerar recomendações de filmes com o Apache Mahout e HDInsight (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md).

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como utilizar o Apache Mahout, descobrir outras formas de trabalhar com dados no HDInsight:

* [Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

[build]: https://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: https://grouplens.org/datasets/movielens/
[100k]: https://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: https://en.wikipedia.org/wiki/Machine_learning
[forest]: https://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
