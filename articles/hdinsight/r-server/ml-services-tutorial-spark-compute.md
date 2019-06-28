---
title: 'Tutorial: Utilize R no Spark computação contexto no Azure HDInsight'
description: Tutorial - introdução ao R e Spark nos serviços de ML.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 244c62467f187417bbb9f0e54173aad5a7d26d0a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450283"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Tutorial: Utilize R no Spark computação contexto no Azure HDInsight

Este tutorial disponibiliza uma introdução passo a passo para utilizar as funções de R no Apache Spark em execução num cluster de serviços de ML no Azure HDInsight.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Transferir dados de exemplo para o armazenamento local
> * Copiar dados para o armazenamento predefinido
> * Configurar o conjunto de dados
> * Criar origem de dados
> * Criar o contexto de cálculo para Spark
> * Ajustar um modelo linear
> * Utilizar ficheiros XDF compostos
> * Converter XDF para CSV

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de serviços de ML no HDInsight. Ver [Apache Hadoop criar clusters no portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **serviços ML** para **tipo de Cluster**.

## <a name="connect-to-rstudio-server"></a>Ligar ao RStudio Server

RStudio Server é executado no nó de extremidade do cluster. Aceda ao URL seguinte onde `CLUSTERNAME` é o nome do cluster serviços ML criou:

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

A primeira vez que iniciar sessão terá de se autenticar duas vezes. No primeiro aviso de autenticação, forneça o início de sessão de administrador de cluster e a palavra-passe, a predefinição é `admin`. No segundo aviso de autenticação, forneça o início de sessão SSH e a palavra-passe, a predefinição é `sshuser`. Inícios de sessão subsequentes necessitam apenas das credenciais SSH.

## <a name="download-sample-data"></a>Transferir os dados de exemplo

O *companhia aérea 2012 conjunto de dados de forma atempada* consiste em 12 arquivos separados por vírgulas que contém informações sobre detalhes de chegada e de saída de voo para todos os voos comerciais nos EUA, para o ano 2012. Este é um grande conjunto de dados com mais de seis milhões observações.

1. Inicialize algumas variáveis de ambiente. Introduza o código seguinte na consola do RStudio Server:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

    As variáveis serão apresentado no lado direito da tela sob o **ambiente** separador.

    ![RStudio](./media/ml-services-tutorial-spark-compute/rstudio.png)

2.  Criar diretório local e transferir dados de exemplo. Introduza o seguinte código no RStudio:

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

    O download deve demorar cerca de 9 e um meio minutos.

## <a name="copy-data-to-default-storage"></a>Copiar dados para o armazenamento predefinido

A localização de HDFS é especificada com o `airDataDir` variável. Introduza o seguinte código no RStudio:

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

O passo deve ser concluído em cerca de 10 segundos.

## <a name="set-up-data-set"></a>Configurar o conjunto de dados

1. Crie um objeto de sistema de ficheiros que utiliza os valores predefinidos. Introduza o seguinte código no RStudio:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

2. Os ficheiros CSV originais têm nomes de variáveis bastante complicados, portanto, fornecemos uma `colInfo` lista para que fiquem mais fáceis de gerir. Introduza o seguinte código no RStudio:

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

## <a name="create-data-source"></a>Criar origem de dados

Num contexto de computação Spark, pode criar origens de dados com as seguintes funções:

|Função | Descrição |
|---------|-------------|
|`RxTextData` | Uma origem de dados de texto delimitado por vírgulas. |
|`RxXdfData` | Dados no formato de ficheiro de dados XDF. No RevoScaleR, o formato de arquivo XDF é modificado para o Hadoop armazenar dados num conjunto composto de arquivos em vez de um único arquivo. |
|`RxHiveData` | Gera um objeto de origem de dados do Hive.|
|`RxParquetData` | Gera um objeto de origem de dados de Parquet.|
|`RxOrcData` | Gera um objeto de origem de dados de Orc.|

Criar uma [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) objeto usando os arquivos copiados no HDFS. Introduza o seguinte código no RStudio:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-compute-context-for-spark"></a>Criar o contexto de cálculo para Spark

Para carregar dados e executar análises em nós de trabalho, defina o contexto de cálculo no script para [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). Neste contexto, as funções de R automaticamente distribuem a carga de trabalho em todos os nós de trabalho, nenhum requisito incorporado para o gerenciamento de tarefas ou a fila. O contexto de cálculo do Spark é estabelecido através de `RxSpark` ou `rxSparkConnect()` para criar o Spark, o contexto de cálculo e utiliza `rxSparkDisconnect()` para retornar ao contexto de cálculo local. Introduza o seguinte código no RStudio:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Ajustar um modelo linear

1. Utilize o [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) funcionar de acordo com um modelo linear com sua `airDS` origem de dados. Introduza o seguinte código no RStudio:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Este passo deve ser concluído entre 2 e 3 minutos.

1. Veja os resultados. Introduza o seguinte código no RStudio:

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

    Tenha em atenção que os resultados indicam processámos a todos os dados, observações de seis milhões, com todos os ficheiros. csv no diretório especificado. Observe também que uma vez que especificamos `cube = TRUE`, temos um coeficiente estimada para cada dia da semana (e não a interceptação).

## <a name="use-composite-xdf-files"></a>Utilizar ficheiros XDF compostos

Como temos visto, pode analisar os ficheiros CSV diretamente com o R no Hadoop, mas a análise pode ser feita mais rapidamente se os dados são armazenados num formato mais eficiente. O formato de .xdf R é extremamente eficiente, mas é modificado-um pouco para HDFS, para que permaneçam de arquivos individuais dentro de um único bloco HDFS. (O tamanho do bloco HDFS varia de instalação para instalação, mas é normalmente 64MB ou 128 MB.) Quando utiliza [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) no Hadoop, especifique um `RxTextData` da origem de dados, tais como `AirDS` como o inData e um `RxXdfData` origem de dados com o sistema de ficheiros definido como um sistema de ficheiros HDFS que o argumento de outFile para criar um conjunto de ficheiros de .xdf compostos. O `RxXdfData` objeto, em seguida, pode ser usado como o argumento de dados em análises subsequentes do R.

1. Definir um `RxXdfData` objeto. Introduza o seguinte código no RStudio:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Definir um tamanho de bloco de 250000 linhas e especifique que Lemos todos os dados. Introduza o seguinte código no RStudio:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importar os dados com `rxImport`. Introduza o seguinte código no RStudio:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Este passo deve ser concluído dentro de alguns minutos.

1. Fazer uma nova estimativa o mesmo modelo linear, utilizar a origem de dados novo e mais rápido. Introduza o seguinte código no RStudio:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    O passo de instalação deve demorar menos de um minuto.

1. Veja os resultados. Os resultados devem ser os mesmos, como os ficheiros CSV. Introduza o seguinte código no RStudio:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Converter XDF para CSV

### <a name="in-a-spark-context"></a>Num contexto de Spark

Se tiver convertido o seus CSV para XDF para tirar partido da eficiência durante a execução de análises, mas agora pretende converter os dados CSV pode fazer isso usando [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Para criar uma pasta de ficheiros CSV, crie primeiro um `RxTextData` usando um nome de diretório como o argumento de arquivo de objeto; isso representa a pasta na qual pretende criar os ficheiros CSV. Este diretório é criado quando executa o `rxDataStep`. Em seguida, aponte para isso `RxTextData` objeto no `outFile` argumento do `rxDataStep`. Terá o nome cada CSV criado com base no nome do diretório e seguido por um número.

Suponha que desejemos escrever uma pasta de CSV no HDFS do nosso `airDataXdf` XDF composto depois efetuámos a regressão logística e de predição, para que os novos ficheiros CSV contêm os valores previstos e residuals. Introduza o seguinte código no RStudio:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Este passo deve ser concluído em cerca de duas e um meio minutos.

Notar que o `rxDataStep` foi escrita a um CSV para todos os ficheiros de .xdfd no ficheiro de compostos XDF entrada. Este é o comportamento predefinido para gravação CSV de XDF composto no HDFS, quando o contexto de cálculo está definido como `RxSpark`.

### <a name="in-a-local-context"></a>Num contexto local

Em alternativa, poderia alternar seu contexto de cálculo para `local` quando tiver terminado a realização de suas análises e tire partido de dois argumentos dentro `RxTextData` que fornecem a um pouco mais de controle quando o gravar ficheiros CSV no HDFS: `createFileSet` e `rowsPerOutFile`. Quando `createFileSet` está definido como `TRUE`, uma pasta de ficheiros CSV é escrita para o diretório especificado. Quando `createFileSet` está definido como `FALSE` um único ficheiro CSV é escrito. O segundo argumento `rowsPerOutFile`, pode ser definido para um número inteiro para indicar o número de linhas para escrever cada CSV de ficheiros quando `createFileSet` é `TRUE`.

Introduza o seguinte código no RStudio:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Este passo deve ser concluído dentro de 10 minutos.

Quando utilizar um `RxSpark` contexto, de cálculo `createFileSet` assume a predefinição `TRUE` e `rowsPerOutFile` não tem qualquer efeito. Portanto, se pretender criar um CSV único ou personalizar o número de linhas por ficheiro tem de executar o `rxDataStep` num `local` contexto de cálculo (os dados ainda podem ser hdfs).

## <a name="final-steps"></a>Etapas finais

1. Limpe os dados. Introduza o seguinte código no RStudio:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Pare a aplicação Spark remota. Introduza o seguinte código no RStudio:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Sair da sessão de R. Introduza o seguinte código no RStudio:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o tutorial, pode pretender eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para eliminar um cluster, veja [eliminar um cluster do HDInsight com o seu browser, o PowerShell ou a CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar funções de R no Apache Spark em execução num cluster de serviços de ML no Azure HDInsight. Para obter mais informações, veja os artigos seguintes:

* [Opções de contexto para os serviços de ML no HDInsight de computação](r-server-compute-contexts.md)
* [Funções de R para o Spark no Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
