---
title: 'Quickstart: RStudio Server & ML Services for R - Azure HDInsight'
description: No arranque rápido, executa um script R num cluster ML Services em Azure HDInsight usando o RStudio Server.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.custom: mvc
ms.openlocfilehash: 7604a327c43ed1942d336399b6d8b9dbb1e64d75
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935713"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-rstudio-server"></a>Quickstart: Execute um script R num cluster ML Services em Azure HDInsight usando O Servidor RStudio

ML Services on Azure HDInsight permite que scripts R utilizem Apache Spark e Apache Hadoop MapReduce para executar computações distribuídas. A ML Services controla a forma como as chamadas são executadas definindo o contexto de computação. O nó de borda de um cluster fornece um lugar conveniente para se conectar ao cluster e executar os seus scripts R. Com um nó de borda, você tem a opção de executar as funções distribuídas paralelas de RevoScaleR através dos núcleos do servidor de nó de borda. Também pode executá-los através dos nós do cluster, utilizando os contextos de cálculo Hadoop Do RevoScaleR ou apache spark.

Neste quickstart, aprende-se a executar um script R com O Servidor RStudio que demonstra a utilização do Spark para computações R distribuídas. Você definirá um contexto computacional para executar computações localmente em um nó de borda, e novamente distribuído através dos nós no cluster HDInsight.

## <a name="prerequisite"></a>Pré-requisito

Um cluster ML Services em HDInsight. Consulte [os clusters Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **serviços ML** para **o tipo cluster**.

## <a name="connect-to-rstudio-server"></a>Ligar ao RStudio Server

O RStudio Server funciona no nó de borda do cluster. Vá para o seguinte URL onde `CLUSTERNAME` está o nome do cluster ML Services que criou:

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

A primeira vez que assinar, precisa de autenticar duas vezes. Para a primeira indicação de autenticação, forneça o login e a palavra-passe do cluster Admin, o padrão é `admin` . Para a segunda instrução de autenticação, forneça o login SSH e a palavra-passe, o padrão é `sshuser` . As inscrições subsequentes só requerem as credenciais SSH.

Assim que estiver ligado, o ecrã deve assemelhar-se à captura de ecrã seguinte:

![Visão geral da consola web do estúdio R](./media/ml-services-quickstart-job-rstudio/connect-to-r-studio1.png)

## <a name="use-a-compute-context"></a>Utilizar um contexto de cálculo

1. A partir do RStudio Server, utilize o seguinte código para carregar dados de exemplo no armazenamento predefinido para HDInsight:

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

1. Executar uma regressão logística sobre os dados usando o contexto computecional **local.** Introduza o seguinte código no RStudio:

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

    Os cálculos devem estar completos em cerca de 7 minutos. Deve ver saída que termina com linhas semelhantes ao seguinte corte:

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

    Os cálculos devem estar completos em cerca de 5 minutos.

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de completar o arranque rápido, é possível que queira eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para eliminar um cluster, consulte [Eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a executar um script R com O Servidor RStudio que demonstrou usar o Spark para computações R distribuídas.  Avance para o próximo artigo para saber as opções disponíveis para especificar se e como a execução é paralelamente através de núcleos do nó de borda ou cluster HDInsight.

> [!div class="nextstepaction"]
>[Opções de contexto computacional para serviços ML em HDInsight](./r-server-compute-contexts.md)

> [!NOTE]
> Esta página descreve funcionalidades do software RStudio. O Microsoft Azure HDInsight não está filiado à RStudio, Inc.
