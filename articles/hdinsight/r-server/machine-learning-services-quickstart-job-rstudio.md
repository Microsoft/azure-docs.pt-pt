---
title: 'Início rápido: Servidor RStudio para executar R em serviços de ML – Azure HDInsight'
description: No início rápido, você executa um script R em um cluster de serviços do ML no Azure HDInsight usando o servidor RStudio.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 6d87e46b522bb7c7e55c711194cc6456fcd89e92
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736216"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-rstudio-server"></a>Início rápido: Executar um script do R em um cluster de serviços de ML no Azure HDInsight usando o servidor RStudio

Os serviços de ML no Azure HDInsight permitem que os scripts R usem Apache Spark e Apache Hadoop MapReduce para executar cálculos distribuídos. Os serviços de ML controlam como as chamadas são executadas definindo o contexto de computação. O nó de borda de um cluster fornece um local conveniente para se conectar ao cluster e executar os scripts do R. Com um nó de borda, você tem a opção de executar as funções distribuídas paralelizadas do RevoScaleR nos núcleos do servidor do nó de borda. Você também pode executá-los em todos os nós do cluster usando o mapa do Hadoop do RevoScaleR para reduzir ou Apache Spark contextos de computação.

Neste guia de início rápido, você aprende a executar um script R com o RStudio Server que demonstra o uso do Spark para cálculos de R distribuídos. Você definirá um contexto de computação para executar cálculos localmente em um nó de borda e será distribuído novamente entre os nós no cluster HDInsight.

## <a name="prerequisite"></a>Pré-requisito

Um cluster de serviços do ML no HDInsight. Consulte [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Serviços ml** para o **tipo de cluster**.

## <a name="connect-to-rstudio-server"></a>Ligar ao RStudio Server

O servidor RStudio é executado no nó de borda do cluster. Vá para a URL a seguir `CLUSTERNAME` , em que é o nome do cluster de serviços ml que você criou:

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Na primeira vez que você entrar, precisará autenticar duas vezes. Para o primeiro prompt de autenticação, forneça o logon de administrador do cluster e a `admin`senha, o padrão é. Para o segundo prompt de autenticação, forneça o logon e a senha do SSH `sshuser`, o padrão é. As entradas subsequentes exigem apenas as credenciais SSH.

Assim que estiver ligado, o ecrã deve assemelhar-se à captura de ecrã seguinte:

![Detalhes básicos do cluster](./media/ml-services-quickstart-job-rstudio/connect-to-r-studio.png)

## <a name="use-a-compute-context"></a>Utilizar um contexto de cálculo

1. No servidor RStudio, use o seguinte código para carregar dados de exemplo no armazenamento padrão do HDInsight:

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

    Esta etapa pode levar cerca de 8 minutos para ser concluída.

1. Crie algumas informações de dados e defina duas fontes de dados. Insira o seguinte código em RStudio:

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

1. Execute uma regressão logística sobre os dados usando o contexto de computação **local** . Insira o seguinte código em RStudio:

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

    Os cálculos devem ser concluídos em aproximadamente 7 minutos. Você deve ver a saída que termina com linhas semelhantes ao trecho a seguir:

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

1. Execute a mesma regressão logística usando o contexto do **Spark** . O contexto do Spark distribui o processamento por todos os nós de trabalho do cluster do HDInsight. Insira o seguinte código em RStudio:

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

    Os cálculos devem ser concluídos em cerca de 5 minutos.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o início rápido, talvez você queira excluir o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para excluir um cluster, consulte [excluir um cluster HDInsight usando seu navegador, o PowerShell ou o CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a executar um script R com o RStudio Server, que demonstrou usar o Spark para cálculos de R distribuídos.  Avance para o próximo artigo para aprender as opções disponíveis para especificar se e como a execução é paralelizada entre os núcleos do nó de borda ou do cluster HDInsight.

> [!div class="nextstepaction"]
>[Opções de contexto de computação para serviços de ML no HDInsight](./r-server-compute-contexts.md)

> [!NOTE]
> Esta página descreve os recursos do RStudio software. Microsoft Azure HDInsight não é afiliado com RStudio, Inc.
