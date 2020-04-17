---
title: Adicione um pacote R aos Serviços de Aprendizagem Automática (pré-visualização)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Este artigo explica como instalar um pacote R que ainda não está instalado nos Serviços de Machine Learning de Máquinas de Base de Dados Azure SQL (pré-visualização).
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
ROBOTS: NOINDEX
ms.openlocfilehash: ab066609bff773ceacb06be604e386eed5cdf7ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453341"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Adicione um pacote R aos Serviços de Machine Learning de Máquinas de Base de Dados Azure SQL (pré-visualização)

Este artigo explica como adicionar um pacote R aos Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Instale [o R](https://www.r-project.org) e [o RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) no seu computador local. O R está disponível para Windows, MacOS e Linux. Este artigo assume que está a usar o Windows.

- Este artigo inclui um exemplo de utilização do [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou [do SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) para executar um script R na Base de Dados Azure SQL. Pode executar scripts R utilizando outras ferramentas de gestão de bases de dados ou consultas, mas este exemplo pressupõe o Azure Data Studio ou o SSMS.
   
> [!NOTE]
> Não é possível instalar um pacote executando um script R utilizando **sp_execute_external_script** no Azure Data Studio ou SSMS. Só é possível instalar e remover pacotes utilizando a linha de comando R e rStudio, conforme descrito neste artigo. Uma vez instalado o pacote, pode aceder às funções do pacote num script R utilizando **sp_execute_external_script**.

## <a name="list-r-packages"></a>Listar pacotes de R

A Microsoft fornece uma série de pacotes R pré-instalados com serviços de aprendizagem automática na sua base de dados Azure SQL.
Pode ver uma lista de pacotes R instalados executando o seguinte comando no Azure Data Studio ou SSMS.

1. Abra o Estúdio de Dados Azure ou SSMS e ligue-se à sua Base de Dados Azure SQL.

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

A saída deve ser semelhante à seguinte.

**Resultados**

![Pacotes instalados em R](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Adicione um pacote com sqlmlutils

Se necessitar de utilizar um pacote que ainda não esteja instalado na sua Base de Dados Azure SQL, pode instalá-lo utilizando [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **Sqlmlutils** é um pacote projetado para ajudar os utilizadores a interagir com bases de dados SQL (SQL Server e Azure SQL Database) e executar código R ou Python em SQL a partir de um cliente R ou Python. Atualmente, apenas a versão R de **sqlmlutils** é suportada na Base de Dados Azure SQL.

No exemplo seguinte, irá instalar o pacote de **[cola](https://cran.r-project.org/web/packages/glue/)** que pode formatar e interpolar cordas. Estes passos instalam **sqlmlutils** e **RODBCext** (um pré-requisito para **sqlmlutils)** e adicione o pacote de **cola.**

### <a name="install-sqlmlutils"></a>Instale **sqlmlutils**

1. Descarregue o mais recente ficheiro https://github.com/Microsoft/sqlmlutils/tree/master/R/dist zip **sqlmlutils** de para o seu computador local. Não precisas de desapertar o ficheiro.

1. Abra um **Comando Solicitação** e execute os seguintes comandos para instalar **RODBCext** e **sqlmlutils** no seu computador local. Substitua o caminho completo para o ficheiro zip **sqlmlutil sqlmlutils** que descarregou (o exemplo pressupõe que o ficheiro está na sua pasta Documentos).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    A saída que vê deve ser semelhante à seguinte.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Se tiver o erro, "R" não é reconhecido como um comando interno ou externo, programa operável ou ficheiro de lote", o que provavelmente significa que o caminho para R.exe não está incluído na variável ambiente **PATH** no Windows. Pode adicionar o caminho à variável ambiental ou navegar para a `cd C:\Program Files\R\R-3.5.3\bin`pasta no pedido de comando (por exemplo) e, em seguida, voltar a tentar o comando.

### <a name="add-the-package"></a>Adicione o pacote

1. Abra o RStudio e crie um ficheiro **R Script** novo. 

1. Utilize o seguinte código R para instalar a embalagem de **cola** utilizando **sqlmlutils**. Substitua as suas próprias informações de ligação à base de dados Azure SQL.

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
    > O público **público** ou o **público.** **PRIVATE** O âmbito público é útil para o administrador da base de dados instalar pacotes que todos os utilizadores podem utilizar. O âmbito privado disponibiliza o pacote apenas ao utilizador que o instala. Se não indicar o âmbito, a predefinição é **PRIVADO**.

### <a name="verify-the-package"></a>Verifique a embalagem

Verifique se o pacote de **cola** foi instalado executando o seguinte script R no RStudio. Utilize a mesma **ligação** definida no passo anterior.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Resultados**

![Conteúdos da tabela RTestData](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Use a embalagem

Uma vez instalado o pacote, pode usá-lo num script R através **sp_execute_external_script**.

1. Abra o Estúdio de Dados Azure ou SSMS e ligue-se à sua Base de Dados Azure SQL.

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

    Verá o seguinte resultado no separador **Mensagens.**

    **Resultados**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Retire a embalagem

Se quiser remover o pacote, execute o seguinte script R no RStudio. Use a mesma **ligação** que definiu anteriormente.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Outra forma de instalar um pacote R na sua base de dados Azure SQL é carregar o pacote R a partir de um fluxo de byte utilizando a declaração Create **EXTERNAL LIBRARY** T-SQL. Consulte [Criar uma biblioteca a partir de um fluxo de byte](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) na documentação de referência create [external LIBRARY.](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre os Serviços de Machine Learning de Máquinas de Base de Dados Azure SQL com R (pré-visualização), consulte os seguintes artigos.

- [Serviços de Machine Learning de Base de Dados Azure SQL com R (pré-visualização)](sql-database-machine-learning-services-overview.md)
- [Escreva funções R avançadas na Base de Dados Azure SQL utilizando serviços de aprendizagem automática (pré-visualização)](sql-database-machine-learning-services-functions.md)
- [Trabalhar com dados R e SQL nos Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)](sql-database-machine-learning-services-data-issues.md)