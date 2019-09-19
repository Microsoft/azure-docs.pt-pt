---
title: Criar e executar scripts R simples
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Execute scripts R simples no Serviços de Machine Learning do banco de dados SQL do Azure (versão prévia).
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
ms.openlocfilehash: a47e7a81ba486056841bdc0fe65cfd10f1b2c412
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123198"
---
# <a name="create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Criar e executar scripts R simples no Serviços de Machine Learning do banco de dados SQL do Azure (versão prévia)

Neste guia de início rápido, você criará e executará um conjunto de scripts R simples usando a visualização pública de [serviços de Machine Learning (com R) no banco de dados SQL do Azure](sql-database-machine-learning-services-overview.md). Você aprenderá a encapsular um script R bem formado no procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) e executará o script em um banco de dados SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

- Para executar o código de exemplo nesses exercícios, você deve primeiro ter um banco de dados SQL do Azure com Serviços de Machine Learning (com R) habilitado. Durante a visualização pública, a Microsoft integrará você e habilitará o aprendizado de máquina para seu banco de dados novo ou existente. Siga as etapas em [inscrever-se para a versão prévia](sql-database-machine-learning-services-overview.md#signup).

- Verifique se você instalou o SSMS ( [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) mais recente). Você pode executar scripts do R usando outras ferramentas de consulta e gerenciamento de banco de dados, mas neste guia de início rápido você usará o SSMS.

- Este guia de início rápido requer que você configure uma regra de firewall no nível de servidor. Para obter informações sobre como fazer isso, consulte [criar regra de firewall no nível de servidor](sql-database-server-level-firewall-rule.md).

## <a name="run-a-simple-script"></a>Executar um script simples

Para executar um script R, você o passará como um argumento para o procedimento armazenado do sistema, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

Nas etapas a seguir, você executará este script R de exemplo em seu banco de dados SQL:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Abra o **SQL Server Management Studio** e ligue à sua base de dados SQL.

   Se precisar de ajuda para se conectar [, consulte início rápido: Use SQL Server Management Studio para se conectar e consultar um banco de](sql-database-connect-query-ssms.md)dados SQL do Azure.

1. Passe o script R completo para o procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) .

   O script é passado pelo `@script` argumento. Tudo dentro do `@script` argumento deve ser um código R válido.

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

   Se receber um erro, poderá ser porque a pré-visualização pública do Machine Learning Services (com R) não está ativada para a sua Base de Dados SQL. Consulte [pré-requisitos](#prerequisites) acima.

   > [!NOTE]
   > Se você for um administrador, poderá executar código externo automaticamente. Você pode conceder permissão a outros usuários usando o comando:
   <br>**conceder executar qualquer script externo para** *nome deusuário\>. \<*

2. O resultado correto é calculado e a função `print` R retorna o resultado para a janela **mensagens** .

   Ele deve ser semelhante a este.

    **Resultados**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Executar um script de Olá, Mundo

Um script de exemplo típico é aquele que apenas gera a cadeia de caracteres "Olá, Mundo". Execute o comando a seguir.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

As entradas para esse procedimento armazenado incluem:

| | |
|-|-|
| @language | define a extensão de linguagem para chamar, neste caso, R |
| @script | define os comandos passados para o tempo de execução de R. Todo o script R deve estar incluído nesse argumento, como texto Unicode. Você também pode adicionar o texto a uma variável do tipo **nvarchar** e, em seguida, chamar a variável |
| @input_data_1 | dados retornados pela consulta, passados para o tempo de execução de R, que retorna os dados para SQL Server como um quadro de dados |
|COM CONJUNTOS DE RESULTADOS | a cláusula define o esquema da tabela de dados retornada para SQL Server, adicionando "Olá, Mundo" como o nome da coluna, **int** para o tipo de dados |

O comando gera o seguinte texto:

| Olá, Mundo |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Usar entradas e saídas

Por padrão, o [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) aceita um único conjunto de dados como entrada, que normalmente você fornece na forma de uma consulta SQL válida. Em seguida, ele retorna um único quadro de dados do R como saída.

Por enquanto, vamos usar as variáveis de entrada e saída padrão de [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql): **InputDataSet** e **OutputDataSet**.

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

1. Use a `SELECT` instrução para consultar a tabela.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Resultados**

    ![Conteúdos da tabela RTestData](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. Execute o script R a seguir. Ele recupera os dados da tabela usando a `SELECT` instrução, passa-os pelo tempo de execução do R e retorna os dados como um quadro de dados. A `WITH RESULT SETS` cláusula define o esquema da tabela de dados retornada para o banco de dado SQL, adicionando o nome da coluna *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Resultados**

    ![Saída do script R que devolve dados de uma tabela](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. Agora, vamos alterar os nomes das variáveis de entrada e saída. Os nomes de variáveis de entrada e saída padrão são **InputDataSet** e **OutputDataSet**, esse script altera os nomes para **SQL_in** e **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Observe que o R diferencia maiúsculas de minúsculas. As variáveis de entrada e saída usadas no script R (**SQL_out**, **SQL_in**) precisam corresponder aos valores definidos com `@input_data_1_name` and, incluindo maiúsculas e `@output_data_1_name`minúsculas.

   > [!TIP]
   > Apenas um conjunto de dados de entrada pode ser transmitido como parâmetro e só pode ser devolvido um conjunto de dados. No entanto, pode chamar outros conjuntos de dados no código R e devolver saídas de outros tipos para além do conjunto de dados. Também pode adicionar a palavra-chave OUTPUT a qualquer parâmetro, para que seja devolvida com os resultados.

1. Você também pode gerar valores usando apenas o script R sem dados de entrada (`@input_data_1` está definido como em branco).

   O script a seguir gera o texto "Olá" e "mundo".

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

Se você quiser ver qual versão do R está instalada no banco de dados SQL, execute o script a seguir.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

A função `print` de R devolve a versão na janela **Messages** (Mensagens). Na saída de exemplo abaixo, você pode ver que o banco de dados SQL neste caso tem R versão 3.4.4 instalada.

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

Para ver uma lista de quais pacotes do R estão instalados, incluindo informações de versão, dependências, licença e caminho de biblioteca, execute o script a seguir.

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

A saída é de `installed.packages()` em R e retornada como um conjunto de resultados.

**Resultados**

![Pacotes instalados em R](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>Passos seguintes

Para criar um modelo de aprendizado de máquina usando R no banco de dados SQL, siga este guia de início rápido:

> [!div class="nextstepaction"]
> [Criar e treinar um modelo de previsão em R com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia)](sql-database-quickstart-r-train-score-model.md)

Para obter mais informações sobre o Serviços de Machine Learning do banco de dados SQL do Azure com R (versão prévia), consulte os artigos a seguir.

- [Banco de dados SQL do Azure Serviços de Machine Learning com R (visualização)](sql-database-machine-learning-services-overview.md)
- [Escrever funções de R avançadas no banco de dados SQL do Azure usando Serviços de Machine Learning (versão prévia)](sql-database-machine-learning-services-functions.md)
- [Trabalhar com dados de R e SQL no banco de Serviços de Machine Learning SQL do Azure (versão prévia)](sql-database-machine-learning-services-data-issues.md)
