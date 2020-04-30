---
title: 'SSMS: Conectar e consultar dados'
description: Saiba como ligar à Base de Dados SQL no Azure, utilizando o SQL Server Management Studio (SSMS). Em seguida, execute declarações do Transact-SQL (T-SQL) para consultar e editar dados.
keywords: ligar à base de dados sql, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/10/2020
ms.openlocfilehash: 31bd47128a272e75d7021180b536fe6bf7420f55
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79299299"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Quickstart: Use o SQL Server Management Studio para ligar e consultar uma base de dados Azure SQL

Neste arranque rápido, você aprenderá a usar o SQL Server Management Studio (SSMS) para ligar a uma base de dados Azure SQL e executar algumas consultas.

## <a name="prerequisites"></a>Pré-requisitos

Completar este quickstart requer os seguintes itens:

- [Estúdio de Gestão de Servidores SQL (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).
- A base de dados da amostra AdventureWorksLT. Se precisar de uma cópia de trabalho da base de dados AdventureWorksLT, crie uma, completando a base de [dados Create e SQL.](sql-database-single-database-get-started.md)
    - Os scripts deste artigo são escritos para usar a base de dados AdventureWorksLT. Se estiver a utilizar uma instância gerida, deve importar a base de dados AdventureWorks numa base de dados de instâncias ou modificar os scripts deste artigo para utilizar a base de dados dos Importadores do Mundo Largo.

Se simplesmente quiser fazer algumas consultas ad-hoc sem instalar SSMS, consulte [Quickstart: Use o editor de consulta do portal Azure para consultar uma base de dados SQL](sql-database-connect-query-portal.md).

## <a name="get-sql-server-connection-information"></a>Obtenha informações de ligação ao servidor SQL

Obtenha a informação de ligação que precisa para ligar à sua base de dados. Necessitará do nome do servidor ou nome do anfitrião totalmente qualificado, nome da base de dados e informações de login para completar este arranque rápido.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para a base de **dados SQL** ou para a instância gerida pela **SQL** que pretende consultar.

3. Na página **'Visão Geral',** copie o nome do servidor totalmente qualificado. É ao lado do **nome server** para uma única base de dados, ou o nome de servidor totalmente qualificado ao lado do **Host** para uma instância gerida. O nome totalmente qualificado parece: *servername.database.windows.net,* exceto que tem o nome do servidor real.

## <a name="connect-to-your-database"></a>Ligar à base de dados

No SSMS, ligue-se ao seu servidor de base de dados Azure SQL.

> [!IMPORTANT]
> Um servidor de base de dados Azure SQL ouve na porta 1433. Para se ligar a um servidor de base de dados SQL por trás de uma firewall corporativa, a firewall deve ter esta porta aberta.

1. Abra o SQL Server Management Studio.

2. A caixa de diálogo **Ligar ao Servidor** é apresentada. Introduza as seguintes informações:

   | Definição      | Valor sugerido    | Descrição |
   | ------------ | ------------------ | ----------- |
   | **Tipo de servidor** | Motor de base de dados | Valor necessário. |
   | **Nome do servidor** | O nome de servidor completamente qualificado | Algo como: **servername.database.windows.net.** |
   | **Autenticação** | Autenticação do SQL Server | Este tutorial utiliza a Autenticação SQL. |
   | **Início de sessão** | ID do utilizador da conta de administração do servidor | O ID do utilizador da conta de administração do servidor utilizada para criar o servidor. |
   | **Palavra-passe** | Palavra-passe da conta de administração do servidor | A palavra-passe da conta de administração do servidor usada para criar o servidor. |
   ||||

   ![ligar ao servidor](./media/sql-database-connect-query-ssms/connect.png)  

3. Selecione **Opções** na caixa de diálogo **Connect to Server.** No menu **Connect to database** drop-down, selecione **mySampleDatabase**. Completar o quickstart na [secção Pré-requisitos](#prerequisites) cria uma base de dados AdventureWorksLT chamada mySampleDatabase. Se a sua cópia de trabalho da base de dados AdventureWorks tiver um nome diferente do mySampleDatabase, então selecione-a.

   ![ligar à base de dados no servidor](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Selecione **Ligar**. A janela do Explorador de Objetos abre-se.

5. Para ver os objetos da base de dados, expandir as **Bases de Dados** e, em seguida, expandir o seu nó de base de dados.

   ![objetos mySampleDatabase](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Consultar dados

Execute este código [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL para consultar os 20 melhores produtos por categoria.

1. No Object Explorer, clique no **mySampleDatabase** e selecione **New Query**. Abre-se uma nova janela de consulta ligada à sua base de dados.

2. Na janela de consulta, colhe a seguinte consulta SQL:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Na barra de ferramentas, selecione **Executar** para `Product` `ProductCategory` executar a consulta e recuperar dados das tabelas e tabelas.

    ![consulta para recolher dados da tabela Categoria de Produtos e Produtos](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>Inserir dados

Executar [este](https://msdn.microsoft.com/library/ms174335.aspx) código Transact-SQL para criar `SalesLT.Product` um novo produto na tabela.

1. Substitua a consulta anterior por esta.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Selecione **Executar** para inserir `Product` uma nova linha na tabela. Os visores do painel de **mensagens** **(1 linha afetada)**.

#### <a name="view-the-result"></a>Ver o resultado

1. Substitua a consulta anterior por esta.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Selecione **Executar**. O resultado seguinte aparece.

   ![resultado da consulta de tabela do produto](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>Atualizar dados

Execute [este](https://msdn.microsoft.com/library/ms177523.aspx) código Transact-SQL para modificar o seu novo produto.

1. Substitua a consulta anterior por esta que devolve o novo recorde criado anteriormente:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Selecione **Executar** para atualizar `Product` a linha especificada na tabela. Os visores do painel de **mensagens** **(1 linha afetada)**.

### <a name="delete-data"></a>Eliminar dados

Execute este código [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL para remover o seu novo produto.

1. Substitua a consulta anterior por esta.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Selecione **Executar** para eliminar `Product` a linha especificada na tabela. Os visores do painel de **mensagens** **(1 linha afetada)**.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre o SSMS, consulte [o SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Para ligar e consultar usando o portal Azure, consulte [Connect e consulta com o portal Azure SQL Query editor](sql-database-connect-query-portal.md).
- Para ligar e consultar com o Visual Studio Code, veja [Connect and query with Visual Studio Code (Ligar e consultar com o Visual Studio Code)](sql-database-connect-query-vscode.md).
- Para ligar e consultar com .NET, consulte [Connect and query with .NET (Ligar e consultar com .NET)](sql-database-connect-query-dotnet.md).
- Para ligar e consultar com PHP, consulte [Connect and query with PHP (Ligar e consultar com PHP)](sql-database-connect-query-php.md).
- Para ligar e consultar com Node.js, consulte [Connect and query with Node.js (Ligar e consultar com Node.js)](sql-database-connect-query-nodejs.md).
- Para ligar e consultar com Java, consulte [Connect and query with Java (Ligar e consultar com Java)](sql-database-connect-query-java.md).
- Para ligar e consultar com Python, consulte [Connect and query with Python (Ligar e consultar com Python)](sql-database-connect-query-python.md).
- Para ligar e consultar com Ruby, consulte [Connect and query with Ruby (Ligar e consultar com Ruby)](sql-database-connect-query-ruby.md).
