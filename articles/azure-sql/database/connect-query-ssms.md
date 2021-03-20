---
title: 'SSMS: Ligar e consultar dados'
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Saiba como ligar-se à Base de Dados SQL do Azure ou ao SQL Managed Instance utilizando o SQL Server Management Studio (SSMS). Em seguida, execute declarações do Transact-SQL (T-SQL) para consultar e editar dados.
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
ms.date: 09/28/2020
ms.openlocfilehash: 60977b9388af3a93d0ebbbc6aad50628b79e0e44
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91598268"
---
# <a name="quickstart-use-ssms-to-connect-to-and-query-azure-sql-database-or-azure-sql-managed-instance"></a>Quickstart: Utilize SSMS para ligar e consultar a Base de Dados Azure SQL ou a exemplo gerida do Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Neste arranque rápido, você aprenderá a usar o SQL Server Management Studio (SSMS) para ligar à Base de Dados Azure SQL ou Azure SQL Managed Instance e executar algumas consultas.

## <a name="prerequisites"></a>Pré-requisitos

Completar este arranque rápido requer os seguintes itens:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).

- Uma base de dados na Base de Dados Azure SQL. Pode utilizar um destes quickstarts para criar e, em seguida, configurar uma base de dados na Base de Dados Azure SQL:

  | Ação | SQL Database | Instância Gerida do SQL | SQL Server numa VM do Azure |
  |:--- |:--- |:---|:---|
  | Criar| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configurar | [Regra de firewall IP de nível de servidor](firewall-create-server-level-portal-quickstart.md)| [Conectividade de um VM](../managed-instance/connect-vm-instance-configure.md)|
  |||[Conectividade a partir do local](../managed-instance/point-to-site-p2s-configure.md) | [Ligar ao SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Carregar dados|Obras de Aventura carregadas por quickstart|[Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md) | [Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md) |
  |||Restaurar ou importar Obras de Aventura a partir de ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Restaurar ou importar Obras de Aventura a partir de ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts deste artigo são escritos para usar a base de dados Adventure Works. Com um caso gerido, você deve importar a base de dados Adventure Works em uma base de dados de casos ou modificar os scripts deste artigo para usar a base de dados de importadores do Mundo Largo.

Se simplesmente quiser executar algumas consultas ad-hoc sem instalar SSMS, consulte [Quickstart: Use o editor de consulta do portal Azure para consultar uma base de dados na Base de Dados Azure SQL](connect-query-portal.md).

## <a name="get-server-connection-information"></a>Obtenha informações de ligação do servidor

Obtenha a informação de ligação necessária para ligar à sua base de dados. Você precisará do nome do [servidor](logical-servers.md) totalmente qualificado ou nome de anfitrião, nome da base de dados e informações de login para completar este início rápido.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para a **base de dados** ou **caso gerido** que deseja consultar.

3. Na página **'Vista Geral',** reveja o nome do servidor totalmente qualificado ao lado **do nome do Servidor** para a sua base de dados na Base de Dados SQL ou o nome do servidor (ou endereço IP) totalmente qualificado ao lado do **Anfitrião** para a sua instância gerida em SQL Ou a sua instância do Servidor SQL no seu VM. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copy.**

> [!NOTE]
> Para obter informações de ligação para O Servidor SQL em Azure VM, consulte [Connect to SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="connect-to-your-database"></a>Ligar à sua base de dados

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]

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
   | **Iniciar sessão** | ID do utilizador da conta de administração do servidor | O ID do utilizador a partir da conta de administração do servidor utilizado para criar o servidor. |
   | **Palavra-passe** | Palavra-passe da conta de administrador do servidor | A palavra-passe da conta de administração do servidor utilizada para criar o servidor. |
   ||||

   ![ligar ao servidor](./media/connect-query-ssms/connect.png)  

> [!NOTE]
> Este tutorial utiliza a Autenticação do Servidor SQL.

3. Selecione **Opções** na caixa de diálogo **'Ligar ao Servidor'.** No menu **'Ligar para baixo' da base de dados,** selecione **mySampleDatabase**. Completar o quickstart na [secção Pré-Requisitos](#prerequisites) cria uma base de dados AdventureWorksLT chamada mySampleDatabase. Se a sua cópia de trabalho da base de dados AdventureWorks tiver um nome diferente do mySampleDatabase, então selecione-a.

   ![ligar à base de dados no servidor](./media/connect-query-ssms/options-connect-to-db.png)  

4. Selecione **Ligar**. É aberta a janela Object Explorer.

5. Para ver os objetos de base de dados, expanda **Bases de dados** e, em seguida, expanda o nó da sua base de dados.

   ![objetos mySampleDatabase](./media/connect-query-ssms/connected.png)  

## <a name="query-data"></a>Consultar dados

Executar este código [SELECT](/sql/t-sql/queries/select-transact-sql/) Transact-SQL para consultar os 20 melhores produtos por categoria.

1. No Object Explorer, clique com o botão direito **mySampleDatabase** e selecione **Nova Consulta**. Abre-se uma nova janela de consulta ligada à sua base de dados.

2. Na janela de consulta, cole a seguinte consulta SQL:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Na barra de ferramentas, **selecione Executar** para executar a consulta e obter dados das `Product` tabelas e `ProductCategory` tabelas.

    ![consulta para recuperar dados de tabela Produto e ProductCategoria](./media/connect-query-ssms/query2.png)

### <a name="insert-data"></a>Inserir dados

Executar este código [INSERT](/sql/t-sql/statements/insert-transact-sql/) Transact-SQL para criar um novo produto na `SalesLT.Product` tabela.

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

2. **Selecione Executar** para inserir uma nova linha na `Product` tabela. O painel **de mensagens** apresenta **(1 linha afetada)**.

#### <a name="view-the-result"></a>Ver o resultado

1. Substitua a consulta anterior por esta.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Selecione **Execute** (Executar). O seguinte resultado aparece.

   ![resultado da consulta da tabela de produtos](./media/connect-query-ssms/result.png)

### <a name="update-data"></a>Atualizar dados

Executar este código [UPDATE](/sql/t-sql/queries/update-transact-sql) Transact-SQL para modificar o seu novo produto.

1. Substitua a consulta anterior por esta que devolve o novo recorde criado anteriormente:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. **Selecione Executar** para atualizar a linha especificada na `Product` tabela. O painel **de mensagens** apresenta **(1 linha afetada)**.

### <a name="delete-data"></a>Eliminar dados

Executar este código [DELETE](/sql/t-sql/statements/delete-transact-sql/) Transact-SQL para remover o seu novo produto.

1. Substitua a consulta anterior por esta.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. **Selecione Executar** para eliminar a linha especificada na `Product` tabela. O painel **de mensagens** apresenta **(1 linha afetada)**.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre o SSMS, consulte [o SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms/).
- Para ligar e consultar utilizando o portal Azure, consulte [Ligar e consultar o editor de consulta do portal Azure SQL](connect-query-portal.md).
- Para ligar e consultar com o Visual Studio Code, veja [Connect and query with Visual Studio Code (Ligar e consultar com o Visual Studio Code)](connect-query-vscode.md).
- Para ligar e consultar com .NET, consulte [Connect and query with .NET (Ligar e consultar com .NET)](connect-query-dotnet-visual-studio.md).
- Para ligar e consultar com PHP, consulte [Connect and query with PHP (Ligar e consultar com PHP)](connect-query-php.md).
- Para ligar e consultar com Node.js, consulte [Connect and query with Node.js (Ligar e consultar com Node.js)](connect-query-nodejs.md).
- Para ligar e consultar com Java, consulte [Connect and query with Java (Ligar e consultar com Java)](connect-query-java.md).
- Para ligar e consultar com Python, consulte [Connect and query with Python (Ligar e consultar com Python)](connect-query-python.md).
- Para ligar e consultar com Ruby, consulte [Connect and query with Ruby (Ligar e consultar com Ruby)](connect-query-ruby.md).
