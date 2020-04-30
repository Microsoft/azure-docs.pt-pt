---
title: 'Quickstart: RStudio Server & ML Services for R - Azure HDInsight'
description: No arranque rápido, executa um script R num cluster de Serviços ML em Azure HDInsight utilizando o RStudio Server.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 8a6a204ee5080e3acf99c13ecba1e1c7664d68b4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73241894"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-rstudio-server"></a>Quickstart: Execute um script R num cluster de serviços ML em Azure HDInsight usando o RStudio Server

Os serviços ML no Azure HDInsight permitem que os scripts R utilizem apache Spark e Apache Hadoop MapReduce para executar computações distribuídas. Os Serviços ML controlam a forma como as chamadas são executadas definindo o contexto do cálculo. O nó de borda de um cluster fornece um lugar conveniente para ligar ao cluster e executar os seus scripts R. Com um nó de borda, tem a opção de executar as funções distribuídas paralelas do RevoScaleR através dos núcleos do servidor do nó de borda. Também pode executá-los através dos nós do cluster utilizando os contextos de cálculo do Mapa hadoop do RevoScaleR ou da computação Apache Spark.

Neste arranque rápido, aprende-se a executar um script R com o RStudio Server que demonstra o uso da Spark para computações R distribuídas. Definirá um contexto computacional para realizar computações localmente num nó de borda, e novamente distribuído pelos nós do cluster HDInsight.

## <a name="prerequisite"></a>Pré-requisito

Um cluster de serviços ML no HDInsight. Consulte os [clusters De Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **ml Services** for **Cluster type**.

## <a name="connect-to-rstudio-server"></a>Ligar ao RStudio Server

RStudio Server corre no nó de borda do cluster. Vá ao seguinte `CLUSTERNAME` URL onde está o nome do cluster ml Services que criou:

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

A primeira vez que se inscreve tem de autenticar duas vezes. Para o primeiro pedido de autenticação, forneça o login e a senha do cluster Admin, predefinido é `admin`. Para o segundo pedido de autenticação, forneça o login sSH e a palavra-passe, predefinido é `sshuser`. Os subsequentes inscrições requerem apenas as credenciais SSH.

Assim que estiver ligado, o ecrã deve assemelhar-se à captura de ecrã seguinte:

![Vistas gerais da consola do estúdio R](./media/ml-services-quickstart-job-rstudio/connect-to-r-studio1.png)

## <a name="use-a-compute-context"></a>Utilizar um contexto de cálculo

1. A partir do RStudio Server, utilize o seguinte código para carregar dados de exemplo no armazenamento predefinido para hDInsight:

    ```RStudio
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

    Este passo pode demorar cerca de 8 minutos a ser concluído.

1. Crie algumas informações de dados e defina duas fontes de dados. Introduza o seguinte código no RStudio:

    ```RStudio
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

1. Executar uma regressão logística sobre os dados usando o contexto **computacional local.** Introduza o seguinte código no RStudio:

    ```RStudio
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    Os cálculos devem estar concluídos em cerca de 7 minutos. Deve ver saída que termina com linhas semelhantes às seguintes:

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

1. Executar a mesma regressão logística usando o contexto **Spark.** O contexto do Spark distribui o processamento por todos os nós de trabalho do cluster do HDInsight. Introduza o seguinte código no RStudio:

    ```RStudio
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

    Os cálculos devem estar concluídos em cerca de 5 minutos.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de completar o arranque rápido, poderá querer eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para eliminar um cluster, consulte [Eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a executar um script R com o RStudio Server que demonstrou usar a Spark para computações R distribuídas.  Avance para o próximo artigo para saber as opções disponíveis para especificar se e como a execução é paralelizada entre os núcleos do nó de borda ou do cluster HDInsight.

> [!div class="nextstepaction"]
>[Opções de contexto computacional para serviços ML no HDInsight](./r-server-compute-contexts.md)

> [!NOTE]
> Esta página descreve funcionalidades do software RStudio. O Microsoft Azure HDInsight não está afiliado à RStudio, Inc.
