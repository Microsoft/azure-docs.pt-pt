---
title: Executar trabalhos do U-SQL em Python, R e C# -Azure data Lake Analytics
description: Saiba como usar o code-behind com Python, R C# e para enviar trabalho em Azure data Lake.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: cb3ddf0c4147fa982e8ab0f9d440292d12803d35
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309710"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Desenvolva o U-SQL com Python, R C# e para Azure Data Lake Analytics no Visual Studio Code
Saiba como usar o Visual Studio Code (VSCode) para escrever python, R e C# code-behind com U-SQL e enviar trabalhos para Azure data Lake serviço. Para obter mais informações sobre as ferramentas de Azure Data Lake para VSCode, consulte [usar o código de ferramentas do Azure data Lake para Visual Studio](data-lake-analytics-data-lake-tools-for-vscode.md).

Antes de escrever código personalizado por trás do código, você precisa abrir uma pasta ou um espaço de trabalho no VSCode.


## <a name="prerequisites-for-python-and-r"></a>Pré-requisitos para Python e R
Registre assemblies de extensões do Python e do R para sua conta do ADL. 
1. Abra sua conta no Portal.
   - Selecione **Descrição geral**. 
   - Clique em **script de exemplo**.
2. Clique em **mais**.
3. Selecione **instalar extensões U-SQL**. 
4. A mensagem de confirmação é exibida depois que as extensões do U-SQL são instaladas. 

   ![Configurar o ambiente para Python e R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Para obter as melhores experiências no Python e no serviço de linguagem R, instale a extensão VSCode Python e R. 

## <a name="develop-python-file"></a>Desenvolver arquivo Python
1. Clique no **novo arquivo** em seu espaço de trabalho.
2. Escreva seu código no U-SQL. Veja a seguir um exemplo de código.
    ```U-SQL
    REFERENCE ASSEMBLY [ExtPython];
    @t  = 
        SELECT * FROM 
        (VALUES
            ("D1","T1","A1","@foo Hello World @bar"),
            ("D2","T2","A2","@baz Hello World @beer")
        ) AS 
            D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer("pythonSample.usql.py", pyVersion : "3.5.1");

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();
    ```
    
3. Clique com o botão direito do mouse em um arquivo **de script e selecione ADL: Gere o arquivo**code-behind do Python. 
4. O arquivo **xxx.usql.py** é gerado em sua pasta de trabalho. Escreva seu código no arquivo Python. Veja a seguir um exemplo de código.

    ```Python
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ```
5. Clique com o botão direito do mouse no arquivo **USQL** , você pode clicar em **Compilar script** ou **Enviar trabalho** para executar o trabalho.

## <a name="develop-r-file"></a>Desenvolver arquivo R
1. Clique no **novo arquivo** em seu espaço de trabalho.
2. Escreva seu código no arquivo U-SQL. Veja a seguir um exemplo de código.
    ```U-SQL
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT SepalLength double,
                SepalWidth double,
                PetalLength double,
                PetalWidth double,
                Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput =
        REDUCE @ExtendedData
        ON Par
        PRODUCE Par,
                fit double,
                lwr double,
                upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile : "RClusterRun.usql.R", rReturnType : "dataframe", stringsAsFactors : false);
    OUTPUT @RScriptOutput
    TO @OutputFilePredictions
    USING Outputters.Tsv();
    ```
3. Clique com o botão direito do mouse no arquivo USQL **e selecione ADL: Gerar arquivo**code-behind do R. 
4. O arquivo **xxx. usql. r** é gerado em sua pasta de trabalho. Escreva seu código no arquivo R. Veja a seguir um exemplo de código.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Clique com o botão direito do mouse no arquivo **USQL** , você pode clicar em **Compilar script** ou **Enviar trabalho** para executar o trabalho.

## <a name="develop-c-file"></a>Desenvolver C# arquivo
Um arquivo code-behind é um C# arquivo associado a um único script U-SQL. Você pode definir um script dedicado a UDO, UDA, UDT e UDF no arquivo code-behind. O UDO, o UDA, o UDT e o UDF podem ser usados diretamente no script sem registrar o assembly primeiro. O arquivo code-behind é colocado na mesma pasta que o arquivo de script U-SQL de emparelhamento. Se o script for denominado xxx. usql, o code-behind será nomeado como xxx.usql.cs. Se você excluir manualmente o arquivo code-behind, o recurso code-behind será desabilitado para seu script U-SQL associado. Para obter mais informações sobre como escrever código de cliente para o script u [-SQL, consulte escrevendo e usando código personalizado no U-SQL: Funções]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/)definidas pelo usuário.

1. Clique no **novo arquivo** em seu espaço de trabalho.
2. Escreva seu código no arquivo U-SQL. Veja a seguir um exemplo de código.
    ```U-SQL
    @a = 
        EXTRACT 
            Iid int,
        Starts DateTime,
        Region string,
        Query string,
        DwellTime int,
        Results string,
        ClickedUrls string 
        FROM @"/Samples/Data/SearchLog.tsv" 
        USING Extractors.Tsv();

    @d =
        SELECT DISTINCT Region 
        FROM @a;

    @d1 = 
        PROCESS @d
        PRODUCE 
            Region string,
        Mkt string
        USING new USQLApplication_codebehind.MyProcessor();

    OUTPUT @d1 
        TO @"/output/SearchLogtest.txt" 
        USING Outputters.Tsv();
    ```
3. Clique com o botão direito do mouse no arquivo USQL **e selecione ADL: Gerar arquivo**code-behind de cs. 
4. O arquivo **xxx.usql.cs** é gerado em sua pasta de trabalho. Escreva seu código no arquivo CS. Veja a seguir um exemplo de código.

    ```CS
    namespace USQLApplication_codebehind
    {
        [SqlUserDefinedProcessor]

        public class MyProcessor : IProcessor
        {
            public override IRow Process(IRow input, IUpdatableRow output)
            {
                output.Set(0, input.Get<string>(0));
                output.Set(1, input.Get<string>(0));
                return output.AsReadOnly();
            } 
        }
    }
    ```
5. Clique com o botão direito do mouse no arquivo **USQL** , você pode clicar em **Compilar script** ou **Enviar trabalho** para executar o trabalho.

## <a name="next-steps"></a>Passos seguintes
* [Utilizar as Ferramentas do Azure Data Lake para o Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Execução local e depuração local do U-SQL com Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Introdução ao Data Lake Analytics usando o PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução ao Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Use as ferramentas de Data Lake para o Visual Studio para desenvolver aplicativos U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Usar catálogo Data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
