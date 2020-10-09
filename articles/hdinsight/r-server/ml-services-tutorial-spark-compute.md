---
title: 'Tutorial: Use R num contexto computo Spark em Azure HDInsight'
description: Tutorial - Começa com r e faísca num cluster de serviços de machine learning Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 73ca0d089ab758fb13e69d341337139d79194cc5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "71121926"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Tutorial: Use R num contexto computo Spark em Azure HDInsight

Este tutorial proporciona uma introdução passo a passo para usar as funções R em Apache Spark que funcionam num cluster de serviços de machine learning Azure HDInsight.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Faça o download dos dados da amostra para o armazenamento local
> * Copie os dados para o armazenamento predefinido
> * Configurar um conjunto de dados
> * Criar fontes de dados
> * Criar um contexto computacional para faísca
> * Encaixe num modelo linear
> * Utilize ficheiros XDF compostos
> * Converter XDF em CSV

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de serviços de machine learning Azure HDInsight. Vá para [criar clusters Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e, para o **tipo Cluster,** selecione **ML Services**.

## <a name="connect-to-rstudio-server"></a>Ligar ao RStudio Server

O RStudio Server funciona no nó de borda do cluster. Vá ao seguinte site (onde *clustername* no URL é o nome do cluster de serviços de aprendizagem automática HDInsight que criou):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

A primeira vez que entras, autenticas duas vezes. Na primeira instrução de autenticação, forneça o nome de utilizador e a palavra-passe do administrador do cluster (o padrão é *administrador).* Na segunda instrução de autenticação, forneça o nome de utilizador SSH e a palavra-passe (o padrão é *sshuser*). Os inserções subsequentes requerem apenas as credenciais SSH.

## <a name="download-the-sample-data-to-local-storage"></a>Faça o download dos dados da amostra para o armazenamento local

O *Conjunto de Dados On-Time da Companhia Aérea 2012* é composto por 12 ficheiros separados por vírgula que contêm detalhes de chegada e partida de voos para todos os voos comerciais dentro dos EUA para o ano de 2012. Este conjunto de dados é grande, com mais de 6 milhões de observações.

1. Inicialize algumas variáveis ambientais. Na consola RStudio Server, introduza o seguinte código:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. No painel direito, selecione o **separador Ambiente.** As variáveis são apresentadas em **Valores**.

    ![HdInsight R consola web estúdio](./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png)

1. Crie um diretório local e descarregue os dados da amostra. Em RStudio, introduza o seguinte código:

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

    O download deve estar completo em cerca de 9,5 minutos.

## <a name="copy-the-data-to-default-storage"></a>Copie os dados para o armazenamento predefinido

A localização do Sistema de Ficheiros Distribuídos Hadoop (HDFS) é especificada com a `airDataDir` variável. Em RStudio, introduza o seguinte código:

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

O passo deve estar completo em cerca de 10 segundos.

## <a name="set-up-a-dataset"></a>Configurar um conjunto de dados

1. Crie um objeto de sistema de ficheiros que utilize os valores predefinidos. Em RStudio, introduza o seguinte código:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Como os ficheiros CSV originais têm nomes variáveis bastante inflexíveis, você fornece uma lista *de colInfo* para torná-los mais manejáveis. Em RStudio, introduza o seguinte código:

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

## <a name="create-data-sources"></a>Criar fontes de dados

Num contexto computacional spark, pode criar fontes de dados utilizando as seguintes funções:

|Função | Descrição |
|---------|-------------|
|`RxTextData` | Uma fonte de dados de texto delimitada em vírgula. |
|`RxXdfData` | Dados no formato de ficheiro de dados XDF. No RevoScaleR, o formato de ficheiro XDF é modificado para hadoop para armazenar dados num conjunto composto de ficheiros em vez de um único ficheiro. |
|`RxHiveData` | Gera um objeto Hive Data Source.|
|`RxParquetData` | Gera um objeto Parquet Data Source.|
|`RxOrcData` | Gera um objeto Orc Data Source.|

Crie um objeto [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) utilizando os ficheiros copiados para HDFS. Em RStudio, introduza o seguinte código:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Criar um contexto computacional para faísca

Para carregar dados e executar análises nos nós dos trabalhadores, define o contexto de computação no seu script para [o RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). Neste contexto, as funções R distribuem automaticamente a carga de trabalho em todos os nós dos trabalhadores, sem necessidade incorporada para gerir postos de trabalho ou a fila. O contexto computacional spark é estabelecido através `RxSpark` ou para criar o contexto `rxSparkConnect()` computacional Spark, e ele usa `rxSparkDisconnect()` para voltar a um contexto compute local. Em RStudio, introduza o seguinte código:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Encaixe num modelo linear

1. Utilize a função [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) para encaixar num modelo linear utilizando a sua `airDS` fonte de dados. Em RStudio, introduza o seguinte código:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Este passo deve estar completo em 2 a 3 minutos.

1. Veja os resultados. Em RStudio, introduza o seguinte código:

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

    Os resultados indicam que processou todos os dados, 6 milhões de observações, usando todos os ficheiros CSV no diretório especificado. Como `cube = TRUE` especificou, tem um coeficiente estimado para cada dia da semana (e não para a interceção).

## <a name="use-composite-xdf-files"></a>Utilize ficheiros XDF compostos

Como viste, podes analisar ficheiros CSV diretamente com o R no Hadoop. Mas pode fazer a análise mais rapidamente se armazenar os dados num formato mais eficiente. O formato de ficheiro R XDF é eficiente, mas é modificado um pouco para HDFS para que os ficheiros individuais permaneçam dentro de um único bloco HDFS. (O tamanho do bloco HDFS varia de instalação para instalação, mas é tipicamente de 64 MB ou 128 MB.) 

Quando utiliza [o rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) no Hadoop para criar um conjunto de ficheiros XDF compósitos, especifica uma fonte de `RxTextData` dados como o `AirDS` inData e uma fonte de `RxXdfData` dados com o ficheiroSystem definido para um sistema de ficheiros HDFS como o argumento outFile. Em seguida, pode utilizar o `RxXdfData` objeto como argumento de dados nas análises R subsequentes.

1. Defina um `RxXdfData` objeto. Em RStudio, introduza o seguinte código:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Desajuste um bloco de 250000 linhas e especifique que lemos todos os dados. Em RStudio, introduza o seguinte código:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importe os dados utilizando `rxImport` . Em RStudio, introduza o seguinte código:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Este passo deve estar completo em poucos minutos.

1. Reestimui o mesmo modelo linear, utilizando a nova fonte de dados mais rápida. Em RStudio, introduza o seguinte código:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    O passo deve estar completo em menos de um minuto.

1. Veja os resultados. Os resultados devem ser os mesmos dos ficheiros CSV. Em RStudio, introduza o seguinte código:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Converter XDF em CSV

### <a name="in-a-spark-context"></a>Em um contexto de faísca

Se converteu os seus ficheiros CSV no formato de ficheiro XDF para uma maior eficiência durante a execução das análises, mas agora pretende converter os seus dados de volta para CSV, pode fazê-lo utilizando [o rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Para criar uma pasta de ficheiros CSV, primeiro crie um `RxTextData` objeto utilizando um nome de diretório como argumento de ficheiro. Este objeto representa a pasta na qual se criam os ficheiros CSV. Este diretório é criado quando você dirige o `rxDataStep` . Em seguida, aponte para este `RxTextData` objeto no argumento do `outFile` `rxDataStep` . Cada CSV que é criado é nomeado com base no nome do diretório e seguido por um número.

Suponha que pretende escrever uma pasta de ficheiros CSV em HDFS a partir do seu XDF composto depois de `airDataXdf` realizar a regressão logística e previsão, de modo a que os novos ficheiros CSV contenham os valores e residuais previstos. Em RStudio, introduza o seguinte código:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Este passo deve estar completo em cerca de 2,5 minutos.

Escreveu `rxDataStep` um ficheiro CSV para cada ficheiro XDFD no ficheiro XDF composto de entrada. Este é o comportamento padrão para a escrita de ficheiros CSV de ficheiros XDF compostos para HDFS quando o contexto de computação é definido para `RxSpark` .

### <a name="in-a-local-context"></a>Num contexto local

Em alternativa, quando terminar de realizar as suas análises, pode mudar o seu contexto de computação `local` para tirar partido de dois argumentos dentro que lhe `RxTextData` dão um pouco mais de controlo quando escreve ficheiros CSV para HDFS: `createFileSet` e `rowsPerOutFile` . Quando se prepara `createFileSet` para , uma pasta de `TRUE` ficheiros CSV é escrita para o diretório que especifica. Quando se prepara `createFileSet` para , um único ficheiro `FALSE` CSV é escrito. Pode definir o segundo argumento, `rowsPerOutFile` para um inteiro para indicar quantas linhas escrever para cada ficheiro CSV quando estiver `createFileSet` `TRUE` .

Em RStudio, introduza o seguinte código:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Este passo deve estar completo em cerca de 10 minutos.

Quando se utiliza um `RxSpark` contexto computacional, `createFileSet` o incumprimento é e não tem qualquer `TRUE` `rowsPerOutFile` efeito. Portanto, se pretender criar um único CSV ou personalizar o número de linhas por ficheiro, execute `rxDataStep` num `local` contexto computacional (os dados ainda podem estar em HDFS).

## <a name="final-steps"></a>Passos finais

1. Limpe os dados. Em RStudio, introduza o seguinte código:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Pare a aplicação remota de Faísca. Em RStudio, introduza o seguinte código:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Desista da sessão de R. Em RStudio, introduza o seguinte código:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de completar o tutorial, é melhor apagar o cluster. Com o HDInsight, os seus dados são armazenados no Azure Storage, para que possa eliminar com segurança um cluster quando este não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Como as taxas para o cluster são muitas vezes mais do que as taxas de armazenamento, faz sentido económico apagar aglomerados quando não estão a ser utilizados.

Para eliminar um cluster, consulte [Eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar funções R em Apache Spark que estão a funcionar num cluster de serviços de aprendizagem automática HDInsight. Para obter mais informações, veja os artigos seguintes:

* [Opções de contexto computacional para um cluster de serviços de machine learning Azure HDInsight](r-server-compute-contexts.md)
* [Funções R para faísca em Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
