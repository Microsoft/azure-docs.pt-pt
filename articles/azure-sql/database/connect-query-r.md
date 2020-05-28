---
title: Utilize R com Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização) para consultar uma base de dados
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Este artigo mostra-lhe como usar um script R com serviços de machine learning de base de dados Azure SQL para ligar a uma base de dados em Azure SQL Database e consulta-lo usando declarações Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: sqldbrb=2 
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7bfc00566ef24fb6d6851a7cdee402598fe15951
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054450"
---
# <a name="quickstart-use-r-with-azure-sql-database-machine-learning-services-preview-to-query-a-database"></a>Quickstart: Use R com Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização) para consultar uma base de dados 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste arranque rápido, utiliza R com Serviços de Machine Learning de Base de Dados Azure SQL para se ligar a uma base de dados em Azure SQL Database e utilizar declarações T-SQL para consultar dados.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma [base de dados Azure SQL](single-database-create-quickstart.md)
- [Serviços de Aprendizagem automática](machine-learning-services-overview.md) com R ativado.
- [Estúdio de Gestão de Servidores SQL](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> Os scripts deste artigo são escritos para usar a base de dados **Adventure Works.**

Serviços de Aprendizagem automática com R é uma característica da Base de Dados Azure SQL utilizada para executar scripts R na base de dados. Para mais informações, consulte o [Projeto R.](https://www.r-project.org/)

## <a name="get-the-sql-server-connection-information"></a>Obtenha as informações de ligação do Servidor SQL

Obtenha as informações de ligação que precisa para ligar à base de dados do Azure SQL Database. Necessitará do nome do servidor ou nome do anfitrião totalmente qualificado, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para as Bases de **Dados SQL** ou página **SQL Managed Instances.**

3. Na página **Overview,** reveja o nome do servidor totalmente qualificado ao lado do **nome do Servidor** para uma base de dados em Base de Dados Azure SQL ou o nome de servidor totalmente qualificado ao lado do **Host** para uma instância gerida em 12 sQL Managed Instance. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copiar.**

## <a name="create-code-to-query-your-database"></a>Crie código para consultar a sua base de dados

1. Abra o **Estúdio de Gestão de Servidores SQL** e ligue-se à sua base de dados.

   Se precisar de ajuda para se ligar, consulte [Quickstart: Use o Estúdio de Gestão de Servidores SQL para ligar e consultar uma base de dados na Base de Dados Azure SQL](connect-query-ssms.md).

1. Passe o script R completo para o [procedimento sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) armazenado.

   O guião é passado através do `@script` argumento. Tudo dentro do `@script` argumento deve ser válido código R.
   
   >[!IMPORTANT]
   >O código neste exemplo utiliza os dados adventureWorksLT da amostra, que pode escolher como fonte ao criar a sua base de dados. Se a sua base de dados tiver dados diferentes, utilize as tabelas da sua própria base de dados na consulta SELECT. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Se tiver algum erro, pode ser porque a pré-visualização pública dos Serviços de Aprendizagem automática (com R) não está ativada para a sua base de dados. Consulte [os pré-requisitos](#prerequisites) acima.

## <a name="run-the-code"></a>Executar o código

1. Execute o [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) procedimento armazenado.

1. Verifique se as 20 melhores linhas de categoria/produto são devolvidas na janela **Mensagens.**

## <a name="next-steps"></a>Próximos passos

- [Desenhe a sua primeira base de dados na Base de Dados Azure SQL](design-first-database-tutorial.md)
- [Serviços de Machine Learning De base de dados Azure SQL (com R)](machine-learning-services-overview.md)
- [Criar e executar scripts R simples em Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)](r-script-create-quickstart.md)
- [Escreva funções R avançadas na Base de Dados Azure SQL utilizando serviços de aprendizagem automática (pré-visualização)](machine-learning-services-functions.md)
