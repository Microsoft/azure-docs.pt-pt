---
title: Criar e executar scripts simples do R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Execute scripts simples do R no SQL da base de dados serviços Azure Machine Learning (pré-visualização).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: ada09959391c551a9eff4d96b186be29c1e3b7a8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013267"
---
# <a name="create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Criar e executar scripts simples do R no SQL da base de dados serviços Azure Machine Learning (pré-visualização)

Neste início rápido, irá criar e executar um conjunto de scripts R simples, com a pré-visualização pública da [Machine Learning Services (com R) na base de dados do Azure SQL](sql-database-machine-learning-services-overview.md). Ficará a saber como pode encapsular um script R bem formado no procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) e execute o script numa base de dados SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, [criar uma conta](https://azure.microsoft.com/free/) antes de começar.

- Para executar o código de exemplo Nestes exercícios, primeiro tem de ter uma SQL database do Azure com o Machine Learning Services (com R) ativada. Durante a pré-visualização pública, Microsoft irá carregar e ativar o machine learning para a base de dados nova ou existente. Siga os passos em [Inscreva-se na pré-visualização](sql-database-machine-learning-services-overview.md#signup).

- Certifique-se de que instalou a versão mais recente [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Pode executar scripts R com outro gestão de bases de dados ou ferramentas de consulta, mas este início rápido irá utilizar o SSMS.

- Este início rápido requer que configure uma regra de firewall ao nível do servidor. Para obter informações sobre como fazer isso, consulte [criar regra de firewall ao nível do servidor](sql-database-server-level-firewall-rule.md).

## <a name="run-a-simple-script"></a>Executar um script simple

Para executar um script R, vai passá-lo como um argumento para o procedimento armazenado do sistema, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

Nos passos seguintes, irá executar este script de exemplo R na sua base de dados SQL:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Abra o **SQL Server Management Studio** e ligue à sua base de dados SQL.

   Se precisar de ajuda com a ligação, veja [início rápido: Utilizar o SQL Server Management Studio para se ligar e consultar uma base de dados SQL do Azure](sql-database-connect-query-ssms.md).

1. Passar o script R completo para o [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) procedimento armazenado.

   O script é transmitido a `@script` argumento. Tudo dentro do `@script` argumento tem de ser um código de R válido.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

   Se receber um erro, poderá ser porque a pré-visualização pública do Machine Learning Services (com R) não está ativada para a sua Base de Dados SQL. Ver [pré-requisitos](#prerequisites) acima.

   > [!NOTE]
   > Se for um administrador, pode executar código externo automaticamente. Pode conceder permissão a outros utilizadores com o comando:
   <br>**CONCESSÃO de executar qualquer externo SCRIPT para**  *\<nome de utilizador\>*.

2. O resultado correto é calculado e o R `print` função devolve o resultado para o **mensagens** janela.

   ele deve ter um aspeto semelhante ao seguinte.

    **Resultados**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Executar um script de Hello World

Um script de exemplo típico é aquele que apenas produz a cadeia de caracteres "Hello World". Execute o seguinte comando.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

Entradas para este procedimento armazenado incluem:

| | |
|-|-|
|*@language* | Define a extensão de linguagem para chamar, neste caso, o R |
|*@script* | Define os comandos transmitidos para o runtime do R. O script R inteiro deve estar entre este argumento, como texto Unicode. Também pode adicionar o texto a uma variável do tipo **nvarchar** e, em seguida, chamar a variável |
|*@input_data_1* | dados devolvidos pela consulta, passado para o runtime do R, que retorna os dados para o SQL Server como um quadro de dados |
|COM CONJUNTOS DE RESULTADOS | cláusula define o esquema da tabela de dados devolvidos para o SQL Server, a adição de "Hello World" como o nome da coluna **int** para o tipo de dados |

O comando produz o seguinte texto:

| Olá, Mundo |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Utilização de entradas e saídas

Por predefinição, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) aceita um único conjunto de dados como entrada, que normalmente fornecer sob a forma de uma consulta SQL válida. Em seguida, ele retorna um único quadro de dados do R como saída.

Apenas um conjunto de dados de entrada pode ser transmitido como parâmetro e só pode ser devolvido um conjunto de dados. No entanto, pode chamar outros conjuntos de dados no código R e devolver saídas de outros tipos para além do conjunto de dados. Também pode adicionar a palavra-chave OUTPUT a qualquer parâmetro, para que seja devolvida com os resultados.

Por enquanto, vamos usar a entrada padrão e variáveis de saída [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql): **InputDataSet** e **OutputDataSet**.

1. Crie uma pequena tabela de dados de teste.

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    
    INSERT INTO RTestData
    VALUES (1);
    
    INSERT INTO RTestData
    VALUES (10);
    
    INSERT INTO RTestData
    VALUES (100);
    GO
    ```

1. Utilize o `SELECT` instrução para consultar a tabela.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Resultados**

    ![Conteúdos da tabela RTestData](./media/sql-database-connect-query-r/select-rtestdata.png)

1. Execute o seguinte script do R. Ele obtém os dados a partir da tabela com o `SELECT` instrução, ele passa o tempo de execução do R e retorna os dados como um quadro de dados. O `WITH RESULT SETS` cláusula define o esquema da tabela de dados retornados para a base de dados SQL, adicionar o nome da coluna *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Resultados**

    ![Saída do script R que devolve dados de uma tabela](./media/sql-database-connect-query-r/r-output-rtestdata.png)

1. Agora vamos alterar os nomes das variáveis de entrada e saídas. A predefinição de entrada e os nomes de variáveis de saída são **InputDataSet** e **OutputDataSet**, o script altera os nomes a **SQL_in** e **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Tenha em atenção que o R diferencia maiúsculas de minúsculas. As variáveis de entrada e de saída utilizadas no R script (**SQL_out**, **SQL_in**) têm de corresponder os valores definidos com `@input_data_1_name` e `@output_data_1_name`, incluindo o caso.

   > [!TIP]
   > Apenas um conjunto de dados de entrada pode ser transmitido como parâmetro e só pode ser devolvido um conjunto de dados. No entanto, pode chamar outros conjuntos de dados no código R e devolver saídas de outros tipos para além do conjunto de dados. Também pode adicionar a palavra-chave OUTPUT a qualquer parâmetro, para que seja devolvida com os resultados.

1. Também pode gerar valores apenas usando o script R com dados de entrada (`@input_data_1` está definido para em branco).

   O seguinte script produz o texto "Olá" e "world".

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Resultados**

    ![Consultar os resultados tendo @script como a entrada](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>Verificar a versão de R

Se gostaria de ver qual é a versão do R está instalada na sua base de dados SQL, execute o seguinte script.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

A função `print` de R devolve a versão na janela **Messages** (Mensagens). O resultado de exemplo abaixo, pode ver que a base de dados SQL tem nesse caso R versão 3.4.4 instalado.

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

A Microsoft disponibiliza vários pacotes de R pré-instalados com o Machine Learning Services na Base de Dados SQL.

Para ver uma lista do que R pacotes forem instalados, incluindo a versão, dependências, licença e informações de caminho de biblioteca, execute o seguinte script.

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

O resultado é de `installed.packages()` em R e é devolvido como resultado conjunto.

**Resultados**

![Pacotes instalados em R](./media/sql-database-connect-query-r/r-installed-packages.png)

## <a name="next-steps"></a>Passos Seguintes

Para criar um modelo de aprendizagem automática, utilizar R na base de dados SQL, siga este guia de introdução:

> [!div class="nextstepaction"]
> [Criar e formar um modelo preditivo em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)](sql-database-quickstart-r-train-score-model.md)

Para obter mais informações sobre o Machine Learning Services, consulte os artigos abaixo. Embora alguns dos seguintes artigos para o SQL Server, a maioria das informações também é aplicável ao Machine Learning Services (com R) na base de dados do Azure SQL.

- [SQL do Azure da base de dados serviços de Machine Learning (com R)](sql-database-machine-learning-services-overview.md)
- [Serviços de Machine Learning do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Tutorial: Aprenda a análise de na base de dados com o R no SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [End-to-end data science walkthrough for R and SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough) (Instruções completas de ciência de dados para R e SQL Server)
- [Tutorial: Utilizar funções de RevoScaleR R com dados do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
