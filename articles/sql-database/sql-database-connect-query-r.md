---
title: Utilize R com Serviços de Aprendizagem Automática para consultar uma base de dados (pré-visualização)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Este artigo mostra-lhe como usar um script R com serviços de machine learning de base de dados Azure SQL para ligar a uma base de dados Azure SQL e consulta-lo usando declarações Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: 0288d8c4710d12d8e67658caab93157c534b75ee
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758364"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Quickstart: Use R com Serviços de Aprendizagem Automática para consultar uma base de dados Azure SQL (pré-visualização)

Neste arranque rápido, utiliza R com Serviços de Aprendizagem Automática para se ligar a uma base de dados Azure SQL e utilizar declarações T-SQL para consultar dados.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

- Uma [base de dados Azure SQL](sql-database-single-database-get-started.md)
  
- [Serviços de Aprendizagem automática](sql-database-machine-learning-services-overview.md) com R ativado. [Inscreva-se para a pré-visualização](sql-database-machine-learning-services-overview.md#signup).

- [Estúdio de Gestão de Servidores SQL](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> Os scripts deste artigo são escritos para usar a base de dados **Adventure Works.**

> [!NOTE]
> Durante a pré-visualização pública, a Microsoft irá embarcar e ativar o machine learning para a sua base de dados existente ou nova, no entanto a opção de implementação de instânciagerida não é suportada atualmente.

Serviços de Aprendizagem automática com R é uma característica da base de dados Azure SQL utilizada para executar scripts R na base de dados. Para mais informações, consulte o [Projeto R.](https://www.r-project.org/)

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao banco de dados SQL do Azure. Você precisará do nome do servidor totalmente qualificado ou nome do host, nome do banco de dados e informações de logon para os próximos procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **instâncias gerenciadas do SQL** .

3. Na página **visão geral** , examine o nome do servidor totalmente qualificado ao lado de **nome do servidor** para um único banco de dados ou o nome do servidor totalmente qualificado ao lado de **host** para uma instância gerenciada. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone de **cópia** .

## <a name="create-code-to-query-your-sql-database"></a>Crie código para consultar a sua base de dados SQL

1. Abra o **SQL Server Management Studio** e ligue à sua base de dados SQL.

   Se precisar de ajuda para se ligar, consulte [Quickstart: Use o Estúdio de Gestão de Servidores SQL para ligar e consultar uma base de dados Azure SQL](sql-database-connect-query-ssms.md).

1. Passe o script R completo para o [procedimento sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) armazenado.

   O guião é passado através do argumento `@script`. Tudo dentro do argumento `@script` deve ser válido código R.
   
   >[!IMPORTANT]
   >O código neste exemplo usa os dados AdventureWorksLT de exemplo, que você pode escolher como fonte ao criar o banco de dado. Se o seu banco de dados tiver um dado diferente, use tabelas do seu próprio banco na consulta SELECT. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Se receber um erro, poderá ser porque a pré-visualização pública do Machine Learning Services (com R) não está ativada para a sua Base de Dados SQL. Consulte [os pré-requisitos](#prerequisites) acima.

## <a name="run-the-code"></a>Executar o código

1. Execute o [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) procedimento armazenado.

1. Verifique se as 20 melhores linhas de categoria/produto são devolvidas na janela **Mensagens.**

## <a name="next-steps"></a>Passos seguintes

- [Criar a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md)
- [Serviços de Machine Learning De base de dados Azure SQL (com R)](sql-database-machine-learning-services-overview.md)
- [Criar e executar scripts R simples em Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)](sql-database-quickstart-r-create-script.md)
- [Escreva funções R avançadas na Base de Dados Azure SQL utilizando serviços de aprendizagem automática (pré-visualização)](sql-database-machine-learning-services-functions.md)
