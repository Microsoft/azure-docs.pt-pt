---
title: Executar empregos U-SQL em Python, R e C# - Azure Data Lake Analytics
description: Aprenda a usar o código para trás com Python, R e C# para submeter o emprego no Lago de Dados Azure.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: cb3ddf0c4147fa982e8ab0f9d440292d12803d35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309710"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Desenvolva U-SQL com Python, R e C# para Azure Data Lake Analytics em Código de Estúdio Visual
Aprenda a usar o Código do Estúdio Visual (VSCode) para escrever código Python, R e C# atrás com a U-SQL e submeter empregos ao serviço Azure Data Lake. Para obter mais informações sobre as ferramentas do Lago de Dados Azure para VSCode, consulte Use as Ferramentas do [Lago de Dados Azure para código de estúdio visual](data-lake-analytics-data-lake-tools-for-vscode.md).

Antes de escrever código por trás do código personalizado, precisa de abrir uma pasta ou um espaço de trabalho no VSCode.


## <a name="prerequisites-for-python-and-r"></a>Pré-requisitos para Python e R
Registe os conjuntos de extensões Python e R para a sua conta ADL. 
1. Abra a sua conta no portal.
   - Selecione **Descrição geral**. 
   - Clique no **Script da amostra**.
2. Clique em **Mais**.
3. **Selecione Instalar extensões U-SQL**. 
4. A mensagem de confirmação é exibida após a instalação das extensões U-SQL. 

   ![Criar o ambiente para python e R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Para obter as melhores experiências sobre o serviço de linguagem Python e R, instale a extensão VSCode Python e R. 

## <a name="develop-python-file"></a>Desenvolver ficheiro Python
1. Clique no **Novo Ficheiro** no seu espaço de trabalho.
2. Escreva o seu código na U-SQL. Segue-se uma amostra de código.
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
    
3. Clique num ficheiro de script e, em seguida, selecione **ADL: Generate Python Code Behind File**. 
4. O ficheiro **xxx.usql.py** é gerado na sua pasta de trabalho. Escreva o seu código no ficheiro Python. Segue-se uma amostra de código.

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
5. Clique à direita no ficheiro **USQL,** pode clicar em **Compile Script** ou **Enviar Trabalho** para o trabalho de execução.

## <a name="develop-r-file"></a>Desenvolver ficheiro R
1. Clique no **Novo Ficheiro** no seu espaço de trabalho.
2. Escreva o seu código no ficheiro U-SQL. Segue-se uma amostra de código.
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
3. Clique à direita no ficheiro **USQL** e, em seguida, selecione **ADL: Generate R Code Behind File**. 
4. O ficheiro **xxx.usql.r** é gerado na sua pasta de trabalho. Escreva o seu código no ficheiro R. Segue-se uma amostra de código.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Clique à direita no ficheiro **USQL,** pode clicar em **Compile Script** ou **Enviar Trabalho** para o trabalho de execução.

## <a name="develop-c-file"></a>Desenvolver ficheiro C#
Um ficheiro de código por trás é um ficheiro C# associado a um único script U-SQL. Pode definir um script dedicado à UDO, UDA, UDT e UDF no ficheiro por trás do código. A UDO, UDA, UDT e UDF podem ser utilizadas diretamente no script sem registar a montagem primeiro. O ficheiro por trás do código é colocado na mesma pasta que o seu ficheiro de script U-SQL. Se o guião for nomeado xxx.usql, o código por trás é nomeado como xxx.usql.cs. Se eliminar manualmente o ficheiro por trás do código, a função por trás do código é desativada para o seu script U-SQL associado. Para obter mais informações sobre a escrita do código do cliente para script U-SQL, consulte [A Escrita e Utilização de Código Personalizado em U-SQL: Funções definidas pelo utilizador]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Clique no **Novo Ficheiro** no seu espaço de trabalho.
2. Escreva o seu código no ficheiro U-SQL. Segue-se uma amostra de código.
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
3. Clique à direita no ficheiro **USQL** e, em seguida, selecione **ADL: Gerao Código CS por trás do ficheiro**. 
4. O ficheiro **xxx.usql.cs** é gerado na sua pasta de trabalho. Escreva o seu código no ficheiro CS. Segue-se uma amostra de código.

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
5. Clique à direita no ficheiro **USQL,** pode clicar em **Compile Script** ou **Enviar Trabalho** para o trabalho de execução.

## <a name="next-steps"></a>Passos seguintes
* [Utilizar as Ferramentas do Azure Data Lake para o Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Corrida local U-SQL e depuração local com Código de Estúdio Visual](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Começar com data lake analytics usando PowerShell](data-lake-analytics-get-started-powershell.md)
* [Começar com data lake analytics usando o portal Azure](data-lake-analytics-get-started-portal.md)
* [Utilize ferramentas de data lake para estúdio visual para desenvolver aplicações U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Utilize o catálogo data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
