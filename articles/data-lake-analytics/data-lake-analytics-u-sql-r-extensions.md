---
title: Estender scripts U-SQL com R em Azure Data Lake Analytics
description: Aprenda a executar código R em scripts U-SQL usando Azure Data Lake Analytics. Incorporar inline de código R ou referência a partir de ficheiros.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/20/2017
ms.openlocfilehash: ab28ba1bb6185f5f0ab90df99f0abea7895b1bf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218005"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Estender scripts U-SQL com código R em Azure Data Lake Analytics

O exemplo a seguir ilustra os passos básicos para a implementação do código R:

* Utilize a `REFERENCE ASSEMBLY` declaração para ativar as extensões R para o Script U-SQL.
* Utilize a `REDUCE` operação para dividir os dados de entrada numa chave.
* As extensões R para U-SQL incluem um redutor incorporado ( `Extension.R.Reducer` ) que executa código R em cada vértice atribuído ao redutor.
* A utilização de quadros de dados nomeados dedicados chamados `inputFromUSQL` `outputToUSQL` e, respectivamente, para transmitir dados entre u-SQL e R. Os nomes identificadores de dados de saída são fixos (ou seja, os utilizadores não podem alterar estes nomes predefinidos dos identificadores de dados de entrada e saída).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Incorporação de código R no script U-SQL

Pode inserção do código R o seu script U-SQL utilizando o parâmetro de comando do `Extension.R.Reducer` . Por exemplo, pode declarar o script R como uma variável de corda e passá-lo como parâmetro para o Redutor.

```usql
REFERENCE ASSEMBLY [ExtR];

DECLARE @myRScript = @"
inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
#do not return readonly columns and make sure that the column names are the same in usql and r cripts,
outputToUSQL=data.frame(summary(lm.fit)$coefficients)
colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
outputToUSQL
";

@RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, ReturnType:"dataframe");
```

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Mantenha o código R num ficheiro separado e refira-o ao script U-SQL

O exemplo a seguir ilustra uma utilização mais complexa. Neste caso, o código R é implantado como um RECURSO que é o script U-SQL.

Guarde este código R como um ficheiro separado.

```usql
load("my_model_LM_Iris.rda")
outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
```

Utilize um script U-SQL para implementar o script R com a declaração DE RECURSO DEPLOY.

```usql
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
```

## <a name="how-r-integrates-with-u-sql"></a>Como o R se integra com o U-SQL

### <a name="datatypes"></a>Tipos de dados

* As colunas de cordas e numéricas da U-SQL são convertidas como-é entre R DataFrame e U-SQL [tipos suportados: `double` , , , , ] `string` `bool` `integer` `byte` .
* O `Factor` tipo de dados não é suportado em U-SQL.
* `byte[]` deve ser serializado como uma base 64 codificada `string` .
* As cordas U-SQL podem ser convertidas em fatores em código R, uma vez que o U-SQL cria um dataframe de entrada R ou estabelece o parâmetro de redução `stringsAsFactors: true` .

### <a name="schemas"></a>Esquemas

* Os conjuntos de dados U-SQL não podem ter nomes de colunas duplicados.
* Os nomes das colunas de conjuntos de dados U-SQL devem ser cordas.
* Os nomes das colunas devem ser os mesmos nos scripts U-SQL e R.
* A coluna Readonly não pode fazer parte do data-dado de saída. Porque as colunas readonly são automaticamente injetadas na tabela U-SQL se for uma parte do esquema de saída do UDO.

### <a name="functional-limitations"></a>Limitações funcionais

* O motor R não pode ser instantâneo duas vezes no mesmo processo.
* Atualmente, o U-SQL não suporta uDOs Combiner para previsão usando modelos divididos gerados usando UDOs redutor. Os utilizadores podem declarar os modelos divididos como recurso e usá-los no seu Script R (ver código de `ExtR_PredictUsingLMRawStringReducer.usql` amostra)

### <a name="r-versions"></a>Versões R

Apenas R 3.2.2 é suportado.

### <a name="standard-r-modules"></a>Módulos R standard

```usql
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
```

### <a name="input-and-output-size-limitations"></a>Limitações do tamanho da entrada e da saída

Cada vértice tem uma quantidade limitada de memória atribuída a ele. Como os DataFrames de entrada e saída devem existir na memória no código R, o tamanho total da entrada e saída não pode exceder 500 MB.

### <a name="sample-code"></a>Código de exemplo

Mais código de amostra está disponível na sua conta Data Lake Store depois de instalar as extensões U-SQL Advanced Analytics. O caminho para mais código de amostra é: `<your_account_address>/usqlext/samples/R` .

## <a name="deploying-custom-r-modules-with-u-sql"></a>Implantação de módulos De R personalizados com U-SQL

Em primeiro lugar, crie um módulo personalizado R e feche-o e, em seguida, carre fique com o ficheiro do módulo personalizado R fechado para a sua loja ADL. No exemplo, vamos enviar magittr_1.5.zip para a raiz da conta ADLS padrão para a conta ADLA que estamos a usar. Assim que fizer o upload do módulo para a loja ADL, declare-o como recurso de utilização para o disponibilizar no seu script U-SQL e ligue `install.packages` para o instalar.

```usql
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
```

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio | Azure](data-lake-analytics-data-lake-tools-get-started.md)
* [Utilização de funções de janela U-SQL para trabalhos de Azure Data Lake Analytics](./data-lake-analytics-u-sql-get-started.md)