---
title: 'SSMS: Connect and query data'
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
ms.date: 03/25/2019
ms.openlocfilehash: ed33d50da84347f55d355802e7767c8477c30e87
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482155"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Quickstart: Use SQL Server Management Studio to connect and query an Azure SQL database

In this quickstart, you'll use [SQL Server Management Studio][ssms-install-latest-84g] (SSMS) to connect to an Azure SQL database. You'll then run Transact-SQL statements to query, insert, update, and delete data. You can use SSMS to manage any SQL infrastructure, from SQL Server to SQL Database for Microsoft Windows.  

## <a name="prerequisites"></a>Pré-requisitos

Uma base de dados SQL do Azure. You can use one of these quickstarts to create and then configure a database in Azure SQL Database:

  || Base de dados única | Instância gerida |
  |:--- |:--- |:---|
  | Create| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurar | [Server-level IP firewall rule](sql-database-server-level-firewall-rule.md)| [Connectivity from a VM](sql-database-managed-instance-configure-vm.md)|
  |||[Connectivity from on-site](sql-database-managed-instance-configure-p2s.md)
  |Carregar dados|Adventure Works loaded per quickstart|[Restore Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Restore or import Adventure Works from [BACPAC](sql-database-import.md) file from [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|

  > [!IMPORTANT]
  > The scripts in this article are written to use the Adventure Works database. With a managed instance, you must either import the Adventure Works database into an instance database or modify the scripts in this article to use the Wide World Importers database.

## <a name="install-the-latest-ssms"></a>Instalar o SSMS mais recente

Before you start, make sure you've installed the latest [SSMS][ssms-install-latest-84g].

## <a name="get-sql-server-connection-information"></a>Get SQL server connection information

Get the connection information you need to connect to the Azure SQL database. You'll need the fully qualified server name or host name, database name, and login information for the upcoming procedures.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navigate to the **SQL databases**  or **SQL managed instances** page.

3. On the **Overview** page, review the fully qualified server name next to **Server name** for a single database or the fully qualified server name next to **Host** for a managed instance. To copy the server name or host name, hover over it and select the **Copy** icon.

## <a name="connect-to-your-database"></a>Ligar à base de dados

In SMSS, connect to your Azure SQL Database server.

> [!IMPORTANT]
> An Azure SQL Database server listens on port 1433. To connect to a SQL Database server from behind a corporate firewall, the firewall must have this port open.
>

1. Abra o SQL Server Management Studio. A caixa de diálogo **Ligar ao Servidor** é apresentada.

2. Introduza as seguintes informações:

   | Definição      | Valor sugerido    | Descrição |
   | ------------ | ------------------ | ----------- |
   | **Tipo de servidor** | Motor de base de dados | Required value. |
   | **Nome do servidor** | O nome de servidor completamente qualificado | Something like: **mynewserver20170313.database.windows.net**. |
   | **Autenticação** | Autenticação do SQL Server | This tutorial uses SQL Authentication. |
   | **Início de sessão** | Server admin account user ID | The user ID from the server admin account used to create the server. |
   | **Palavra-passe** | Server admin account password | The password from the server admin account used to create the server. |
   ||||

   ![ligar ao servidor](./media/sql-database-connect-query-ssms/connect.png)  

3. Select **Options** in the **Connect to Server** dialog box. In the **Connect to database** drop-down menu, select **mySampleDatabase**.If you leave the drop down to default, the connection is made to **master** database.

   ![ligar à base de dados no servidor](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Selecione **Ligar**. The Object Explorer window opens.

5. To view the database's objects, expand **Databases** and then expand **mySampleDatabase**.

   ![mySampleDatabase objects](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Consultar dados

Run this [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL code to query for the top 20 products by category.

1. In Object Explorer, right-click **mySampleDatabase** and select **New Query**. A new query window connected to your database opens.

2. In the query window, paste this SQL query.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. On the toolbar, select **Execute** to retrieve data from the `Product` and `ProductCategory` tables.

    ![query to retrieve data from table Product and ProductCategory](./media/sql-database-connect-query-ssms/query2.png)

## <a name="insert-data"></a>Inserir dados

Run this [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL code to create a new product in the `SalesLT.Product` table.

1. Replace the previous query with this one.

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

2. Select **Execute**  to insert a new row in the `Product` table. The **Messages** pane displays **(1 row affected)** .

## <a name="view-the-result"></a>View the result

1. Replace the previous query with this one.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Selecione **Executar**. The following result appears.

   ![result of Product table query](./media/sql-database-connect-query-ssms/result.png)

## <a name="update-data"></a>Atualizar dados

Run this [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL code to modify your new product.

1. Replace the previous query with this one.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Select **Execute** to update the specified row in the `Product` table. The **Messages** pane displays **(1 row affected)** .

## <a name="delete-data"></a>Eliminar dados

Run this [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL code to remove your new product.

1. Replace the previous query with this one.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Select **Execute** to delete the specified row in the `Product` table. The **Messages** pane displays **(1 row affected)** .

## <a name="next-steps"></a>Passos seguintes

- For information about SSMS, see [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Para ligar e fazer consultas com o portal do Azure, veja [Ligar e fazer consultas com o editor de consultas SQL do portal do Azure](sql-database-connect-query-portal.md).
- Para ligar e consultar com o Visual Studio Code, veja [Connect and query with Visual Studio Code (Ligar e consultar com o Visual Studio Code)](sql-database-connect-query-vscode.md).
- Para ligar e consultar com .NET, consulte [Connect and query with .NET (Ligar e consultar com .NET)](sql-database-connect-query-dotnet.md).
- Para ligar e consultar com PHP, consulte [Connect and query with PHP (Ligar e consultar com PHP)](sql-database-connect-query-php.md).
- Para ligar e consultar com Node.js, consulte [Connect and query with Node.js (Ligar e consultar com Node.js)](sql-database-connect-query-nodejs.md).
- Para ligar e consultar com Java, consulte [Connect and query with Java (Ligar e consultar com Java)](sql-database-connect-query-java.md).
- Para ligar e consultar com Python, consulte [Connect and query with Python (Ligar e consultar com Python)](sql-database-connect-query-python.md).
- Para ligar e consultar com Ruby, consulte [Connect and query with Ruby (Ligar e consultar com Ruby)](sql-database-connect-query-ruby.md).

<!-- Article link references. -->

[ssms-install-latest-84g]: https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms
