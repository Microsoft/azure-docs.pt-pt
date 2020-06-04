---
title: Criar e executar scripts R simples
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Executar scripts R simples em Azure SQL Database Machine Learning Services (pré-visualização).
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
ms.openlocfilehash: 9b78b696b42431c744c30c91a730fdc7ec8c1032
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324611"
---
# <a name="quickstart-create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Quickstart: Criar e executar scripts R simples em Azure SQL Database Machine Learning Services (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste arranque rápido, você cria e executou um conjunto de scripts R usando Serviços de Machine Learning (com R) na Base de Dados Azure SQL.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma [base de dados na Base de Dados Azure SQL](single-database-create-quickstart.md) com uma [regra de firewall de nível de servidor](firewall-create-server-level-portal-quickstart.md)
- [Serviços de Machine Learning](machine-learning-services-overview.md) com R ativado.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

Este exemplo utiliza o procedimento armazenado [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) para embrulhar um script R bem formado.

## <a name="run-a-simple-script"></a>Executar um roteiro simples

Para executar um script R, passará-o como argumento para o procedimento armazenado do sistema, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

Nos seguintes passos, irá executar este script de exemplo R na sua base de dados:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Abra **o SQL Server Management Studio** e ligue-se à sua base de dados.

   Se precisar de ajuda para ligar, consulte [Quickstart: Use o SQL Server Management Studio para ligar e consultar uma base de dados na Base de Dados Azure SQL](connect-query-ssms.md).

1. Passe o guião R completo para o [procedimento sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) armazenado.

   O guião é passado através do `@script` argumento. Tudo dentro do `@script` argumento deve ser código R válido.

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

   Se tiver algum erro, pode ser porque a pré-visualização pública dos Serviços de Machine Learning (com R) não está ativada para a sua base de dados. Consulte [os pré-requisitos](#prerequisites) acima.

   > [!NOTE]
   > Se for um administrador, pode executar código externo automaticamente. Pode conceder permissão a outros utilizadores utilizando o comando:
   <br>**GRANT EXECUTA QUALQUER SCRIPT EXTERNO PARA** *\<username\>* .

2. O resultado correto é calculado e a função R `print` devolve o resultado à janela **Mensagens.**

   Deve ser algo parecido com isto.

    **Resultados**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Executar um roteiro Hello World

Um roteiro de exemplo típico é aquele que apenas produz a cadeia "Hello World". Execute o seguinte comando.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

As entradas para este procedimento armazenado incluem:

| | |
|-|-|
| @language | define a extensão da linguagem para chamar, neste caso, R |
| @script | define os comandos passados para o tempo de execução R. Todo o seu script R deve ser incluído neste argumento, como texto Unicode. Também pode adicionar o texto a uma variável de **tipo nvarchar** e, em seguida, chamar a variável |
| @input_data_1 | dados devolvidos pela consulta, passados para o tempo de execução R, que devolve os dados como um quadro de dados |
|COM CONJUNTOS DE RESULTADOS | cláusula define o esquema da tabela de dados devolvido, adicionando "Hello World" como o nome da coluna, **int** para o tipo de dados |

O comando produz o seguinte texto:

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

1. Use a `SELECT` declaração para consultar a mesa.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Resultados**

    ![Conteúdos da tabela RTestData](./media/r-script-create-quickstart/select-rtestdata.png)

1. Executar o seguinte script R. Recupera os dados da tabela utilizando a `SELECT` declaração, passa-os através do tempo de execução R e devolve os dados como um quadro de dados. A `WITH RESULT SETS` cláusula define o esquema da tabela de dados devolvida para a Base de Dados SQL, adicionando o nome da coluna *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Resultados**

    ![Saída do script R que devolve dados de uma tabela](./media/r-script-create-quickstart/r-output-rtestdata.png)

1. Agora vamos alterar os nomes das variáveis de entrada e saída. Os nomes variáveis de entrada e saída predefinidos são **InputDataSet** e **OutputDataSet,** este script altera os nomes para **SQL_in** e **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Note que R é sensível a casos. As variáveis de entrada e saída utilizadas no script R **(SQL_out**, **SQL_in**) precisam de corresponder aos valores definidos com `@input_data_1_name` `@output_data_1_name` e, incluindo o caso.

   > [!TIP]
   > Apenas um conjunto de dados de entrada pode ser transmitido como parâmetro e só pode ser devolvido um conjunto de dados. No entanto, pode chamar outros conjuntos de dados no código R e devolver saídas de outros tipos para além do conjunto de dados. Também pode adicionar a palavra-chave OUTPUT a qualquer parâmetro, para que seja devolvida com os resultados.

1. Também pode gerar valores apenas usando o script R sem dados de entrada `@input_data_1` (está definido em branco).

   O seguinte script produz o texto "olá" e "mundo".

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

    ![Consultar os resultados tendo @script como a entrada](./media/r-script-create-quickstart/r-data-generated-output.png)

## <a name="check-r-version"></a>Verificar a versão de R

Se quiser ver qual a versão de R instalada na sua base de dados, execute o seguinte script.

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

A Microsoft fornece uma série de pacotes R pré-instalados com serviços de machine learning na sua base de dados.

Para ver uma lista dos pacotes R instalados, incluindo versão, dependências, licença e informações sobre o caminho da biblioteca, execute o seguinte script.

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

A saída é de `installed.packages()` R e é devolvida como resultado definido.

**Resultados**

![Pacotes instalados em R](./media/r-script-create-quickstart/r-installed-packages.png)

## <a name="next-steps"></a>Próximos passos

Para criar um modelo de aprendizagem automática utilizando R na Base de Dados SQL, siga este arranque rápido:

> [!div class="nextstepaction"]
> [Criar e formar um modelo preditivo em R com Azure SQL Database Machine Learning Services (pré-visualização)](r-train-score-model-create-quickstart.md)

Para obter mais informações sobre os Serviços de Aprendizagem automática de máquinas de base de dados Azure SQL com R (pré-visualização), consulte os seguintes artigos.

- [Azure SQL Database Machine Learning Services com R (pré-visualização)](machine-learning-services-overview.md)
- [Escreva funções R avançadas na Base de Dados Azure SQL utilizando serviços de aprendizagem automática (pré-visualização)](machine-learning-services-functions.md)
- [Trabalhar com dados R e SQL em Azure SQL Database Machine Learning Services (pré-visualização)](machine-learning-services-data-issues.md)
