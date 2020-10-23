---
title: Executar empregos U-SQL em Python, R e C# - Azure Data Lake Analytics
description: Aprenda a usar códigos com Python, R e C# para submeter trabalho no Lago de Dados Azure.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/22/2017
ms.custom: devx-track-python
ms.openlocfilehash: d6066bd6ec2a4c986ae17ad0cce3e7f6f73b21e7
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92219977"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Desenvolver U-SQL com Python, R e C# para Azure Data Lake Analytics em Código de Estúdio Visual
Aprenda a usar o Código do Estúdio Visual (VSCode) para escrever o código Python, R e C# atrás com o U-SQL e submeta empregos ao serviço Azure Data Lake. Para obter mais informações sobre as ferramentas do Lago de Dados Azure para VSCode, consulte [as ferramentas do Lago de Dados Azure para código de estúdio visual.](data-lake-analytics-data-lake-tools-for-vscode.md)

Antes de escrever código de código personalizado, tem de abrir uma pasta ou um espaço de trabalho no VSCode.


## <a name="prerequisites-for-python-and-r"></a>Pré-requisitos para Python e R
Registe os conjuntos de extensões R para a sua conta ADL. 
1. Abra a sua conta no portal.
   - Selecione **Descrição geral**. 
   - Clique no **Script sample.**
2. Clique em **Mais**.
3. Selecione **instalar extensões U-SQL**. 
4. A mensagem de confirmação é apresentada após a instalação das extensões U-SQL. 

   ![Configurar o ambiente para python e R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Para melhores experiências no serviço de língua Python e R, instale a extensão VSCode Python e R. 

## <a name="develop-python-file"></a>Desenvolver ficheiro Python
1. Clique no **Novo Arquivo** no seu espaço de trabalho.
2. Escreva o seu código em U-SQL. O seguinte é uma amostra de código.
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
    
3. Clique com o direito num ficheiro de script e, em seguida, selecione **ADL: Gere o código python por trás do ficheiro**. 
4. O **ficheiro xxx.usql.py** é gerado na sua pasta de trabalho. Escreve o teu código no ficheiro Python. O seguinte é uma amostra de código.

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
5. Clique com o botão direito no ficheiro **USQL,** pode clicar em **Compile Script** ou **submeter Job** a executar trabalho.

## <a name="develop-r-file"></a>Desenvolver ficheiro R
1. Clique no **Novo Arquivo** no seu espaço de trabalho.
2. Escreva o seu código no ficheiro U-SQL. O seguinte é uma amostra de código.
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
3. Clique com o botão direito no ficheiro **USQL** e, em seguida, selecione **ADL: Gerar código R por trás do ficheiro**. 
4. O ficheiro **xxx.usql.r** é gerado na sua pasta de trabalho. Escreva o seu código no ficheiro R. O seguinte é uma amostra de código.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Clique com o botão direito no ficheiro **USQL,** pode clicar em **Compile Script** ou **submeter Job** a executar trabalho.

## <a name="develop-c-file"></a>Desenvolver ficheiro C#
Um ficheiro por trás de código é um ficheiro C# associado a um único script U-SQL. Pode definir um script dedicado à UDO, UDA, UDT e UDF no ficheiro code-behind. O UDO, UDA, UDT e UDF podem ser usados diretamente no script sem registar primeiro a montagem. O ficheiro por detrás de código é colocado na mesma pasta que o seu ficheiro de script U-SQL. Se o script for nomeado xxx.usql, o código-traseiro é nomeado como xxx.usql.cs. Se eliminar manualmente o ficheiro por trás do código, a função de código-de-trás é desativada para o seu script U-SQL associado. Para obter mais informações sobre a escrita do código do cliente para script U-SQL, consulte [escrita e utilização de código personalizado em U-SQL: User-Defined Funções]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Clique no **Novo Arquivo** no seu espaço de trabalho.
2. Escreva o seu código no ficheiro U-SQL. O seguinte é uma amostra de código.
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
3. Clique com o botão direito no ficheiro **USQL** e, em seguida, selecione **ADL: Gere o código CS por trás do ficheiro**. 
4. O **ficheiro xxx.usql.cs** é gerado na sua pasta de trabalho. Escreva o seu código no ficheiro CS. O seguinte é uma amostra de código.

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
5. Clique com o botão direito no ficheiro **USQL,** pode clicar em **Compile Script** ou **submeter Job** a executar trabalho.

## <a name="next-steps"></a>Passos seguintes
* [Utilizar as Ferramentas do Azure Data Lake para o Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [U-SQL corrida local e depurar local com Código de Estúdio Visual](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Começar com data lake analytics usando PowerShell](data-lake-analytics-get-started-powershell.md)
* [Começar com data lake analytics usando o portal Azure](data-lake-analytics-get-started-portal.md)
* [Utilize ferramentas do Data Lake para o Estúdio Visual para o desenvolvimento de aplicações U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Use o catálogo Data Lake Analytics (U-SQL)](./data-lake-analytics-u-sql-get-started.md)