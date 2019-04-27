---
title: Expandir scripts U-SQL com o R no Azure Data Lake Analytics
description: Saiba como executar o código de R em scripts U-SQL com o Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 59a52b2aeb83732a608f1fcf5bc4de907d25dfd1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813754"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Expandir scripts U-SQL com o código de R no Azure Data Lake Analytics

O exemplo seguinte ilustra as etapas básicas para implementar o código R:
* Utilize o `REFERENCE ASSEMBLY` instrução para ativar extensões R para o Script de U-SQL.
* Utilize o `REDUCE` operação para os dados de entrada numa chave de partição.
* As extensões de R do U-SQL incluem um reducer incorporada (`Extension.R.Reducer`) que executa o código de R em cada vértice atribuído para o reducer. 
* Utilização de dedicado com o nome quadros de dados chamados `inputFromUSQL` e `outputToUSQL` , respetivamente, para passar dados entre o U-SQL e R. entrada e saída foram corrigidos os nomes de identificador de pacote de dados (ou seja, os utilizadores não podem alterar esses nomes predefinidos de entrada e saída DataFrame identificadores).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Incorporar o código R no script de U-SQL

Pode inline o R de código do script de U-SQL utilizando o parâmetro de comando do `Extension.R.Reducer`. Por exemplo, pode declarar o script de R como uma variável de cadeia de caracteres e passá-lo como um parâmetro para o Reducer.


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

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Manter o código de R num arquivo separado e fazer referência a ela o script de U-SQL

O exemplo a seguir ilustra um uso mais complexo. Neste caso, o código de R é implementado como um recurso que é o script de U-SQL.

Guarde este código de R como um arquivo separado.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Utilize um script de U-SQL para implementar esse script R com a instrução de implementar recursos.

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

### <a name="datatypes"></a>Tipos de dados
* Colunas de cadeia de caracteres e numéricos de U-SQL são convertidas como-entre DataFrame de R e U-SQL [tipos suportados: `double`, `string`, `bool`, `integer`, `byte`].
* O `Factor` tipo de dados não é suportado em U-SQL.
* `byte[]` tem de ser serializado como um codificada em base64 `string`.
* Cadeias de caracteres de U-SQL podem ser convertidas em fatores no código de R, depois de U-SQL criar pacote de dados de entrada de R ou ao definir o parâmetro reducer `stringsAsFactors: true`.

### <a name="schemas"></a>Esquemas
* Conjuntos de dados U-SQL não podem ter nomes de colunas duplicados.
* Nomes de coluna de conjuntos de dados U-SQL têm de ser cadeias de caracteres.
* Os nomes das colunas tem de ser o mesmo no U-SQL e os R scripts.
* Coluna de só de leitura não pode fazer parte do pacote de dados de saída. Uma vez que as colunas de só de leitura são inseridos automaticamente à volta na tabela U-SQL se faz parte do esquema de saída do UDO.

### <a name="functional-limitations"></a>Limitações funcionais
* O mecanismo do R não pode ser instanciado duas vezes no mesmo processo. 
* Atualmente, o U-SQL não suporta UDOs de combinação para previsão usando modelos particionados gerados usando Reducer UDOs. Os utilizadores podem declarar os modelos de particionada como recurso e usá-los em seu Script R (consulte o código de exemplo `ExtR_PredictUsingLMRawStringReducer.usql`)

### <a name="r-versions"></a>Versões do R
Apenas R 3.2.2 é suportada.

### <a name="standard-r-modules"></a>Módulos padrão do R

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

### <a name="input-and-output-size-limitations"></a>Entrada e limitações de tamanho de saída
Cada vértice tem uma quantidade limitada de memória atribuída à mesma. Uma vez que os pacotes de entrada e saída têm de existir na memória no código R, o tamanho total de entrada e de saída não pode exceder os 500 MB.

### <a name="sample-code"></a>Código de Exemplo
Mais código de exemplo está disponível na sua conta do Data Lake Store depois de instalar as extensões de análise avançada de U-SQL. O caminho para obter mais código de exemplo é: `<your_account_address>/usqlext/samples/R`. 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Implementar módulos R personalizados com o U-SQL

Em primeiro lugar, crie um módulo personalizado de R e zip-lo e, em seguida, carregue o ficheiro de módulo personalizado de R zipado ao seu arquivo ADL. No exemplo, podemos irá carregar magittr_1.5.zip na raiz da conta do ADLS padrão para a conta ADLA que estamos a utilizar. Depois de carregar o módulo para o arquivo do ADL, declará-lo como usar o recurso de implementar para disponibilizá-lo no seu script de U-SQL e a chamada `install.packages` para instalá-lo.

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
* [Usando as funções de janela de U-SQL para tarefas de Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
