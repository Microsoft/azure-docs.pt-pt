---
title: Adicionar um pacote de R para serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Este artigo explica como instalar um pacote de R que já não está instalado nos serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/29/2019
ms.openlocfilehash: 4e7145570cbc906ea540c9d8f95f6c3cbde1c610
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927101"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Adicionar um pacote de R para serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)

Este artigo explica como adicionar um pacote de R para serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Instale [R](https://www.r-project.org) e [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) no seu computador local. O R está disponível para Windows, MacOS e Linux. Este artigo pressupõe que está a utilizar o Windows.

- Este artigo inclui um exemplo de uso [Estúdio de dados do Azure](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) para executar uma R do script na base de dados do Azure SQL. Pode executar scripts R com outro gestão de bases de dados ou ferramentas de consulta, mas este exemplo assume Estúdio de dados do Azure ou o SSMS.
   
> [!NOTE]
> Não é possível instalar um pacote ao executar um script de R com **sp_execute_external_script** no Studio de dados do Azure ou no SSMS. Apenas pode instalar e remover pacotes com a linha de comandos de R e o RStudio, conforme descrito neste artigo. Depois de instalar o pacote, pode acessar as funções de pacote num script de R com **sp_execute_external_script**.

## <a name="list-r-packages"></a>Listar pacotes de R

A Microsoft fornece um número de pacotes de R pré-instalado com o Machine Learning Services na sua base de dados SQL do Azure.
Pode ver uma lista de pacotes de R instalados ao executar o seguinte comando no Studio de dados do Azure ou no SSMS.

1. Abra o estúdio de dados do Azure ou o SSMS e ligue à base de dados SQL do Azure.

1. Execute o seguinte comando:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
WITH RESULT SETS((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            ));
```

O resultado deverá ter um aspeto semelhante ao seguinte.

**Resultados**

![Pacotes instalados em R](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Adicionar um pacote com sqlmlutils

Se precisar de utilizar um pacote que já não está instalado na base de dados SQL do Azure, pode instalá-lo através de [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** é um pacote projetado para ajudar os utilizadores interagem com bancos de dados do SQL (SQL Server e base de dados do Azure SQL) e executar o código de R ou Python no SQL a partir de um cliente de R ou Python. Atualmente, apenas a versão de R de **sqlmlutils** é suportada na base de dados do Azure SQL.

O exemplo seguinte, irá instalar o **[cola](https://cran.r-project.org/web/packages/glue/)** pacote que pode formatar e fazer a interpolação de cadeias de caracteres. Instalar estes passos **sqlmlutils** e **RODBCext** (um pré-requisito para **sqlmlutils**) e adicione o **cola** pacote.

### <a name="install-sqlmlutils"></a>Instalar **sqlmlutils**

1. Baixe a versão mais recente **sqlmlutils** ficheiro zip do https://github.com/Microsoft/sqlmlutils/tree/master/R/dist para o computador local. Não precisa de deszipar o ficheiro.

1. Abra um **linha de comandos** e execute os seguintes comandos para instalar **RODBCext** e **sqlmlutils** no seu computador local. Substitua o caminho completo para o **sqlmlutils** ficheiro zip transferido (o exemplo assume o arquivo está em sua pasta documentos).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    O resultado que vir deve ser semelhante ao seguinte.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Se obtiver o erro, "'R' não é reconhecido como um comando interno ou externo, programa operável ou arquivo em lote", provavelmente significa que o caminho para R.exe não está incluído no seu **caminho** variável de ambiente Windows. Pode adicionar o caminho para a variável de ambiente ou navegue para a pasta no prompt de comando (por exemplo `cd C:\Program Files\R\R-3.5.3\bin`) e, em seguida, repita o comando.

### <a name="add-the-package"></a>Adicione o pacote

1. Abra o r Studio e crie um novo **R Script** ficheiro. 

1. Utilize o seguinte código de R para instalar o **cola** utilizando o pacote **sqlmlutils**. Substitua as suas próprias informações de ligação de base de dados do Azure SQL.

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > O **âmbito** pode ser uma **público** ou **privada**. O âmbito público é útil para o administrador da base de dados instalar pacotes que todos os utilizadores podem utilizar. Escopo particular torna o pacote disponível apenas para o utilizador que instala-o. Se não indicar o âmbito, a predefinição é **PRIVADO**.

### <a name="verify-the-package"></a>Verifique se o pacote

Certifique-se de que o **cola** pacote foi instalado, executando o seguinte script do R no RStudio. Utilizar o mesmo **ligação** definido no passo anterior.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Resultados**

![Conteúdos da tabela RTestData](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Utilizar o pacote

Depois de instalar o pacote, pode utilizá-lo num script R através de **sp_execute_external_script**.

1. Abra o estúdio de dados do Azure ou o SSMS e ligue à base de dados SQL do Azure.

1. Execute o seguinte comando:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
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

    Verá o seguinte resultado na **mensagens** separador.

    **Resultados**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Remover o pacote

Se quiser remover o pacote, execute o seguinte script do R no RStudio. Utilizar o mesmo **ligação** definido anteriormente.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Outra forma de instalar um pacote de R em sua base de dados SQL do Azure é para carregar o pacote de R a partir de um fluxo de bytes com o **CREATE EXTERNAL LIBRARY** instrução T-SQL. Ver [criar uma biblioteca a partir de um fluxo de bytes](/sql/t-sql/statements/create-external-library-transact-sql#c-create-a-library-from-a-byte-stream) no [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) documentação de referência.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre serviços do Azure SQL Database Machine Learning com R (pré-visualização), consulte os artigos seguintes.

- [Serviços do Azure SQL da base de dados Machine Learning com R (pré-visualização)](sql-database-machine-learning-services-overview.md)
- [Escrever funções avançadas do R na base de dados do SQL Azure com serviços do Machine Learning (pré-visualização)](sql-database-machine-learning-services-functions.md)
- [Trabalhar com dados de R e SQL no SQL da base de dados serviços Azure Machine Learning (pré-visualização)](sql-database-machine-learning-services-data-issues.md)