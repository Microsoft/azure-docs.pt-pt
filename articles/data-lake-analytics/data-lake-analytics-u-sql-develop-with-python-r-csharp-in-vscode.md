---
title: Desenvolver o U-SQL com o Python, R e c# para o Azure Data Lake Analytics no Visual Studio Code
description: Saiba como utilizar o code-behind com Python, R e c# para submeter a tarefa no Azure Data Lake.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: 6c234ad6756f4e65e172bf0ffc0ae5a1d35d109b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087487"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Desenvolver o U-SQL com o Python, R e c# para o Azure Data Lake Analytics no Visual Studio Code
Saiba como utilizar o Visual Studio Code (VSCode) para escrever o Python, R e c# o código por trás com U-SQL e submeter tarefas para o serviço Azure Data Lake. Para obter mais informações sobre o Azure Data Lake Tools para VSCode, consulte [utilizar o Azure Data Lake Tools para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Antes de escrever código personalizado de code-behind, precisa abrir uma pasta ou uma área de trabalho no VSCode.


## <a name="prerequisites-for-python-and-r"></a>Pré-requisitos para o Python e R
Registre-se de assemblies de extensões de Python e R para a sua conta do ADL. 
1. Abra a sua conta no portal.
   - Selecione **Descrição geral**. 
   - Clique em **Script de exemplo**.
2. Clique em **mais**.
3. Selecione **instalar as extensões U-SQL**. 
4. É apresentada a mensagem de confirmação depois de instalar as extensões de U-SQL. 

   ![Configurar o ambiente para o python e R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Para melhores experiências no serviço de linguagem Python e R, instale a extensão VSCode Python e R. 

## <a name="develop-python-file"></a>Desenvolver o ficheiro de Python
1. Clique nas **novo ficheiro** na sua área de trabalho.
2. Escreva o código no U-SQL. Segue-se um exemplo de código.
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
    
3. Um ficheiro de script com o botão direito e, em seguida, selecione **ADL: Gerar Python ficheiro Code-Behind**. 
4. O **xxx.usql.py** ficheiro é gerado na sua pasta de trabalho. Escreva o código no ficheiro de Python. Segue-se um exemplo de código.

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
5. Com o botão direito na **u-SQL** arquivo, pode clicar em **compilar o Script** ou **submeter tarefa** para executar a tarefa.

## <a name="develop-r-file"></a>Desenvolver o ficheiro de R
1. Clique nas **novo ficheiro** na sua área de trabalho.
2. Escreva o código no ficheiro de U-SQL. Segue-se um exemplo de código.
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
3. Com o botão direito na **u-SQL** do ficheiro e, em seguida, selecione **ADL: Gerar R ficheiro Code-Behind**. 
4. O **xxx.usql.r** ficheiro é gerado na sua pasta de trabalho. Escreva o código no ficheiro de R. Segue-se um exemplo de código.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Com o botão direito na **u-SQL** arquivo, pode clicar em **compilar o Script** ou **submeter tarefa** para executar a tarefa.

## <a name="develop-c-file"></a>Desenvolver o arquivo em c#
Um arquivo de code-behind é um arquivo c# associado a um único script de U-SQL. Pode definir um script dedicado UDO, UDA, UDT e UDF no arquivo code-behind. O UDO, UDA, UDT e UDF podem ser utilizado diretamente no script sem primeiro a registar o assembly. O arquivo de code-behind é colocado na mesma pasta que seu arquivo de script de U-SQL peering. Se o script se chama xxx.usql, o code-behind é nomeado como xxx.usql.cs. Se eliminar manualmente o arquivo de code-behind, a funcionalidade de código-behind está desativada para o seu script de U-SQL associado. Para obter mais informações sobre como escrever código de cliente para o script U-SQL, consulte [escrita e usando o código de personalizado no U-SQL: Funções definidas pelo utilizador]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Clique nas **novo ficheiro** na sua área de trabalho.
2. Escreva o código no ficheiro de U-SQL. Segue-se um exemplo de código.
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
3. Com o botão direito na **u-SQL** do ficheiro e, em seguida, selecione **ADL: Gerar CS ficheiro Code-Behind**. 
4. O **xxx.usql.cs** ficheiro é gerado na sua pasta de trabalho. Escreva o código no ficheiro de CS. Segue-se um exemplo de código.

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
5. Com o botão direito na **u-SQL** arquivo, pode clicar em **compilar o Script** ou **submeter tarefa** para executar a tarefa.

## <a name="next-steps"></a>Passos Seguintes
* [Utilizar as Ferramentas do Azure Data Lake para o Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Execução local do U-SQL e depuração local com o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Introdução ao Data Lake Analytics com o PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução ao Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Utilizar ferramentas do Data Lake para Visual Studio para desenvolver aplicações U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Catálogo de Analytics(U-SQL) utilizar Data Lake](data-lake-analytics-use-u-sql-catalog.md)
