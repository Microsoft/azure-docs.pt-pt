---
title: Alargar scripts U-SQL com R em Azure Data Lake Analytics
description: Aprenda a executar o código R em scripts U-SQL usando o Azure Data Lake Analytics. Insebed Código R inline ou referência a partir de ficheiros.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: c5dd3f493e85afc925b639c142a293eed1e8cbd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672685"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Alargar scripts U-SQL com código R no Azure Data Lake Analytics

O exemplo que se segue ilustra os passos básicos para a implementação do código R:
* Utilize `REFERENCE ASSEMBLY` a declaração para ativar as extensões R para o Script U-SQL.
* Utilize `REDUCE` a operação para dividir os dados de entrada numa tecla.
* As extensões R para U-SQL incluem`Extension.R.Reducer`um redutor incorporado que executa o código R em cada vértice atribuído ao redutor. 
* A utilização de quadros `inputFromUSQL` `outputToUSQL` de dados dedicados chamados e, respectivamente, para passar dados entre os nomes de identificador de entrada e saída de Dados de entrada e saída são fixados (ou seja, os utilizadores não podem alterar estes nomes predefinidos de identificadores de dados de entrada e saída).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Codificar de incorporação r no script U-SQL

Pode inlinear o código R do seu script U-SQL `Extension.R.Reducer`utilizando o parâmetro de comando do . Por exemplo, pode declarar o script R como uma variável de cadeia e passá-lo como parâmetro para o Redutor.


    REFERENCE ASSEMBLY [ExtR];
    
    DECLARE @myRScript = @"
    inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
    lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
    #do not return readonly columns and make sure that the column names are the same in usql and r scripts,
    outputToUSQL=data.frame(summary(lm.fit)$coefficients)
    colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
    outputToUSQL
    ";
    
    @RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, rReturnType:"dataframe");

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Mantenha o código R num ficheiro separado e refira-o ao script U-SQL

O exemplo que se segue ilustra uma utilização mais complexa. Neste caso, o código R é implantado como um RECURSO que é o script U-SQL.

Guarde este código R como um ficheiro separado.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Utilize um script U-SQL para implementar o script R com a declaração de RECURSOS DE IMPLANTAÇÃO.

    REFERENCE ASSEMBLY [ExtR];

    DEPLOY RESOURCE @"/usqlext/samples/R/RinUSQL_PredictUsingLinearModelasDF.R";
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT 
            SepalLength double,
            SepalWidth double,
            PetalLength double,
            PetalWidth double,
            Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT 
            Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput = REDUCE @ExtendedData ON Par
        PRODUCE Par, fit double, lwr double, upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile:"RinUSQL_PredictUsingLinearModelasDF.R", rReturnType:"dataframe", stringsAsFactors:false);
        OUTPUT @RScriptOutput TO @OutputFilePredictions USING Outputters.Tsv();

## <a name="how-r-integrates-with-u-sql"></a>Como r integra com U-SQL

### <a name="datatypes"></a>Tipos de dados
* As colunas de cordas e numéricas da U-SQL são convertidas como está `double`entre `string` `bool`O `integer` `byte`DataFrame R e o U-SQL [tipos suportados: , , , ]
* O `Factor` tipo de dados não é suportado em U-SQL.
* `byte[]`deve ser serializado como base64 `string`codificada .
* As cordas U-SQL podem ser convertidas em fatores no código R, uma vez que u-SQL crie um data de entrada R ou definindo o parâmetro `stringsAsFactors: true`redutor .

### <a name="schemas"></a>Esquemas
* Os conjuntos de dados U-SQL não podem ter nomes de colunas duplicados.
* Os nomes das colunas de conjuntos de dados U-SQL devem ser cordas.
* Os nomes das colunas devem ser os mesmos nos scripts U-SQL e R.
* A coluna de leitura não pode fazer parte do quadro de dados de saída. Porque as colunas de leitura são automaticamente injetadas na tabela U-SQL se fizer parte do esquema de saída do UDO.

### <a name="functional-limitations"></a>Limitações funcionais
* O Motor R não pode ser instantâneo duas vezes no mesmo processo. 
* Atualmente, a U-SQL não suporta UDOs combinados para previsão utilizando modelos divididos gerados usando UDOs redutores. Os utilizadores podem declarar os modelos divididos como recurso `ExtR_PredictUsingLMRawStringReducer.usql`e usá-los no seu Script R (ver código de amostra)

### <a name="r-versions"></a>Versões R
Apenas o R 3.2.2 é suportado.

### <a name="standard-r-modules"></a>Módulos R padrão

    base
    boot
    Class
    Cluster
    codetools
    compiler
    datasets
    doParallel
    doRSR
    foreach
    foreign
    Graphics
    grDevices
    grid
    iterators
    KernSmooth
    lattice
    MASS
    Matrix
    Methods
    mgcv
    nlme
    Nnet
    Parallel
    pkgXMLBuilder
    RevoIOQ
    revoIpe
    RevoMods
    RevoPemaR
    RevoRpeConnector
    RevoRsrConnector
    RevoScaleR
    RevoTreeView
    RevoUtils
    RevoUtilsMath
    Rpart
    RUnit
    spatial
    splines
    Stats
    stats4
    survival
    Tcltk
    Tools
    translations
    utils
    XML

### <a name="input-and-output-size-limitations"></a>Limitações do tamanho de entrada e saída
Cada vértice tem uma quantidade limitada de memória atribuída a ele. Uma vez que os DataFrames de entrada e de saída devem existir na memória no código R, o tamanho total da entrada e saída não pode exceder 500 MB.

### <a name="sample-code"></a>Código de Exemplo
Mais código de amostra está disponível na sua conta Data Lake Store depois de instalar as extensões U-SQL Advanced Analytics. O caminho para mais `<your_account_address>/usqlext/samples/R`código de amostra é: . 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Implementação de módulos Custom R com U-SQL

Primeiro, crie um módulo personalizado R e feche-o e, em seguida, carregue o ficheiro de módulo personalizado R com fecho para a sua loja ADL. No exemplo, enviaremos magittr_1.5.zip para a raiz da conta ADLS padrão para a conta ADLA que estamos a usar. Assim que enviar o módulo para a loja ADL, declare-o como recurso de `install.packages` implantação para o disponibilizar no seu script U-SQL e ligue para instalá-lo.

    REFERENCE ASSEMBLY [ExtR];
    DEPLOY RESOURCE @"/magrittr_1.5.zip";

    DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFileModelSummary string = @"/R/Output/CustomPackages.txt";

    // R script to run
    DECLARE @myRScript = @"
    # install the magrittr package,
    install.packages('magrittr_1.5.zip', repos = NULL),
    # load the magrittr package,
    require(magrittr),
    # demonstrate use of the magrittr package,
    2 %>% sqrt
    ";

    @InputData =
    EXTRACT SepalLength double,
    SepalWidth double,
    PetalLength double,
    PetalWidth double,
    Species string
    FROM @IrisData
    USING Extractors.Csv();

    @ExtendedData =
    SELECT 0 AS Par,
    *
    FROM @InputData;

    @RScriptOutput = REDUCE @ExtendedData ON Par
    PRODUCE Par, RowId int, ROutput string
    READONLY Par
    USING new Extension.R.Reducer(command:@myRScript, rReturnType:"charactermatrix");

    OUTPUT @RScriptOutput TO @OutputFileModelSummary USING Outputters.Tsv();

## <a name="next-steps"></a>Passos Seguintes
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio | Azure](data-lake-analytics-data-lake-tools-get-started.md)
* [Utilização de funções de janela U-SQL para trabalhos de Análise de Lago de Dados Azure](data-lake-analytics-use-window-functions.md)
