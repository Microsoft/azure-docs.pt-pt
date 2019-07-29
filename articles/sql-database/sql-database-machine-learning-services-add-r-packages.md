---
title: Adicionar um pacote do R ao Serviços de Machine Learning do banco de dados SQL do Azure (versão prévia)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Este artigo explica como instalar um pacote R que ainda não está instalado no banco de dados SQL do Azure Serviços de Machine Learning (versão prévia).
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
ms.openlocfilehash: f82408a6aaa7cf3a492f3036a6db5d8666b6f160
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598045"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Adicionar um pacote do R ao Serviços de Machine Learning do banco de dados SQL do Azure (versão prévia)

Este artigo explica como adicionar um pacote R ao banco de dados SQL do Azure Serviços de Machine Learning (versão prévia).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Instale o [R](https://www.r-project.org) e o [RStudio desktop](https://www.rstudio.com/products/rstudio/download/) no computador local. O R está disponível para Windows, MacOS e Linux. Este artigo pressupõe que você esteja usando o Windows.

- Este artigo inclui um exemplo de como usar [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) para executar um script do R no banco de dados SQL do Azure. Você pode executar scripts do R usando outras ferramentas de consulta e gerenciamento de banco de dados, mas este exemplo pressupõe Azure Data Studio ou SSMS.
   
> [!NOTE]
> Você não pode instalar um pacote executando um script R usando **sp_execute_external_script** em Azure Data Studio ou SSMS. Você só pode instalar e remover pacotes usando a linha de comando R e RStudio, conforme descrito neste artigo. Depois que o pacote for instalado, você poderá acessar as funções de pacote em um script R usando **sp_execute_external_script**.

## <a name="list-r-packages"></a>Listar pacotes de R

A Microsoft fornece vários pacotes de R pré-instalados com Serviços de Machine Learning em seu banco de dados SQL do Azure.
Você pode ver uma lista de pacotes de R instalados executando o comando a seguir no Azure Data Studio ou no SSMS.

1. Abra Azure Data Studio ou SSMS e conecte-se ao banco de dados SQL do Azure.

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

A saída deve ser semelhante ao seguinte.

**Resultados**

![Pacotes instalados em R](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Adicionar um pacote com sqlmlutils

Se você precisar usar um pacote que ainda não esteja instalado em seu banco de dados SQL do Azure, você poderá instalá-lo usando o [sqlmlutils](https://github.com/Microsoft/sqlmlutils). o **sqlmlutils** é um pacote projetado para ajudar os usuários a interagir com bancos de dados sql (SQL Server e o Azure SQL Database) e executar o código r ou Python no SQL de um cliente R ou Python. Atualmente, somente a versão R do **sqlmlutils** tem suporte no banco de dados SQL do Azure.

No exemplo a seguir, você instalará o pacote de **[União](https://cran.r-project.org/web/packages/glue/)** que pode formatar e interpolar cadeias de caracteres. Estas etapas instalam **sqlmlutils** e **RODBCext** (um pré-requisito para **sqlmlutils**) e adicionam o pacote de **cola** .

### <a name="install-sqlmlutils"></a>Instalar o **sqlmlutils**

1. Baixe o arquivo zip **sqlmlutils** mais recente https://github.com/Microsoft/sqlmlutils/tree/master/R/dist de em seu computador local. Você não precisa descompactar o arquivo.

1. Abra um **prompt de comando** e execute os comandos a seguir para instalar o **RODBCext** e o **sqlmlutils** no computador local. Substitua o caminho completo para o arquivo zip **sqlmlutils** que você baixou (o exemplo supõe que o arquivo está em sua pasta documentos).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    A saída que você vê deve ser semelhante à seguinte.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Se você receber o erro "o" R "não é reconhecido como um comando interno ou externo, um programa ou arquivo em lotes operável, provavelmente significa que o caminho para o R. exe não está incluído em sua variável de ambiente **Path** no Windows. Você pode adicionar o caminho à variável de ambiente ou navegar até a pasta no prompt de comando (por exemplo `cd C:\Program Files\R\R-3.5.3\bin`) e, em seguida, repetir o comando.

### <a name="add-the-package"></a>Adicionar o pacote

1. Abra RStudio e crie um novo arquivo de **script R** . 

1. Use o código R a seguir para instalar o pacote de **União** usando **sqlmlutils**. Substitua suas próprias informações de conexão do banco de dados SQL do Azure.

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
    > O **escopo** pode ser **público** ou **privado**. O âmbito público é útil para o administrador da base de dados instalar pacotes que todos os utilizadores podem utilizar. O escopo privado disponibiliza o pacote somente para o usuário que o instala. Se não indicar o âmbito, a predefinição é **PRIVADO**.

### <a name="verify-the-package"></a>Verificar o pacote

Verifique se o pacote de **colagem** foi instalado executando o seguinte script R no RStudio. Use a mesma **conexão** que você definiu na etapa anterior.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Resultados**

![Conteúdos da tabela RTestData](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Usar o pacote

Depois que o pacote for instalado, você poderá usá-lo em um script R por meio de **sp_execute_external_script**.

1. Abra Azure Data Studio ou SSMS e conecte-se ao banco de dados SQL do Azure.

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

    Você verá o seguinte resultado na guia **mensagens** .

    **Resultados**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Remover o pacote

Se você quiser remover o pacote, execute o seguinte script R em RStudio. Use a mesma **conexão** que você definiu anteriormente.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Outra maneira de instalar um pacote do R em seu banco de dados SQL do Azure é carregar o pacote R de um fluxo de bytes usando a instrução T-SQL **criar biblioteca externa** . Consulte [criar uma biblioteca de um fluxo de bytes](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) na documentação criar referência de [biblioteca externa](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) .

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Serviços de Machine Learning do banco de dados SQL do Azure com R (versão prévia), consulte os artigos a seguir.

- [Banco de dados SQL do Azure Serviços de Machine Learning com R (visualização)](sql-database-machine-learning-services-overview.md)
- [Escrever funções de R avançadas no banco de dados SQL do Azure usando Serviços de Machine Learning (versão prévia)](sql-database-machine-learning-services-functions.md)
- [Trabalhar com dados de R e SQL no banco de Serviços de Machine Learning SQL do Azure (versão prévia)](sql-database-machine-learning-services-data-issues.md)