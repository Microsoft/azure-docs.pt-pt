---
title: Utilizar R com Azure SQL Database Machine Learning Services (pré-visualização) para consultar uma base de dados
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Este artigo mostra-lhe como utilizar um script R com os Serviços de Aprendizagem de Máquinas de Base de Dados Azure SQL para ligar a uma base de dados na Base de Dados Azure SQL e questioná-lo utilizando declarações Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: sqldbrb=2
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, sstein
manager: cgronlun
ms.date: 05/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 62f1015bc945b5c01f719d7e96e6446c49ac8b8a
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675150"
---
# <a name="quickstart-use-r-with-azure-sql-database-machine-learning-services-preview-to-query-a-database"></a>Quickstart: Use R com Azure SQL Database Machine Learning Services (pré-visualização) para consultar uma base de dados 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste arranque rápido, utiliza R com Azure SQL Database Machine Learning Services para ligar a uma base de dados na Base de Dados Azure SQL e utilizar declarações T-SQL para consultar dados.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma [base de dados Azure SQL](single-database-create-quickstart.md)
- [Serviços de Machine Learning](../managed-instance/machine-learning-services-overview.md) com R ativado.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> Os scripts deste artigo são escritos para usar a base de dados **Adventure Works.**

Machine Learning Services with R é uma característica da Base de Dados Azure SQL utilizada para executar scripts R na base de dados. Para mais informações, consulte o [Projeto R.](https://www.r-project.org/)

## <a name="get-the-sql-server-connection-information"></a>Obtenha a informação de ligação do SQL Server

Obtenha a informação de ligação necessária para ligar à base de dados na Base de Dados Azure SQL. Você precisará do nome do servidor totalmente qualificado ou nome de anfitrião, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Navegue para a **página SQL Databases** ou **SQL Managed Instances.**

3. Na página **'Vista Geral',** reveja o nome do servidor totalmente qualificado ao lado **do nome do Servidor** para uma base de dados na Base de Dados Azure SQL ou o nome do servidor totalmente qualificado ao lado do **Anfitrião** para uma instância gerida em Azure SQL Managed Instance. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copy.**

## <a name="create-code-to-query-your-database"></a>Crie código para consultar a sua base de dados

1. Abra **o SQL Server Management Studio** e ligue-se à sua base de dados.

   Se precisar de ajuda para ligar, consulte [Quickstart: Use o SQL Server Management Studio para ligar e consultar uma base de dados na Base de Dados Azure SQL](connect-query-ssms.md).

1. Passe o guião R completo para o [procedimento sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) armazenado.

   O guião é passado através do `@script` argumento. Tudo dentro do `@script` argumento deve ser código R válido.
   
   >[!IMPORTANT]
   >O código neste exemplo utiliza a amostra de dados AdventureWorksLT, que pode escolher como fonte ao criar a sua base de dados. Se a sua base de dados tiver dados diferentes, utilize tabelas da sua própria base de dados na consulta SELECT. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Se tiver algum erro, pode ser porque a pré-visualização pública dos Serviços de Machine Learning (com R) não está ativada para a sua base de dados. Consulte [os pré-requisitos](#prerequisites) acima.

## <a name="run-the-code"></a>Executar o código

1. Execute o [procedimento sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) armazenado.

1. Verifique se as linhas top 20 categoria/produto são devolvidas na janela **Mensagens.**

## <a name="next-steps"></a>Passos seguintes

- [Desenhe a sua primeira base de dados na Base de Dados Azure SQL](design-first-database-tutorial.md)
- [Azure SQL Database Machine Learning Services (com R)](../managed-instance/machine-learning-services-overview.md)
- [Criar e executar scripts R simples em Azure SQL Database Machine Learning Services (pré-visualização)](/sql/machine-learning/tutorials/quickstart-r-create-script?context=%252fazure%252fazure-sql%252fmanaged-instance%252fcontext%252fml-context)