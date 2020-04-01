---
title: 'Tutorial: Use R num contexto de computação de faísca no Azure HDInsight'
description: Tutorial - Inicie com R e Spark num cluster de serviços de Machine Learning Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 73ca0d089ab758fb13e69d341337139d79194cc5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "71121926"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Tutorial: Use R num contexto de computação de faísca no Azure HDInsight

Este tutorial fornece uma introdução passo a passo para a utilização das funções R em Apache Spark que funcionam num cluster de serviços de Machine Learning Azure HDInsight.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Descarregue os dados da amostra para o armazenamento local
> * Copiar os dados para armazenamento predefinido
> * Configurar um conjunto de dados
> * Criar fontes de dados
> * Criar um contexto computacional para a Spark
> * Ajuste um modelo linear
> * Utilize ficheiros XDF compósitos
> * Converter XDF em CSV

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de serviços de Machine Learning Azure HDInsight. Vá para [criar clusters Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e, para **o tipo cluster,** selecione **ML Services**.

## <a name="connect-to-rstudio-server"></a>Ligar ao RStudio Server

RStudio Server corre no nó de borda do cluster. Vá ao seguinte site (onde *clusterNAME* no URL é o nome do cluster de serviços hDInsight Machine Learning que criou):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

A primeira vez que se inscreve, autentica duas vezes. No primeiro pedido de autenticação, forneça o nome de utilizador e a palavra-passe do cluster (o predefinido é *administrador).* Na segunda solicitação de autenticação, forneça o nome de utilizador e a palavra-passe SSH (o predefinido é *sshuser).* Os subsequentes inscrições requerem apenas as credenciais SSH.

## <a name="download-the-sample-data-to-local-storage"></a>Descarregue os dados da amostra para o armazenamento local

O Conjunto de *Dados On-Time da Airline 2012* é composto por 12 ficheiros separados de vírem que contêm detalhes de chegada e partida de voos para todos os voos comerciais nos EUA para o ano de 2012. Este conjunto de dados é grande, com mais de 6 milhões de observações.

1. Inicialize algumas variáveis ambientais. Na consola RStudio Server, introduza o seguinte código:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. No painel certo, selecione o separador **Ambiente.** As variáveis são apresentadas em **Valores**.

    ![Consola web de estúdio HDInsight R](./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png)

1. Crie um diretório local e descarregue os dados da amostra. No RStudio, introduza o seguinte código:

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

    O download deve estar concluído em cerca de 9,5 minutos.

## <a name="copy-the-data-to-default-storage"></a>Copiar os dados para armazenamento predefinido

A localização do Sistema de Ficheiros Distribuídos hadoop `airDataDir` (HDFS) é especificada com a variável. No RStudio, introduza o seguinte código:

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

1. Crie um objeto de sistema de ficheiros que utilize os valores predefinidos. No RStudio, introduza o seguinte código:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Como os ficheiros CSV originais têm nomes variáveis bastante desajeitados, fornece-se uma lista *colInfo* para torná-los mais manejáveis. No RStudio, introduza o seguinte código:

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

Num contexto de computação Spark, pode criar fontes de dados utilizando as seguintes funções:

|Função | Descrição |
|---------|-------------|
|`RxTextData` | Uma fonte de dados de texto delimitada com vírposta. |
|`RxXdfData` | Dados no formato de ficheiros de dados XDF. No RevoScaleR, o formato de ficheiro XDF é modificado para hadoop armazenar dados num conjunto composto de ficheiros em vez de um único ficheiro. |
|`RxHiveData` | Gera um objeto de Origem de Dados da Colmeia.|
|`RxParquetData` | Gera um objeto De Origem de Dados Parquet.|
|`RxOrcData` | Gera um objeto Orc Data Source.|

Crie um objeto [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) utilizando os ficheiros copiados para o HDFS. No RStudio, introduza o seguinte código:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Criar um contexto computacional para a Spark

Para carregar dados e executar análises em nós de trabalhadores, define o contexto computacional no seu script para [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). Neste contexto, as funções R distribuem automaticamente a carga de trabalho em todos os nós dos trabalhadores, sem necessidade incorporada de gestão de empregos ou da fila. O contexto da computação `RxSpark` `rxSparkConnect()` Spark é estabelecido através ou para `rxSparkDisconnect()` criar o contexto da computação Spark, e usa para voltar a um contexto de computação local. No RStudio, introduza o seguinte código:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Ajuste um modelo linear

1. Utilize a função [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) para `airDS` encaixar num modelo linear utilizando a sua fonte de dados. No RStudio, introduza o seguinte código:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Este passo deve estar completo em 2 a 3 minutos.

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

    Os resultados indicam que processou todos os dados, 6 milhões de observações, utilizando todos os ficheiros CSV no diretório especificado. Como especificou, `cube = TRUE`tem um coeficiente estimado para cada dia da semana (e não a interceção).

## <a name="use-composite-xdf-files"></a>Utilize ficheiros XDF compósitos

Como já viste, podes analisar ficheiros CSV diretamente com o R em Hadoop. Mas pode fazer a análise mais rapidamente se armazenar os dados num formato mais eficiente. O formato de ficheiro R XDF é eficiente, mas é modificado um pouco para o HDFS para que os ficheiros individuais permaneçam dentro de um único bloco HDFS. (O tamanho do bloco HDFS varia de instalação para instalação, mas é tipicamente de 64 MB ou 128 MB.) 

Quando utiliza [o rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) em Hadoop para criar um conjunto `RxTextData` de ficheiros XDF compósitos, especifica uma fonte de dados como `AirDS` o inData e uma `RxXdfData` fonte de dados com o sistema de ficheiros definido para um sistema de ficheiros HDFS como o argumento outFile. Em seguida, `RxXdfData` pode utilizar o objeto como argumento de dados nas análises R subsequentes.

1. Defina `RxXdfData` um objeto. No RStudio, introduza o seguinte código:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Detete um bloco de 250000 linhas e especifique que lemos todos os dados. No RStudio, introduza o seguinte código:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importar os `rxImport`dados utilizando . No RStudio, introduza o seguinte código:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Este passo deve estar completo em poucos minutos.

1. Re-estimar o mesmo modelo linear, utilizando a nova fonte de dados mais rápida. No RStudio, introduza o seguinte código:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    O passo deve estar completo em menos de um minuto.

1. Veja os resultados. Os resultados devem ser os mesmos dos ficheiros CSV. No RStudio, introduza o seguinte código:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Converter XDF em CSV

### <a name="in-a-spark-context"></a>Em um contexto de faísca

Se converteu os seus ficheiros CSV para o formato de ficheiro XDF para uma maior eficiência durante a execução das análises, mas agora pretende converter os seus dados de volta para CSV, pode fazê-lo utilizando [o rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Para criar uma pasta de ficheiros `RxTextData` CSV, primeiro crie um objeto usando um nome de diretório como argumento de ficheiro. Este objeto representa a pasta para criar os ficheiros CSV. Este diretório é criado `rxDataStep`quando se dirige o . Em seguida, `RxTextData` aponte `outFile` para este `rxDataStep`objeto no argumento do . Cada CSV criado é nomeado com base no nome do diretório e seguido por um número.

Suponha que queira escrever uma pasta de ficheiros CSV em HDFS a partir do seu `airDataXdf` XDF composto depois de realizar a regressão e previsão logística, de modo que os novos ficheiros CSV contenham os valores e residuais previstos. No RStudio, introduza o seguinte código:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Este passo deve estar completo em cerca de 2,5 minutos.

O `rxDataStep` escreveu um ficheiro CSV para cada ficheiro XDFD no ficheiro XDF composto de entrada. Este é o comportamento padrão para escrever ficheiros CSV de ficheiros XDF `RxSpark`compósitos para HDFS quando o contexto da computação está definido para .

### <a name="in-a-local-context"></a>Em um contexto local

Em alternativa, quando terminar a realização das suas análises, poderá `local` mudar o contexto da `RxTextData` computação para tirar partido de dois argumentos dentro que `createFileSet` lhe `rowsPerOutFile`dão um pouco mais de controlo quando escreve ficheiros CSV para HDFS: e . Quando se `createFileSet` `TRUE`define para , uma pasta de ficheiros CSV é escrita para o diretório que especifica. Quando se `createFileSet` `FALSE`define, um único ficheiro CSV é escrito. Pode definir o segundo `rowsPerOutFile`argumento, para um inteiro para indicar quantas linhas escrever para `createFileSet` `TRUE`cada ficheiro CSV quando é .

No RStudio, introduza o seguinte código:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Este passo deve estar completo em cerca de 10 minutos.

Quando se `RxSpark` utiliza um `createFileSet` contexto computacional, falha `TRUE` e `rowsPerOutFile` não tem efeito. Portanto, se quiser criar um único CSV ou personalizar o número `rxDataStep` de `local` linhas por ficheiro, execute num contexto de cálculo (os dados ainda podem estar no HDFS).

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

1. Desista da sessão R. No RStudio, introduza o seguinte código:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Depois de completar o tutorial, talvez queira apagar o cluster. Com o HDInsight, os seus dados são armazenados no Armazenamento Azure, para que possa eliminar com segurança um cluster quando não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Como as taxas para o cluster são muitas vezes mais do que as taxas de armazenamento, faz sentido económico apagar aglomerados quando não estão a ser usados.

Para eliminar um cluster, consulte [Eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar funções R em Apache Spark que estão a funcionar num cluster de serviços hDInsight Machine Learning. Para obter mais informações, veja os artigos seguintes:

* [Opções de contexto computacional para um cluster de serviços de Machine Learning Azure HDInsight](r-server-compute-contexts.md)
* [Funções R para Faísca em Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
