---
title: 'Início rápido: Executar um script R num cluster no HDInsight do Azure utilizando a consola de R Services de ML'
description: Guia de introdução, que executa um script R num cluster de serviços de ML no HDInsight do Azure utilizando a consola de R.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 682ee4f44dcdd2619668645fa7a8aa22cb645273
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450927"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-r-console"></a>Início rápido: Executar um script R num cluster no HDInsight do Azure utilizando a consola de R Services de ML

Serviços de ML no Azure HDInsight permite que os scripts R utilizem o Apache Spark e Apache Hadoop MapReduce para executar cálculos distribuídos. Serviços de ML controla como as chamadas são executadas ao definir o contexto de cálculo. Nó de extremidade de um cluster fornece um local conveniente para ligar ao cluster e para executar os scripts R. Com um nó de extremidade, tem a opção de executar as funções em paralelo distribuídas de RevoScaleR entre os núcleos do servidor de nó edge. Também pode executá-los em todos os nós do cluster através da utilização Hadoop mapeamento redução do RevoScaleR ou contextos de computação do Apache Spark.

Neste início rápido, irá aprender a executar um script R com a consola de R que demonstra a utilização do Spark para cálculos de R distribuídos. Definir o contexto de cálculo para efetuar cálculos localmente num nó de extremidade e, novamente distribuída por nós do cluster do HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de serviços de ML no HDInsight. Ver [Apache Hadoop criar clusters no portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **serviços ML** para **tipo de Cluster**.

* Um cliente SSH. Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="connect-to-r-console"></a>Ligar à consola de R

1. Ligar ao nó de extremidade de um cluster do HDInsight de serviços de ML através de SSH. Editar o comando abaixo, substituindo `CLUSTERNAME` com o nome do cluster e, em seguida, introduza o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

1. Na sessão SSH, utilize o comando seguinte para iniciar a consola do R:

    ```
    R
    ```

    Deverá ver um resultado com a versão dos Serviços ML, além de outras informações.


## <a name="use-a-compute-context"></a>Utilizar um contexto de cálculo

1. Na linha de comandos `>`, pode introduzir o código R. Utilize o seguinte código para carregar dados de exemplo para o armazenamento predefinido do HDInsight:

    ```R
    # Set the HDFS (WASB) location of example data
     bigDataDirRoot <- "/example/data"
    
     # create a local folder for storing data temporarily
     source <- "/tmp/AirOnTimeCSV2012"
     dir.create(source)
    
     # Download data to the tmp folder
     remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
     download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
     download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
     download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
     download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
     download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
     download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
     download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
     download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
     download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
     download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
     download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
     download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
    
     # Set directory in bigDataDirRoot to load the data into
     inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")
    
     # Make the directory
     rxHadoopMakeDir(inputDir)
    
     # Copy the data from source to input
     rxHadoopCopyFromLocal(source, bigDataDirRoot)
    ```

    Este passo pode demorar cerca de 10 minutos a concluir.

1. Criar algumas informações de dados e definir duas origens de dados. Introduza o código seguinte na consola do R:

    ```R
    # Define the HDFS (WASB) file system
     hdfsFS <- RxHdfsFileSystem()
    
     # Create info list for the airline data
     airlineColInfo <- list(
          DAY_OF_WEEK = list(type = "factor"),
          ORIGIN = list(type = "factor"),
          DEST = list(type = "factor"),
          DEP_TIME = list(type = "integer"),
          ARR_DEL15 = list(type = "logical"))
    
     # get all the column names
     varNames <- names(airlineColInfo)
    
     # Define the text data source in hdfs
     airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
    
     # Define the text data source in local system
     airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)
    
     # formula to use
     formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"
    ```

1. Executar um regressão logística sobre os dados com o **local** contexto de cálculo. Introduza o código seguinte na consola do R:

    ```R
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    As computações devem demorar cerca de 7 minutos. Deverá ver um resultado que acaba com linhas semelhantes ao seguinte fragmento:

    ```output
    Data: airOnTimeDataLocal (RxTextData Data Source)
     File name: /tmp/AirOnTimeCSV2012
     Dependent variable(s): ARR_DEL15
     Total independent variables: 634 (Including number dropped: 3)
     Number of valid observations: 6005381
     Number of missing observations: 91381
     -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)
    
     Coefficients:
                      Estimate Std. Error z value Pr(>|z|)
      (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
      ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
      ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
      ......
      DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
      DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
      DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
      DEST=BPT         Dropped    Dropped Dropped  Dropped
    
      ---
    
      Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
      Condition number of final variance-covariance matrix: 11904202
      Number of iterations: 7
    ```

1. Executar a mesma regressão logística com o **Spark** contexto. O contexto do Spark distribui o processamento por todos os nós de trabalho do cluster do HDInsight. Introduza o código seguinte na consola do R:

    ```R
    # Define the Spark compute context
     mySparkCluster <- RxSpark()
    
     # Set the compute context
     rxSetComputeContext(mySparkCluster)
    
     # Run a logistic regression
     system.time(  
        modelSpark <- rxLogit(formula, data = airOnTimeData)
     )
    
     # Display a summary
     summary(modelSpark)
    ```

    As computações devem demorar cerca de 5 minutos.

1. Para sair da consola do R, utilize o seguinte comando:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o início rápido, pode querer eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para eliminar um cluster, veja [eliminar um cluster do HDInsight com o seu browser, o PowerShell ou a CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a executar um script R com a consola de R que demonstram a utilização do Spark para cálculos de R distribuídos.  Avance para o artigo seguinte para saber as opções que estão disponíveis para especificar se e como a execução é paralelizada entre os núcleos do nó de extremidade ou cluster do HDInsight.

> [!div class="nextstepaction"]
>[Opções de contexto para os serviços de ML no HDInsight de computação](./r-server-compute-contexts.md)