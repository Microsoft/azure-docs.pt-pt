---
title: 'Tutorial: Utilize R no Spark computação contexto no Azure HDInsight'
description: Tutorial - introdução ao R e Spark num cluster HDInsight do Azure Machine Learning services.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: f072b6905881da7b7854b0e51d690dbbd40dffb5
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227431"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Tutorial: Utilize R no Spark computação contexto no Azure HDInsight

Este tutorial disponibiliza uma introdução passo a passo para utilizar as funções de R no Apache Spark, que são executados num cluster HDInsight do Azure Machine Learning services.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Transferir os dados de exemplo para o armazenamento local
> * Copiar os dados para o armazenamento predefinido
> * Configurar um conjunto de dados
> * Criar origens de dados
> * Criar um contexto de cálculo para Spark
> * Ajustar um modelo linear
> * Utilizar ficheiros XDF compostos
> * Converter XDF para CSV

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de serviços de Machine Learning do Azure HDInsight. Aceda a [clusters do Apache Hadoop criar com o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e, para **tipo de Cluster**, selecione **serviços ML**.

## <a name="connect-to-rstudio-server"></a>Ligar ao RStudio Server

RStudio Server é executado no nó de extremidade do cluster. Vá para o site seguinte (em que *CLUSTERNAME* na URL é o nome do cluster de serviços de aprendizagem do HDInsight que criou):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Na primeira vez que iniciar sessão, se autenticar duas vezes. Na primeiro aviso de autenticação, indique o nome de utilizador de administrador de cluster e a palavra-passe (a predefinição é *administrador*). No prompt de autenticação de segundo, forneça o nome de utilizador SSH e a palavra-passe (a predefinição é *sshuser*). Inícios de sessão subsequentes necessitam apenas das credenciais SSH.

## <a name="download-the-sample-data-to-local-storage"></a>Transferir os dados de exemplo para o armazenamento local

O *companhia aérea 2012 conjunto de dados de forma atempada* consiste em 12 ficheiros separados por vírgulas que contêm detalhes de chegada e de saída de voo para todos os voos comerciais nos E.U.A. para o ano 2012. Este conjunto de dados é grande, com mais de 6 milhões observações.

1. Inicialize algumas variáveis de ambiente. Na consola do RStudio Server, introduza o seguinte código:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. No painel direito, selecione o **ambiente** separador. As variáveis são apresentadas sob **valores**.

    ![RStudio](./media/ml-services-tutorial-spark-compute/rstudio.png)

1.  Crie um diretório local e transferir os dados de exemplo. No RStudio, introduza o seguinte código:

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    O download deve ser concluído em cerca de 9,5 minutos.

## <a name="copy-the-data-to-default-storage"></a>Copiar os dados para o armazenamento predefinido

A localização de Hadoop Distributed File System (HDFS) é especificada com o `airDataDir` variável. No RStudio, introduza o seguinte código:

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

A etapa deve ser concluída em aproximadamente 10 segundos.

## <a name="set-up-a-dataset"></a>Configurar um conjunto de dados

1. Crie um objeto de sistema de ficheiros que utiliza os valores predefinidos. No RStudio, introduza o seguinte código:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Como os arquivos CSV originais têm nomes de variáveis complicados em vez disso, fornecer uma *colInfo* lista para que fiquem mais fáceis de gerir. No RStudio, introduza o seguinte código:

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>Criar origens de dados

Num contexto de computação Spark, pode criar origens de dados utilizando as seguintes funções:

|Função | Descrição |
|---------|-------------|
|`RxTextData` | Uma origem de dados de texto delimitado por vírgulas. |
|`RxXdfData` | Dados no formato de ficheiro de dados XDF. No RevoScaleR, o formato de arquivo XDF é modificado para o Hadoop armazenar dados num conjunto composto de arquivos em vez de um único arquivo. |
|`RxHiveData` | Gera um objeto de origem de dados do Hive.|
|`RxParquetData` | Gera um objeto de origem de dados de Parquet.|
|`RxOrcData` | Gera um objeto de origem de dados de Orc.|

Criar uma [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) objeto utilizando os ficheiros que copiou no HDFS. No RStudio, introduza o seguinte código:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Criar um contexto de cálculo para Spark

Para carregar dados e executar análises em nós de trabalho, defina o contexto de cálculo no script para [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). Neste contexto, as funções de R automaticamente distribuem a carga de trabalho em todos os nós de trabalho, nenhum requisito incorporado para o gerenciamento de tarefas ou a fila. O contexto de cálculo do Spark é estabelecido através de `RxSpark` ou `rxSparkConnect()` para criar contexto de cálculo do Spark e utiliza `rxSparkDisconnect()` para retornar ao contexto de cálculo local. No RStudio, introduza o seguinte código:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Ajustar um modelo linear

1. Utilize o [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) funcionar de acordo com um modelo linear com sua `airDS` origem de dados. No RStudio, introduza o seguinte código:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Este passo deve ser concluído em 2 a 3 minutos.

1. Veja os resultados. No RStudio, introduza o seguinte código:

    ```R
    summary(delayArr)
    ```

    Deverá ver os seguintes resultados:

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    Os resultados indicam que já processados todos os dados, observações de 6 milhões, com todos os ficheiros CSV no diretório especificado. Uma vez que especificar `cube = TRUE`, tem um coeficiente estimada para cada dia da semana (e não a interceptação).

## <a name="use-composite-xdf-files"></a>Utilizar ficheiros XDF compostos

Como pôde ver, pode analisar os ficheiros CSV diretamente com o R no Hadoop. Mas pode fazer a análise mais rapidamente, se armazenar os dados num formato mais eficiente. O formato de arquivo XDF de R é eficiente, mas ele modificou um pouco para HDFS, para que permaneçam de arquivos individuais dentro de um único bloco HDFS. (O tamanho do bloco HDFS varia de instalação para instalação, mas é normalmente 64MB ou 128 MB.) 

Quando utiliza [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) no Hadoop para criar um conjunto de ficheiros XDF compostos, especifique um `RxTextData` da origem de dados, tais como `AirDS` como o inData e um `RxXdfData` origem de dados com o sistema de ficheiros definido como um sistema de ficheiros HDFS como o outFile argumento. Em seguida, pode utilizar o `RxXdfData` objeto como o argumento de dados em análises subsequentes do R.

1. Definir um `RxXdfData` objeto. No RStudio, introduza o seguinte código:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Definir um tamanho de bloco de 250000 linhas e especifique que Lemos todos os dados. No RStudio, introduza o seguinte código:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importar os dados com `rxImport`. No RStudio, introduza o seguinte código:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Este passo deve ser concluído dentro de alguns minutos.

1. Fazer uma nova estimativa o mesmo modelo linear, utilizar a origem de dados novo e mais rápido. No RStudio, introduza o seguinte código:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    O passo deve ser concluído em menos de um minuto.

1. Veja os resultados. Os resultados devem ser os mesmos, como os ficheiros CSV. No RStudio, introduza o seguinte código:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Converter XDF para CSV

### <a name="in-a-spark-context"></a>Num contexto de Spark

Se tiver convertido os ficheiros CSV para o formato de ficheiro XDF para uma maior eficiência durante a execução de análises, mas agora pretende converter os dados CSV, pode fazer isso usando [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Para criar uma pasta de ficheiros CSV, crie primeiro um `RxTextData` objeto ao utilizar um nome de diretório como o argumento de arquivo. Este objeto representa a pasta na qual pretende criar os ficheiros CSV. Este diretório é criado quando executa o `rxDataStep`. Em seguida, aponte para isso `RxTextData` objeto no `outFile` argumento do `rxDataStep`. Tem o nome cada CSV que é criado com base no nome do diretório e seguido por um número.

Suponha que queira escrever uma pasta de ficheiros CSV no HDFS do seu `airDataXdf` XDF composto depois de efetuar a regressão logística e de predição, para que os novos ficheiros CSV contêm os valores previstos e residuals. No RStudio, introduza o seguinte código:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Este passo deve ser concluído em cerca de 2,5 minutos.

O `rxDataStep` foi escrita a um ficheiro CSV para todos os ficheiros XDFD no ficheiro de compostos XDF entrada. Este é o comportamento predefinido para escrever ficheiros CSV de ficheiros XDF compostos HDFS quando o contexto de cálculo está definido como `RxSpark`.

### <a name="in-a-local-context"></a>Num contexto local

Em alternativa, quando terminar executando suas análises, poderia alternar seu contexto de cálculo para `local` para tirar partido de dois argumentos dentro `RxTextData` que fornecem a um pouco mais de controle quando escreve os ficheiros CSV no HDFS: `createFileSet` e `rowsPerOutFile`. Se definir `createFileSet` para `TRUE`, uma pasta de ficheiros CSV é escrita para o diretório que especificar. Se definir `createFileSet` para `FALSE`, um único ficheiro CSV é escrito. Pode definir o segundo argumento `rowsPerOutFile`, para um número inteiro para indicar o número de linhas para escrever cada CSV de ficheiros quando `createFileSet` é `TRUE`.

No RStudio, introduza o seguinte código:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Este passo deve ser concluído dentro de 10 minutos.

Quando utiliza um `RxSpark` contexto, de cálculo `createFileSet` assume a predefinição `TRUE` e `rowsPerOutFile` não tem qualquer efeito. Por conseguinte, se pretender criar um CSV único ou personalizar o número de linhas por ficheiro, execute `rxDataStep` num `local` contexto de cálculo (os dados podem ainda ser hdfs).

## <a name="final-steps"></a>Etapas finais

1. Limpe os dados. No RStudio, introduza o seguinte código:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Pare a aplicação Spark remota. No RStudio, introduza o seguinte código:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Sair da sessão de R. No RStudio, introduza o seguinte código:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o tutorial, pode querer eliminar o cluster. Com o HDInsight, os seus dados são armazenados no armazenamento do Azure, pelo que pode eliminar em segurança um cluster quando não está em utilização. Também cobrado um cluster do HDInsight, mesmo quando não está em utilização. Como os custos para o cluster são muito superiores aos custos do armazenamento, faz sentido em termos económico para eliminar os clusters quando não estiverem em utilização.

Para eliminar um cluster, veja [eliminar um cluster do HDInsight com o seu browser, o PowerShell ou a CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu como utilizar as funções R no Apache Spark em execução num cluster do HDInsight Machine Learning services. Para obter mais informações, veja os artigos seguintes:

* [Opções de contexto para um cluster do HDInsight do Azure Machine Learning dos serviços de computação](r-server-compute-contexts.md)
* [Funções de R para o Spark no Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
