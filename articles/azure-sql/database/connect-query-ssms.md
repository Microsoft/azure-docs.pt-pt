---
title: 'SSMS: Conectar e consultar dados'
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Saiba como ligar-se à Base de Dados Azure SQL ou à Instância Gerida sQL utilizando o Estúdio de Gestão de Servidores SQL (SSMS). Em seguida, execute declarações do Transact-SQL (T-SQL) para consultar e editar dados.
keywords: ligar à base de dados sql, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/10/2020
ms.openlocfilehash: bc1e767227f0bdd4ac6b90124c9ccca8f4a0669b
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054378"
---
# <a name="quickstart-use-ssms-to-connect-to-and-query-azure-sql-database"></a>Quickstart: Use SSMS para ligar e consultar base de dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste arranque rápido, você aprenderá a usar o SQL Server Management Studio (SSMS) para ligar à Base de Dados Azure SQL ou ao Azure SQL Managed Instance e executar algumas consultas.

## <a name="prerequisites"></a>Pré-requisitos

Completar este quickstart requer os seguintes itens:

- [Estúdio de Gestão de Servidores SQL (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).

- Uma base de dados na Base de Dados Azure SQL. Pode utilizar um destes quickstarts para criar e, em seguida, configurar uma base de dados na Base de Dados Azure SQL:

  || Base de Dados SQL | Instância Gerida do SQL | SQL Server numa VM do Azure |
  |:--- |:--- |:---|:---|
  | Criar| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configurar | [Regra de firewall IP ao nível do servidor](firewall-create-server-level-portal-quickstart.md)| [Conectividade a partir de um VM](../managed-instance/connect-vm-instance-configure.md)|
  |||[Conectividade a partir do local](../managed-instance/point-to-site-p2s-configure.md) | [Ligar ao SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Carregar dados|Obras de Aventura carregadas por quickstart|[Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md) | [Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md) |
  |||Restaurar ou importar Obras de Aventura a partir do ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Restaurar ou importar Obras de Aventura a partir do ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts deste artigo são escritos para usar a base de dados Adventure Works. Com um caso gerido, deve importar a base de dados da Adventure Works numa base de dados de instâncias ou modificar os scripts deste artigo para utilizar a base de dados dos Importadores do Mundo.

Se simplesmente quiser fazer algumas consultas ad-hoc sem instalar SSMS, consulte [Quickstart: Use o editor de consulta do portal Azure para consultar uma base de dados SQL](connect-query-portal.md).

## <a name="get-server-connection-information"></a>Obtenha informações de ligação ao servidor

Obtenha a informação de ligação que precisa para ligar à sua base de dados. Necessitará do nome do [servidor](logical-servers.md) ou nome do anfitrião totalmente qualificado, nome da base de dados e informações de login para completar este arranque rápido.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para a **base de dados** ou para a instância **gerida** que pretende consultar.

3. Na página **'Overview',** reveja o nome do servidor totalmente qualificado ao lado do **nome do Servidor** para a sua base de dados na Base de Dados SQL ou o nome do servidor (ou endereço IP) totalmente qualificado ao lado do **Anfitrião** para a sua instância gerida em Instância Gerida sQL ou na sua instância de Servidor SQL no seu VM. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copiar.**

> [!NOTE]
> Para obter informações de ligação para o Servidor SQL no Azure VM, consulte [Connect to SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="connect-to-your-database"></a>Ligar à base de dados

No SSMS, ligue-se ao seu servidor.

> [!IMPORTANT]
> Um servidor ouve na porta 1433. Para se ligar a um servidor por detrás de uma firewall corporativa, a firewall deve ter esta porta aberta.

1. Abra o SQL Server Management Studio.

2. A caixa de diálogo **Ligar ao Servidor** é apresentada. Introduza as seguintes informações:

   | Definição      | Valor sugerido    | Descrição |
   | ------------ | ------------------ | ----------- |
   | **Tipo de servidor** | Motor de base de dados | Valor necessário. |
   | **Nome do servidor** | O nome de servidor completamente qualificado | Algo como: **servername.database.windows.net.** |
   | **Autenticação** | Autenticação do SQL Server | Este tutorial utiliza a Autenticação SQL. |
   | **Iniciar sessão** | ID do utilizador da conta de administração do servidor | O ID do utilizador da conta de administração do servidor utilizada para criar o servidor. |
   | **Palavra-passe** | Palavra-passe da conta de administração do servidor | A palavra-passe da conta de administração do servidor usada para criar o servidor. |
   ||||

   ![ligar ao servidor](./media/connect-query-ssms/connect.png)  

3. Selecione **Opções** na caixa de diálogo **Connect to Server.** No menu **Connect to database** drop-down, selecione **mySampleDatabase**. Completar o quickstart na [secção Pré-requisitos](#prerequisites) cria uma base de dados AdventureWorksLT chamada mySampleDatabase. Se a sua cópia de trabalho da base de dados AdventureWorks tiver um nome diferente do mySampleDatabase, então selecione-a.

   ![ligar à base de dados no servidor](./media/connect-query-ssms/options-connect-to-db.png)  

4. Selecione **Ligar**. A janela do Explorador de Objetos abre-se.

5. Para ver os objetos da base de dados, expandir as **Bases de Dados** e, em seguida, expandir o seu nó de base de dados.

   ![objetos mySampleDatabase](./media/connect-query-ssms/connected.png)  

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

3. Na barra de ferramentas, selecione **Executar** para executar a consulta e recuperar dados das `Product` tabelas e `ProductCategory` tabelas.

    ![consulta para recolher dados da tabela Categoria de Produtos e Produtos](./media/connect-query-ssms/query2.png)

### <a name="insert-data"></a>Inserir dados

Executar [este](https://msdn.microsoft.com/library/ms174335.aspx) código Transact-SQL para criar um novo produto na `SalesLT.Product` tabela.

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

2. Selecione **Executar** para inserir uma nova linha na `Product` tabela. Os visores do painel de **mensagens** **(1 linha afetada)**.

#### <a name="view-the-result"></a>Ver o resultado

1. Substitua a consulta anterior por esta.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Selecione **Executar**. O resultado seguinte aparece.

   ![resultado da consulta de tabela do produto](./media/connect-query-ssms/result.png)

### <a name="update-data"></a>Atualizar dados

Execute [este](https://msdn.microsoft.com/library/ms177523.aspx) código Transact-SQL para modificar o seu novo produto.

1. Substitua a consulta anterior por esta que devolve o novo recorde criado anteriormente:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Selecione **Executar** para atualizar a linha especificada na `Product` tabela. Os visores do painel de **mensagens** **(1 linha afetada)**.

### <a name="delete-data"></a>Eliminar dados

Execute este código [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL para remover o seu novo produto.

1. Substitua a consulta anterior por esta.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Selecione **Executar** para eliminar a linha especificada na `Product` tabela. Os visores do painel de **mensagens** **(1 linha afetada)**.

## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre o SSMS, consulte [o SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Para ligar e consultar usando o portal Azure, consulte [Connect e consulta com o portal Azure SQL Query editor](connect-query-portal.md).
- Para ligar e consultar com o Visual Studio Code, veja [Connect and query with Visual Studio Code (Ligar e consultar com o Visual Studio Code)](connect-query-vscode.md).
- Para ligar e consultar com .NET, consulte [Connect and query with .NET (Ligar e consultar com .NET)](connect-query-dotnet-visual-studio.md).
- Para ligar e consultar com PHP, consulte [Connect and query with PHP (Ligar e consultar com PHP)](connect-query-php.md).
- Para ligar e consultar com Node.js, consulte [Connect and query with Node.js (Ligar e consultar com Node.js)](connect-query-nodejs.md).
- Para ligar e consultar com Java, consulte [Connect and query with Java (Ligar e consultar com Java)](connect-query-java.md).
- Para ligar e consultar com Python, consulte [Connect and query with Python (Ligar e consultar com Python)](connect-query-python.md).
- Para ligar e consultar com Ruby, consulte [Connect and query with Ruby (Ligar e consultar com Ruby)](connect-query-ruby.md).
