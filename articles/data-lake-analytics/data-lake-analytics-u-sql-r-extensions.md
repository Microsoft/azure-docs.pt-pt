---
title: Estender scripts U-SQL com R no Azure Data Lake Analytics
description: Saiba como executar o código R em scripts U-SQL usando Azure Data Lake Analytics. Inserir código R embutido ou referência de arquivos.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: c5dd3f493e85afc925b639c142a293eed1e8cbd7
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672685"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Estender scripts U-SQL com código R no Azure Data Lake Analytics

O exemplo a seguir ilustra as etapas básicas para implantar o código R:
* Use a instrução `REFERENCE ASSEMBLY` para habilitar as extensões do R para o script U-SQL.
* Use a operação `REDUCE` para particionar os dados de entrada em uma chave.
* As extensões do R para o U-SQL incluem um redutor interno (`Extension.R.Reducer`) que executa o código R em cada vértice atribuído ao redutor. 
* Uso de quadros de dados nomeados dedicados chamado `inputFromUSQL` e `outputToUSQL`, respectivamente, para passar dados entre U-SQL e R. os nomes do identificador de dataframe de entrada e saída são fixos (ou seja, os usuários não podem alterar esses nomes predefinidos de identificadores de dataframe de entrada e saída).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Inserindo código R no script U-SQL

Você pode embutir o código do R no script U-SQL usando o parâmetro de comando do `Extension.R.Reducer`. Por exemplo, você pode declarar o script R como uma variável de cadeia de caracteres e passá-lo como um parâmetro para o redutor.


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

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Manter o código R em um arquivo separado e fazer referência a ele o script U-SQL

O exemplo a seguir ilustra um uso mais complexo. Nesse caso, o código R é implantado como um recurso que é o script U-SQL.

Salve este código R como um arquivo separado.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Use um script U-SQL para implantar esse script R com a instrução DEPLOY RESOURCE.

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

## <a name="how-r-integrates-with-u-sql"></a>Como o R se integra com o U-SQL

### <a name="datatypes"></a>Tipos
* As colunas de cadeia de caracteres e numéricas do U-SQL são convertidas como estão entre R dataframe e U-SQL [tipos com suporte: `double`, `string`, `bool`, `integer`, `byte`].
* Não há suporte para o tipo de dados `Factor` no U-SQL.
* `byte[]` deve ser serializado como um `string` codificado em base64.
* As cadeias de caracteres u-SQL podem ser convertidas em fatores no código R, uma vez que o U-SQL cria um dataframe de entrada R ou definindo o parâmetro redutor `stringsAsFactors: true`.

### <a name="schemas"></a>Esquemas
* Conjuntos de d-SQL não podem ter nomes de coluna duplicados.
* Os nomes de coluna dos conjuntos de valores do U-SQL devem ser cadeias de caracteres.
* Os nomes de coluna devem ser os mesmos em scripts U-SQL e R.
* A coluna ReadOnly não pode fazer parte do dataframe de saída. Como as colunas ReadOnly são injetadas automaticamente na tabela U-SQL se ela fizer parte do esquema de saída de UDO.

### <a name="functional-limitations"></a>Limitações funcionais
* O mecanismo do R não pode ser instanciado duas vezes no mesmo processo. 
* Atualmente, o U-SQL não dá suporte ao Combiner UDOs para previsão usando modelos particionados gerados usando o redutor UDOs. Os usuários podem declarar os modelos particionados como recursos e usá-los em seu script R (consulte o código de exemplo `ExtR_PredictUsingLMRawStringReducer.usql`)

### <a name="r-versions"></a>Versões do R
Há suporte apenas para 3.2.2 R.

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

### <a name="input-and-output-size-limitations"></a>Limitações de tamanho de entrada e saída
Cada vértice tem uma quantidade limitada de memória atribuída a ele. Como os quadros de dados de entrada e saída devem existir na memória no código R, o tamanho total para a entrada e a saída não pode exceder 500 MB.

### <a name="sample-code"></a>Código de exemplo
Mais código de exemplo está disponível em sua conta de Data Lake Store depois de instalar as extensões de análise avançada do U-SQL. O caminho para mais código de exemplo é: `<your_account_address>/usqlext/samples/R`. 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Implantando módulos R personalizados com o U-SQL

Primeiro, crie um módulo personalizado do R e o zip e, em seguida, carregue o arquivo de módulo personalizado do R em seu repositório do ADL. No exemplo, carregaremos magittr_ 1.5. zip na raiz da conta ADLS padrão para a conta ADLA que estamos usando. Depois de carregar o módulo para o ADL Store, declare-o como usar o recurso de implantação para disponibilizá-lo em seu script U-SQL e chame `install.packages` para instalá-lo.

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

## <a name="next-steps"></a>Próximos Passos
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Usando funções de janela do U-SQL para trabalhos de Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
