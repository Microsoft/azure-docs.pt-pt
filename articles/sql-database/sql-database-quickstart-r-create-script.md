---
title: Criar e executar scripts R simples
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Executar scripts R simples em Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização).
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
ROBOTS: NOINDEX
ms.openlocfilehash: bb6cb6d86933166d2427788d697d9cd38cf04bf0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81460176"
---
# <a name="quickstart-create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Quickstart: Crie e execute scripts R simples em Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)

Neste arranque rápido, cria e executa um conjunto de scripts R utilizando serviços de aprendizagem automática (com R) na Base de Dados Azure SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma [base de dados Azure SQL](sql-database-single-database-get-started.md) com uma regra de firewall ao [nível do servidor](sql-database-server-level-firewall-rule.md)
- [Serviços de Aprendizagem automática](sql-database-machine-learning-services-overview.md) com R ativado.
- [Estúdio de Gestão de Servidores SQL](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

Este exemplo utiliza o procedimento armazenado [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) para embrulhar um script R bem formado.

## <a name="run-a-simple-script"></a>Executar um roteiro simples

Para executar um script R, passará-o como um argumento para o procedimento armazenado pelo sistema, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

Nos seguintes passos, você executará este exemplo script R na sua base de dados SQL:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Abra o **Estúdio de Gestão de Servidores SQL** e ligue-se à sua base de dados SQL.

   Se precisar de ajuda para se ligar, consulte [Quickstart: Use o Estúdio de Gestão de Servidores SQL para ligar e consultar uma base de dados Azure SQL](sql-database-connect-query-ssms.md).

1. Passe o script R completo para o [procedimento sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) armazenado.

   O guião é `@script` passado através do argumento. Tudo dentro `@script` do argumento deve ser válido código R.

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

   Se receber um erro, poderá ser porque a pré-visualização pública do Machine Learning Services (com R) não está ativada para a sua Base de Dados SQL. Consulte [os pré-requisitos](#prerequisites) acima.

   > [!NOTE]
   > Se for administrador, pode executar código externo automaticamente. Pode conceder permissão a outros utilizadores que utilizem o comando:
   <br>**CONCESSÃO EXECUTAR QUALQUER SCRIPT EXTERNO ao** * \<\>nome de utilizador*.

2. O resultado correto é `print` calculado e a função R devolve o resultado à janela **Mensagens.**

   Deve ser algo assim.

    **Resultados**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Executar um roteiro hello world

Um típico exemplo script é aquele que apenas produz a corda "Hello World". Execute o seguinte comando.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

As inputs deste procedimento armazenado incluem:

| | |
|-|-|
| @language | define a extensão da linguagem para chamar, neste caso, R |
| @script | define os comandos passados para o tempo de execução R. Todo o seu script R deve ser incluído neste argumento, como texto Unicode. Você também poderia adicionar o texto a uma variável de **tipo nvarchar** e, em seguida, chamar a variável |
| @input_data_1 | dados devolvidos pela consulta, passados para o tempo de execução R, que devolve os dados ao SQL Server como um quadro de dados |
|COM CONJUNTOS DE RESULTADOS | cláusula define o esquema da tabela de dados devolvida para o SQL Server, adicionando "Hello World" como nome da coluna, **int** para o tipo de dados |

O comando envia o seguinte texto:

| Olá, Mundo |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Utilizar entradas e saídas

Por predefinição, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) aceita um único conjunto de dados como entrada, que normalmente fornece sob a forma de uma consulta SQL válida. Em seguida, devolve um único quadro de dados R como saída.

Por enquanto, vamos utilizar as variáveis de entrada e saída predefinidas de [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql): **InputDataSet** e **OutputDataSet**.

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

1. Use `SELECT` o depoimento para consultar a mesa.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Resultados**

    ![Conteúdos da tabela RTestData](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. Executar o seguinte guião R. Recupera os dados da tabela `SELECT` utilizando a declaração, passa-os através do tempo de execução R e devolve os dados como um quadro de dados. A `WITH RESULT SETS` cláusula define o esquema da tabela de dados devolvida para a Base de Dados SQL, adicionando o nome de coluna *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Resultados**

    ![Saída do script R que devolve dados de uma tabela](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. Agora vamos mudar os nomes das variáveis de entrada e saída. Os nomes variáveis de entrada padrão e de saída são **InputDataSet** e **OutputDataSet,** este script muda os nomes para **SQL_in** e **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Note que R é sensível a casos. As variáveis de entrada e saída utilizadas no script R **(SQL_out,** **SQL_in**) precisam de corresponder aos valores definidos `@input_data_1_name` `@output_data_1_name`e, incluindo caso.

   > [!TIP]
   > Apenas um conjunto de dados de entrada pode ser transmitido como parâmetro e só pode ser devolvido um conjunto de dados. No entanto, pode chamar outros conjuntos de dados no código R e devolver saídas de outros tipos para além do conjunto de dados. Também pode adicionar a palavra-chave OUTPUT a qualquer parâmetro, para que seja devolvida com os resultados.

1. Também pode gerar valores usando o script R`@input_data_1` sem dados de entrada (está definido em branco).

   O seguinte guião produz o texto "olá" e "mundo".

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

    ![Consultar os resultados tendo @script como a entrada](./media/sql-database-quickstart-r-create-script/r-data-generated-output.png)

## <a name="check-r-version"></a>Verificar a versão de R

Se quiser ver qual a versão de R instalada na sua base de dados SQL, execute o seguinte script.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

A função `print` de R devolve a versão na janela **Messages** (Mensagens). Na saída de exemplo abaixo, pode ver que a Base de Dados SQL neste caso tem a versão R 3.4.4 instalada.

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

Para ver uma lista dos quais os pacotes R são instalados, incluindo versão, dependências, licença e informações do caminho da biblioteca, executar o seguinte script.

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

A saída `installed.packages()` é de R e é devolvida como resultado definido.

**Resultados**

![Pacotes instalados em R](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>Passos seguintes

Para criar um modelo de aprendizagem automática utilizando R na Base de Dados SQL, siga este arranque rápido:

> [!div class="nextstepaction"]
> [Criar e treinar um modelo preditivo em R com serviços de machine learning de base de dados Azure SQL (pré-visualização)](sql-database-quickstart-r-train-score-model.md)

Para obter mais informações sobre os Serviços de Machine Learning de Máquinas de Base de Dados Azure SQL com R (pré-visualização), consulte os seguintes artigos.

- [Serviços de Machine Learning de Base de Dados Azure SQL com R (pré-visualização)](sql-database-machine-learning-services-overview.md)
- [Escreva funções R avançadas na Base de Dados Azure SQL utilizando serviços de aprendizagem automática (pré-visualização)](sql-database-machine-learning-services-functions.md)
- [Trabalhar com dados R e SQL nos Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)](sql-database-machine-learning-services-data-issues.md)
