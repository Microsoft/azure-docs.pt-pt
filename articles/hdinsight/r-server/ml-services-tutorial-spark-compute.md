---
title: 'Tutorial: Usar R em um contexto de computação do Spark no Azure HDInsight'
description: Tutorial-introdução ao R e ao Spark em um cluster de serviços Machine Learning do Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: a58d7befdb1066ce08ad1e46e6550141e2b0824f
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900002"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Tutorial: Usar R em um contexto de computação do Spark no Azure HDInsight

Este tutorial fornece uma introdução passo a passo para usar as funções do R no Apache Spark que são executadas em um cluster de serviços de Machine Learning do Azure HDInsight.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Baixar os dados de exemplo no armazenamento local
> * Copiar os dados para o armazenamento padrão
> * Configurar um conjunto de uma
> * Criar fontes de dados
> * Criar um contexto de computação para o Spark
> * Ajustar um modelo linear
> * Usar arquivos XDF compostos
> * Converter XDF em CSV

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de serviços Machine Learning do Azure HDInsight. Vá para [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e, para **tipo de cluster**, selecione **serviços de ml**.

## <a name="connect-to-rstudio-server"></a>Ligar ao RStudio Server

O servidor RStudio é executado no nó de borda do cluster. Vá para o seguinte site (em que *ClusterName* na URL é o nome do cluster do HDInsight Machine Learning Services que você criou):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Na primeira vez que você entrar, você autentica duas vezes. No primeiro prompt de autenticação, forneça o nome de usuário e a senha do administrador do cluster (o padrão é *admin*). No segundo prompt de autenticação, forneça o nome de usuário e a senha do SSH (o padrão é *sshuser*). As entradas subsequentes exigem apenas as credenciais SSH.

## <a name="download-the-sample-data-to-local-storage"></a>Baixar os dados de exemplo no armazenamento local

O *conjunto de dados em tempo da companhia aérea 2012* consiste em 12 arquivos separados por vírgulas que contêm detalhes de chegada e saída de voo para todos os vôos comerciais dentro dos EUA para o ano 2012. Este conjunto de DataSet é grande, com mais de 6 milhões observações.

1. Inicialize algumas variáveis de ambiente. No console do servidor do RStudio, digite o seguinte código:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. No painel direito, selecione a guia **ambiente** . As variáveis são exibidas em **valores**.

    ![RStudio](./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png)

1.  Crie um diretório local e baixe os dados de exemplo. Em RStudio, insira o código a seguir:

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

## <a name="copy-the-data-to-default-storage"></a>Copiar os dados para o armazenamento padrão

O local do sistema de arquivos distribuído do Hadoop (HDFS) é especificado `airDataDir` com a variável. Em RStudio, insira o código a seguir:

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

A etapa deve ser concluída em cerca de 10 segundos.

## <a name="set-up-a-dataset"></a>Configurar um conjunto de uma

1. Crie um objeto do sistema de arquivos que usa os valores padrão. Em RStudio, insira o código a seguir:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Como os arquivos CSV originais têm nomes de variável bastante difíceis, você fornece uma lista *colInfo* para torná-los mais gerenciáveis. Em RStudio, insira o código a seguir:

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

Em um contexto de computação do Spark, você pode criar fontes de dados usando as seguintes funções:

|Função | Descrição |
|---------|-------------|
|`RxTextData` | Uma fonte de dados de texto delimitada por vírgula. |
|`RxXdfData` | Dados no formato de arquivo de dados XDF. No RevoScaleR, o formato de arquivo XDF é modificado para que o Hadoop armazene dados em um conjunto composto de arquivos em vez de um único arquivo. |
|`RxHiveData` | Gera um objeto de fonte de dados do hive.|
|`RxParquetData` | Gera um objeto de fonte de dados parquet.|
|`RxOrcData` | Gera um objeto de fonte de dados Orc.|

Crie um objeto [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) usando os arquivos que você copiou para o HDFS. Em RStudio, insira o código a seguir:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Criar um contexto de computação para o Spark

Para carregar dados e executar análises em nós de trabalho, defina o contexto de computação em seu script como [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). Nesse contexto, as funções do R distribuem automaticamente a carga de trabalho em todos os nós de trabalhador, sem nenhum requisito interno para gerenciar trabalhos ou a fila. O contexto de computação do Spark é `RxSpark` estabelecido `rxSparkConnect()` por meio de ou para criar o contexto de computação `rxSparkDisconnect()` do Spark e ele usa para retornar a um contexto de computação local. Em RStudio, insira o código a seguir:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Ajustar um modelo linear

1. Use a função [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) para se ajustar a um modelo linear `airDS` usando sua fonte de dados. Em RStudio, insira o código a seguir:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Esta etapa deve ser concluída em 2 a 3 minutos.

1. Veja os resultados. Em RStudio, insira o código a seguir:

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

    Os resultados indicam que você processou todos os dados, 6 milhões observações, usando todos os arquivos CSV no diretório especificado. Como você especificou `cube = TRUE`, tem um coeficiente estimado para cada dia da semana (e não a interceptação).

## <a name="use-composite-xdf-files"></a>Usar arquivos XDF compostos

Como você viu, é possível analisar arquivos CSV diretamente com R no Hadoop. Mas você pode fazer a análise mais rapidamente se armazenar os dados em um formato mais eficiente. O formato de arquivo XDF do R é eficiente, mas é modificado um pouco para o HDFS para que os arquivos individuais permaneçam dentro de um único bloco HDFS. (O tamanho do bloco HDFS varia da instalação para a instalação, mas geralmente é 64 MB ou 128 MB.) 

Ao usar o [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) no Hadoop para criar um conjunto de arquivos Xdf de composição, você especifica `RxTextData` uma fonte de `AirDS` dados como, por exemplo, os `RxXdfData` indados e uma fonte de dados com fileSystem definido como um sistema de arquivos HDFS como o argumento outfile. Você pode usar o `RxXdfData` objeto como o argumento de dados em análises de R subsequentes.

1. Defina um `RxXdfData` objeto. Em RStudio, insira o código a seguir:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Defina um tamanho de bloco de 250000 linhas e especifique que todos os dados foram lidos. Em RStudio, insira o código a seguir:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importe os dados usando `rxImport`. Em RStudio, insira o código a seguir:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Esta etapa deve ser concluída em alguns minutos.

1. Recalcule o mesmo modelo linear, usando a nova fonte de dados mais rápida. Em RStudio, insira o código a seguir:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    A etapa deve ser concluída em menos de um minuto.

1. Veja os resultados. Os resultados devem ser os mesmos dos arquivos CSV. Em RStudio, insira o código a seguir:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Converter XDF em CSV

### <a name="in-a-spark-context"></a>Em um contexto do Spark

Se você converteu os arquivos CSV em formato de arquivo XDF para obter maior eficiência ao executar as análises, mas agora deseja converter seus dados de volta em CSV, você pode fazer isso usando o [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Para criar uma pasta de arquivos CSV, primeiro crie um `RxTextData` objeto usando um nome de diretório como o argumento de arquivo. Esse objeto representa a pasta na qual os arquivos CSV são criados. Esse diretório é criado quando você executa o `rxDataStep`. Em seguida, aponte para `RxTextData` esse objeto `outFile` no argumento do `rxDataStep`. Cada CSV criado é nomeado com base no nome do diretório e seguido por um número.

Suponha que você deseja gravar uma pasta de arquivos CSV no HDFS de seu `airDataXdf` Xdf de composição depois de executar a regressão logística e a previsão, para que os novos arquivos CSV contenham os valores previstos e os resíduos. Em RStudio, insira o código a seguir:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Esta etapa deve ser concluída em cerca de 2,5 minutos.

O `rxDataStep` escreveu um arquivo CSV para cada arquivo XDFD no arquivo Xdf de composição de entrada. Esse é o comportamento padrão para gravar arquivos CSV de arquivos XDF compostos no HDFS quando o contexto de computação é definido `RxSpark`como.

### <a name="in-a-local-context"></a>Em um contexto local

Como alternativa, quando terminar de executar suas análises, você pode mudar seu contexto de computação de volta para `local` aproveitar os dois argumentos dentro `RxTextData` que oferecem um pouco mais de controle ao gravar arquivos CSV no HDFS: `createFileSet` e `rowsPerOutFile`. Quando você define `createFileSet` como `TRUE`, uma pasta de arquivos CSV é gravada no diretório que você especificar. Quando você define `createFileSet` como `FALSE`, um único arquivo CSV é gravado. Você pode definir o segundo argumento, `rowsPerOutFile`, para um inteiro para indicar quantas linhas gravar em cada arquivo CSV quando `createFileSet` for `TRUE`.

Em RStudio, insira o código a seguir:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Esta etapa deve ser concluída em cerca de 10 minutos.

Quando você usa um `RxSpark` contexto de computação `createFileSet` , o usa `TRUE` como `rowsPerOutFile` padrão e não tem nenhum efeito. Portanto, se você quiser criar um único CSV ou personalizar o número de linhas por arquivo, execute `rxDataStep` em um `local` contexto de computação (os dados ainda podem estar no HDFS).

## <a name="final-steps"></a>Etapas finais

1. Limpar os dados. Em RStudio, insira o código a seguir:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Pare o aplicativo Spark remoto. Em RStudio, insira o código a seguir:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Saia da sessão do R. Em RStudio, insira o código a seguir:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o tutorial, talvez você queira excluir o cluster. Com o HDInsight, seus dados são armazenados no armazenamento do Azure, para que você possa excluir um cluster com segurança quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes mais do que os encargos de armazenamento, ele faz sentido econômico excluir clusters quando eles não estiverem em uso.

Para excluir um cluster, consulte [excluir um cluster HDInsight usando seu navegador, o PowerShell ou o CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu a usar as funções do R no Apache Spark que estão em execução em um cluster do HDInsight Machine Learning Services. Para obter mais informações, veja os artigos seguintes:

* [Opções de contexto de computação para um cluster de serviços de Machine Learning do Azure HDInsight](r-server-compute-contexts.md)
* [Funções do R para Spark no Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
