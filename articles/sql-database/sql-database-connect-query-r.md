---
title: Início rápido para utilizar o Machine Learning Services (com R) na Base de Dados SQL do Azure (pré-visualização) | Microsoft Docs
description: Este tópico mostra-lhe como utilizar o Machine Learning Services na Base de Dados SQL do Azure e como executar scripts em R para disponibilizar análises avançadas em escala e a capacidade de levar os cálculos e o processamento para onde os dados residem, eliminando a necessidade de extrair dados através da rede.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.reviewer: ''
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 033b853537ade927e4bb7e47c92efe1acff226d9
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247397"
---
# <a name="quickstart-use-machine-learning-services-with-r-in-azure-sql-database-preview"></a>Início rápido: Utilizar o Machine Learning Services (com R) na base de dados do Azure SQL (pré-visualização)

Este artigo explica como pode utilizar a pré-visualização pública da [Machine Learning Services (com R) na base de dados do Azure SQL](sql-database-machine-learning-services-overview.md). Orienta-o pelas noções básicas da movimentação de dados entre uma base de dados SQL e R. Também lhe explica como encapsular código R bem formatado no procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) para compilar, preparar e utilizar modelos de machine learning numa base de dados SQL.

Utilize o poder da linguagem R para fornecer análises avançadas e aprendizagem automática na base de dados. Esta capacidade traz cálculos e processamento para onde os dados residem, eliminando a necessidade de extrair os dados através da rede. Além disso, tire partido do poder dos pacotes de R do enterprise para fornecer análise avançada à escala.

Machine Learning Services inclui uma distribuição base da linguagem R, recoberto com pacotes de R enterprise da Microsoft. Funções de R e algoritmos da Microsoft estão concebidos para o dimensionamento e o utilitário, entrega de Análise Preditiva, modelação estatística, visualizações de dados e algoritmos de aprendizagem de ponta.

Se não tiver uma subscrição do Azure, [criar uma conta](https://azure.microsoft.com/free/) antes de começar.

> [!IMPORTANT]
> Serviços do Aprendizado de máquina de base de dados do Azure SQL está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> [Inscreva-se na pré-visualização](sql-database-machine-learning-services-overview.md#signup).

## <a name="prerequisites"></a>Pré-requisitos

Para executar o código de exemplo nestes exercícios, primeiro tem de ter uma base de dados SQL com o Machine Learning Services (com R) ativado. Durante a pré-visualização pública, Microsoft irá carregar e ativar o machine learning para a base de dados nova ou existente. Siga os passos em [Inscreva-se na pré-visualização](sql-database-machine-learning-services-overview.md#signup).

Pode ligar à Base de Dados SQL e executar os scripts R com qualquer ferramenta de gestão ou consulta de bases de dados, desde que essas ferramentas se possam ligar à Base de Dados SQL, e executar consultas T-SQL ou procedimentos armazenados. Este início rápido utiliza o [SQL Server Management Studio](sql-database-connect-query-ssms.md).

No exercício [adicionar um pacote](#add-package), também terá de instalar o [R](https://www.r-project.org/) e o [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) no computador local.

Este guia de início rápido também necessita que configure uma regra de firewall ao nível do servidor. Para ver um guia de início rápido que mostre como fazer isto, veja [Criar regra de firewall ao nível do servidor](sql-database-server-level-firewall-rule.md).

## <a name="verify-r-exists"></a>Verificar se R existe

Pode confirmar se o Machine Learning Services (com R) está ativado para a sua base de dados SQL. Siga os passos abaixo.

1. Abra o SQL Server Management Studio e ligue à sua base de dados SQL. Para obter mais informações sobre como ligar, consulte [início rápido: Utilizar o SQL Server Management Studio para se ligar e consultar uma base de dados SQL do Azure](sql-database-connect-query-ssms.md).

1. Execute o código abaixo. 

    ```sql
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(31 + 11)';
    GO
    ```
    Se tudo estiver bem, deverá ver uma mensagem de resultado como a seguinte.

    ```text
    STDOUT message(s) from external script: 
    42
    ```

1. Se receber um erro, poderá ser porque a pré-visualização pública do Machine Learning Services (com R) não está ativada para a sua Base de Dados SQL. Veja acima como se inscrever na pré-visualização pública.

## <a name="grant-permissions"></a>Conceder permissões

Se for um administrador, pode executar código externo automaticamente. Todos os outros devem ser concedido permissão.

Substitua `<username>` com um início de sessão de utilizador de base de dados válida antes de executar o comando.

```sql
GRANT EXECUTE ANY EXTERNAL SCRIPT TO <username>
```

## <a name="basic-r-interaction"></a>Interação básica com R

Pode executar o código R na Base de Dados SQL de duas formas:

+ Adicionar o R script como um argumento do procedimento armazenado do sistema, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).
+ Num [cliente R remoto](https://docs.microsoft.com/sql/advanced-analytics/r/set-up-a-data-science-client), ligue à Base de Dados SQL e execute o código, utilizando a Base de Dados SQL como o contexto de computação.

O exercício seguinte debruça-se sobre o primeiro modelo de interação, como transmitir o código R a um procedimento armazenado.

1. Execute um script simple para ver como um script R é executado na base de dados SQL.

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R',
    @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c, d)'
    ```

2. Partindo do princípio de que tem tudo configurado corretamente, é calculado o resultado certo e a função `print` de R devolve-o na janela **Messages** (Mensagens).

    **Resultados**

    ```text
    STDOUT message(s) from external script: 
    0.5 2
    ```

    Embora obter mensagens **stdout** seja útil para testar o código, o mais comum é ter de devolver os resultados em formato de tabela, para os poder utilizar numa aplicação ou escrever numa tabela. Veja a secção de entradas e saídas, abaixo, para obter mais informações.

Não se esqueça de que tudo o que está dentro do argumento `@script` tem de ser código R válido.

## <a name="inputs-and-outputs"></a>Entradas e saídas

Por predefinição, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) aceita um conjunto de dados de entrada individual, que, geralmente, é fornecido na forma de uma consulta SQL válida. Podem ser transmitidos outros tipos de entradas como variáveis de SQL.

O procedimento armazenado devolve um frame de dados R individual como saída, mas também pode produzir escalares e modelos como variáveis. Por exemplo, pode produzir um modelo preparado como uma variável binária e transmiti-lo para uma instrução T-SQL INSERT, para escrever esse modelo numa tabela. Também pode gerar gráficos (em formato binário) ou escalares (valores individuais, como data e hora, o tempo decorrido para preparar o modelo, etc.).

Por agora, vamos ver apenas as variáveis de entrada e saída predefinidas de sp_execute_external_script: `InputDataSet` e `OutputDataSet`.

1. Execute a seguinte instrução T-SQL para criar uma pequena tabela de dados de teste:

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    INSERT INTO RTestData VALUES (1);
    INSERT INTO RTestData VALUES (10);
    INSERT INTO RTestData VALUES (100);
    GO
    ```

    Quando a tabela estiver criada, utilize a instrução seguinte para a consultar:
  
    ```sql
    SELECT * FROM RTestData
    ```

    **Resultados**

    ![Conteúdos da tabela RTestData](./media/sql-database-connect-query-r/select-rtestdata.png)

2. Pode obter os dados da tabela como entrada para o script R. Execute a instrução abaixo. A instrução obtém os dados da tabela, faz uma viagem de ida e volta pelo runtime de R e devolve os valores com o nome de coluna *NewColName*.

    Os dados que a consulta devolve são transmitidos para o runtime de R, que os devolve à Base de Dados SQL como um frame de dados. A cláusula WITH RESULT SETS define o esquema da tabela de dados devolvida à Base de Dados SQL.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    **Resultados**

    ![Saída do script R que devolve dados de uma tabela](./media/sql-database-connect-query-r/r-output-rtestdata.png)

3. Vamos mudar o nome das variáveis de entrada e saída. O script acima utilizou os nomes predefinidos das variáveis de entrada e saída, _InputDataSet_ e _OutputDataSet_. Para definir os dados de entrada associados a  _InputDatSet_, utilizamos a variável *@input_data_1*.

    Neste script, os nomes das variáveis de entrada e saída do procedimento armazenado foram mudados para *SQL_out* e *SQL_in*:

    ```sql
    EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N' SQL_out <- SQL_in;'
      , @input_data_1 = N' SELECT 12 as Col;'
      , @input_data_1_name  = N'SQL_in'
      , @output_data_1_name =  N'SQL_out'
      WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    Tenha em conta que R é sensível a maiúsculas e minúsculas, pelo que os nomes das variáveis em `@input_data_1_name` e `@output_data_1_name` têm de ser iguais aos do código R em `@script`. 

    Além disso, a ordem dos parâmetros também é importante. Para poder utilizar os parâmetros opcionais *@input_data_1_name* e *@output_data_1_name*, tem de especificar primeiro os obrigatórios, *@input_data_1* e *@output_data_1*.

    Apenas um conjunto de dados de entrada pode ser transmitido como parâmetro e só pode ser devolvido um conjunto de dados. No entanto, pode chamar outros conjuntos de dados no código R e devolver saídas de outros tipos para além do conjunto de dados. Também pode adicionar a palavra-chave OUTPUT a qualquer parâmetro, para que seja devolvida com os resultados. 

    A instrução `WITH RESULT SETS` define o esquema dos dados que é utilizado na Base de Dados SQL. Tem de fornecer tipos de dados compatíveis com SQL para cada coluna devolvida por R. Pode utilizar a definição de esquema para fornecer também nomes de colunas novos, pois não é necessário que utilize os nomes de colunas do frame de dados de R.

4. Também pode gerar valores com o script R e deixar a cadeia de consulta de entrada em _@input_data_1_ vazia.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N' mytextvariable <- c("hello", " ", "world");
            OutputDataSet <- as.data.frame(mytextvariable);'
        , @input_data_1 = N''
    WITH RESULT SETS (([Col1] CHAR(20) NOT NULL));
    ```

    **Resultados**

    ![Consultar os resultados tendo @script como a entrada](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>Verificar a versão de R

Se quiser ver a versão de R que está instalada na sua Base de Dados SQL, faça o seguinte:

1. Execute o script abaixo na Base de Dados SQL.

    ```SQL
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(version)';
    GO
    ```

2. A função `print` de R devolve a versão na janela **Messages** (Mensagens). Na saída de exemplo abaixo, pode ver que a Base de Dados SQL neste caso tem instalada a versão de R 3.4.4.

    **Resultados**

    ```text
    STDOUT message(s) from external script:
                   _
    platform       x86_64-w64-mingw32
    arch           x86_64
    os             mingw32
    system         x86_64, mingw32
    status
    major          3
    minor          4.4
    year           2018
    month          03
    day            15
    svn rev        74408
    language       R
    version.string R version 3.4.4 (2018-03-15)
    nickname       Someone to Lean On
    ```

## <a name="list-r-packages"></a>Listar pacotes de R

A Microsoft disponibiliza vários pacotes de R pré-instalados com o Machine Learning Services na Base de Dados SQL. Para ver uma lista dos pacotes de R instalados, incluindo versão, dependências, licenças e informações do caminho da biblioteca, siga os passos abaixo. Para adicionar mais pacotes, veja a secção [adicionar um pacote](#add-package).

1. Execute o script abaixo na Base de Dados SQL.

    ```SQL
    EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
    OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
    WITH result sets((Package NVARCHAR(255), Version NVARCHAR(100), Depends NVARCHAR(4000)
        , License NVARCHAR(1000), LibPath NVARCHAR(2000)));
    ```

2. A saída é de `installed.packages()` em R e é devolvida como um conjunto de resultados.

    **Resultados**

    ![Pacotes instalados em R](./media/sql-database-connect-query-r/r-installed-packages.png)

## <a name="create-a-predictive-model"></a>Criar um modelo preditivo

Pode utilizar R para preparar um modelo e guardá-lo numa tabela na Base de Dados SQL. Neste exercício, vai preparar um modelo de regressão simples que prevê a distância de travagem de um automóvel, com base na velocidade. Verá o conjunto de dados `cars` incluído no R, porque é pequeno e fácil de compreender.

1. Comece por criar uma tabela para guardar os dados de preparação.

    ```sql
    CREATE TABLE dbo.CarSpeed (speed INT NOT NULL, distance INT NOT NULL)
    GO
    INSERT INTO dbo.CarSpeed (speed, distance)
    EXEC sp_execute_external_script
    @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

    Estão incluídos muitos conjuntos de dados, pequenos e grandes, no runtime de R. Para obter uma lista dos conjuntos de dados instalados em R, escreva `library(help="datasets")` numa linha de comandos de R.

2. Crie um modelo de regressão. Os dados de velocidade do automóvel contêm duas colunas, ambas numéricas, `dist` e `speed`. Há múltiplas observações de algumas velocidades. A partir destes dados, vai criar um modelo de regressão linear que descreve algumas relações entre a velocidade do automóvel e a distância necessária para o parar.

    Os requisitos dos modelos lineares são simples:

    - Defina uma fórmula que descreva a relação entre a variável dependente `speed` e a variável independente `distance`.

    - Forneça os dados de entrada que vão ser utilizados na preparação do modelo.

    > [!TIP]
    > Se precisar de relembrar alguns aspetos sobre os modelos de lineares, recomendamos neste tutorial, que descreve o processo de que se ajusta a um modelo usando rxLinMod: [Que se ajusta modelos Linear](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

    Para compilar o modelo, defina a fórmula dentro do código R e transmita os dados como parâmetro de entrada.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
        EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
            trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @output_data_1_name = N'trained_model'
        WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

    O primeiro argumento de rxLinMod é o parâmetro *formula*, que define a distância como estando dependente da velocidade. Os dados de entrada são armazenados na variável `CarsData`, que é preenchida pela consulta SQL. Se não atribuir um nome específico aos dados de entrada, o nome predefinido da variável será _InputDataSet_.

2. Em seguida, crie uma tabela para armazenar o modelo, de modo a poder prepara-lo outra vez ou utilizá-lo para predição. Geralmente, a saída de um pacote R que cria um modelo é um **objeto binário**. Por esse motivo, a tabela tem de fornecer uma coluna do tipo **VARBINARY(max)**.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
    );
    ```
3. Para guardar o modelo, execute a instrução Transact-SQL abaixo, para chamar o procedimento armazenado, gerar o modelo e guardá-lo numa tabela.

    ```sql
    INSERT INTO dbo.stopping_distance_models (model)
    EXEC generate_linear_model;
    ```

    Tenha em conta que, se executar este código uma segunda vez, obterá o erro:

    ```text
    Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object dbo.stopping_distance_models
    ```

    Uma opção para evitar esse erro é atualizar o nome de cada modelo novo. Por exemplo, pode mudá-lo para algo mais descritivo e incluir o tipo de modelo, o dia em que o criou, etc.

    ```sql
    UPDATE dbo.stopping_distance_models
    SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
    WHERE model_name = 'default model'
    ```

4. Geralmente, a saída de R do procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) está limitada a um único frame de dados.

    No entanto, pode devolver saídas de outros tipos, como escalares, para além do frame de dados.

    Por exemplo, imagine que quer preparar um modelo e ver, logo de seguida, uma tabela de coeficientes do modelo. Pode criar a tabela de coeficientes como o conjunto de resultados principal e produzir o modelo preparado numa variável de SQL. Pode reutilizar imediatamente o modelo ao chamar a variável ou guardá-lo numa tabela, conforme mostrado aqui.

    ```sql
    DECLARE @model VARBINARY(max), @modelname VARCHAR(30)
    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
            speedmodel <- rxLinMod(distance ~ speed, CarsData)
            modelbin <- serialize(speedmodel, NULL)
            OutputDataSet <- data.frame(coefficients(speedmodel));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @params = N'@modelbin varbinary(max) OUTPUT'
        , @modelbin = @model OUTPUT
        WITH RESULT SETS (([Coefficient] FLOAT NOT NULL));

    -- Save the generated model
    INSERT INTO dbo.stopping_distance_models(model_name, model)
    VALUES ('latest model', @model)
    ```

    **Resultados**

    ![Modelo preparado com saída adicional](./media/sql-database-connect-query-r/r-train-model-with-additional-output.png)

## <a name="predict"></a>Prever

Utilize o modelo que criou na secção anterior para classificar predições face a dados novos. Para realizar a _classificação_ com dados novos, obtenha um dos modelos preparados da tabela e chame um conjunto de dados novo no qual as predições se vão basear. “Classificação” é um termo por vezes utilizado na ciência de dados que significa gerar predições, probabilidades ou outros valores com base em dados novos introduzidos num modelo preparado.

1. Primeiro, crie uma tabela com dados de velocidade novos. Reparou que os dados de preparação originais param nos 45 quilómetros por hora? Isto deve-se ao facto de os dados originais terem por base uma experimentação de 1920! Poderá estar a pensar quanto tempo seria necessário para que um automóvel de 1920 parasse, partindo do princípio de que poderia estar a circular a 90 K/h ou até mesmo a 140. Para responder a esta pergunta, tem de indicar alguns valores de velocidade novos.

    ```sql
    CREATE TABLE dbo.NewCarSpeed(speed INT NOT NULL,
        distance INT NULL
    )
    GO
    INSERT dbo.NewCarSpeed(speed)
    VALUES (40), (50), (60), (70), (80), (90), (100)
    ```

    Neste exemplo, visto que o seu modelo se baseia no algoritmo **rxLinMod**, fornecido como parte do pacote **RevoScaleR**, vai chamar a função [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) em vez da função `predict` de R genérica.

    ```sql
    DECLARE @speedmodel varbinary(max) = 
        (SELECT model FROM dbo.stopping_distance_models WHERE model_name = 'latest model');

    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
                current_model <- unserialize(as.raw(speedmodel));
                new <- data.frame(NewCarData);
                predicted.distance <- rxPredict(current_model, new);
                str(predicted.distance);
                OutputDataSet <- cbind(new, ceiling(predicted.distance));
                '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS ((new_speed INT, predicted_distance INT));
    ```

    O script acima realiza os passos seguintes:

    + Utilize uma instrução SELECT para obter um modelo individual da tabela e transmita-o como parâmetro de entrada.

    + Depois de receber o modelo da tabela, chame a função `unserialize` no mesmo.

        > [!TIP] 
        > Veja também as [funções de serialização](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) novas que RevoScaleR fornece, as quais suportam classificação em tempo real.
    + Aplique ao modelo a função `rxPredict` com argumentos adequados e forneça os dados de entrada novos.

    + No exemplo, é adicionada a função `str` durante a fase de teste, para verificar o esquema dos dados que R está a devolver. Pode remover a instrução mais tarde.

    + Os nomes das colunas utilizados no script R não são necessariamente transmitidos para a saída do procedimento armazenado. Aqui, utilizámos a cláusula WITH RESULTS para definir alguns nomes de colunas novos.

    **Resultados**

    ![Conjunto de resultados para prever a distância de paragem](./media/sql-database-connect-query-r/r-predict-stopping-distance-resultset.png)

    Também é possível utilizar [PREDICT no Transact-SQL](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql) para gerar um valor previsto ou uma classificação com base num modelo armazenado.

<a name="add-package"></a>

## <a name="add-a-package"></a>Adicionar um pacote

Se precisar de utilizar um pacote que ainda não esteja instalado na sua Base de Dados SQL, pode instalá-lo com [sqlmlutils](https://github.com/Microsoft/sqlmlutils). Siga os passos abaixo para instalar o pacote.

1. Transfira o ficheiro zip **sqlmlutils** mais recente em [github.com/Microsoft/sqlmlutils/tree/master/R/dist](https://github.com/Microsoft/sqlmlutils/tree/master/R/dist) para o computador local. Não tem de deszipar o ficheiro.

1. Se não tiver o R instalado, transfira-o em [www.r-project.org](https://www.r-project.org/) e instale-o no computador local. O R está disponível para Windows, MacOS e Linux. Neste exemplo, estamos a utilizar o Windows.

1. Primeiro, instale o pacote **RODBCext**, que é um pré-requisito de **sqlmlutils**. **RODBCext** também instala a dependência **RODBC**. Abra uma **Linha de Comandos** e execute o comando abaixo:

    ```
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    ```

    Se receber o erro seguinte, "'R' não é reconhecido como um comando interno ou externo, programa operável ou arquivo em lote", provavelmente significa que o caminho para R.exe não está incluído no seu **caminho** variável de ambiente Windows. Pode adicionar o diretório para a variável de ambiente ou navegue para o diretório no prompt de comando (por exemplo `cd C:\Program Files\R\R-3.5.1\bin`) antes de executar o comando.

1. Utilize o comando **R CMD INSTALL** para instalar **sqlmlutils**. Especifique o caminho para o diretório para o qual transferiu o ficheiro zip, bem como o nome do ficheiro. Por exemplo:

    ```
    R CMD INSTALL C:\Users\youruser\Downloads\sqlmlutils_0.5.0.zip
    ```

    A saída recebida deve ser semelhante ao seguinte:

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/youruser/Documents/R/win-library/3.5'
    package 'sqlmlutils' successfully unpacked and MD5 sums checked
    ```

1. Neste exemplo, o RStudio Desktop vai ser utilizado como o IDE. Se preferir, pode utilizar outro IDE. Se ainda não tiver o RStudio Desktop instalado, transfira-o e instale-o a partir de [www.rstudio.com/products/rstudio/download/](https://www.rstudio.com/products/rstudio/download/).

1. Abra o **RStudio** e crie um ficheiro **R Script** novo. Utilize o código R seguinte para instalar um pacote com sqlmlutils. No exemplo abaixo, vai instalar o pacote [glue](https://cran.r-project.org/web/packages/glue/), que consegue formatar e interpolar cadeias.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!NOTE]
    > O **âmbito** pode ser **PÚBLICO** ou **PRIVADO**. O âmbito público é útil para o administrador da base de dados instalar pacotes que todos os utilizadores podem utilizar. O âmbito privado faz com que o pacote só esteja disponível para o utilizador que o instala. Se não indicar o âmbito, a predefinição é **PRIVADO**.

1. Agora, confirme que o pacote **glue** foi instalado.

    ```R
    r<-sql_installed.packages(connectionString = connection, fields=c("Package", "LibPath", "Attributes", "Scope"))
    View(r)
    ```

    **Resultados**

    ![Conteúdos da tabela RTestData](./media/sql-database-connect-query-r/r-verify-package-install.png)


1. Quando o pacote estiver instalado, pode utilizá-lo no script R com **sp_execute_external_script**. Abra o **SQL Server Management Studio** e ligue à sua base de dados SQL. Execute o seguintes script:

    ```sql
    EXEC sp_execute_external_script @language = N'R'
    , @script = N'
    library(glue)

    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
        ''my age next year is {age + 1}, '',
        ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    print(text)
    ';
    ```

    Verá o resultado seguinte no separador Messages (Mensagens).

    **Resultados**

    ```text
    STDOUT message(s) from external script:
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

1. Se pretender remover o pacote, execute o script R abaixo no **RStudio** no computador local.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC") 
    ```

> [!NOTE]
> Outra forma de instalar os pacotes R na Base de Dados SQL é carregá-los a partir do fluxo de bytes com [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Machine Learning Services, consulte os artigos abaixo. Embora alguns dos seguintes artigos para o SQL Server, a maioria das informações também é aplicável ao Machine Learning Services (com R) na base de dados do Azure SQL.

- [SQL do Azure da base de dados serviços de Machine Learning (com R)](sql-database-machine-learning-services-overview.md)
- [Serviços de Machine Learning do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Tutorial: Aprenda a análise de na base de dados com o R no SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [End-to-end data science walkthrough for R and SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough) (Instruções completas de ciência de dados para R e SQL Server)
- [Tutorial: Utilizar funções de RevoScaleR R com dados do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
