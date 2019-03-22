---
title: 'SSMS: Ligar e consultar dados numa base de dados SQL do Azure | Documentos da Microsoft'
description: Saiba como ligar à Base de Dados SQL no Azure, utilizando o SQL Server Management Studio (SSMS). Em seguida, execute declarações do Transact-SQL (T-SQL) para consultar e editar dados.
keywords: ligar à base de dados sql, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/12/2019
ms.openlocfilehash: efdb82f891b21fa03a338c56d3f3e897e684bdf6
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337075"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Início rápido: Utilizar o SQL Server Management Studio para se ligar e consultar uma base de dados SQL do Azure

Neste início rápido, vai utilizar [SQL Server Management Studio] [ ssms-install-latest-84g] (SSMS) para ligar a uma base de dados SQL do Azure. Em seguida, execute instruções Transact-SQL para consultar, inserir, atualizar e eliminar dados. Pode utilizar o SSMS para gerir qualquer infraestrutura SQL, do SQL Server à base de dados SQL para Microsoft Windows.  

## <a name="prerequisites"></a>Pré-requisitos

- Uma base de dados SQL do Azure. Pode utilizar um dos seguintes inícios rápidos para criar e, em seguida, configurar uma base de dados na base de dados do Azure SQL:

  || Base de dados individual | Instância gerida |
  |:--- |:--- |:---|
  | Criar| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) |
  | Configurar | [regra de firewall do IP ao nível do servidor](sql-database-server-level-firewall-rule.md)| [Conectividade a partir de uma VM](sql-database-managed-instance-configure-vm.md)|
  |||[Conectividade no local do](sql-database-managed-instance-configure-p2s.md)
  |Carregar dados|A Adventure Works carregados por início rápido|[Restaurar a Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Restaure ou importar Adventure Works no [BACPAC](sql-database-import.md) ficheiro [github](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts neste artigo são escritos para utilizar a base de dados do Adventure Works. Com uma instância gerida, tem de importar a base de dados do Adventure Works para uma base de dados de instância ou modificar os scripts neste artigo para utilizar a base de dados do Wide World Importers.

## <a name="install-the-latest-ssms"></a>Instalar o SSMS mais recente

Antes de começar, certifique-se de que instalou a versão mais recente [SSMS][ssms-install-latest-84g]. 

## <a name="get-sql-server-connection-information"></a>Obter as informações de ligação do SQL server

Obtenha as informações de ligação que tem de se ligar à base de dados SQL do Azure. Precisará do nome de servidor completamente qualificado ou nome de anfitrião, nome de base de dados e informações de início de sessão para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para o **bases de dados SQL** ou **instâncias geridas SQL** página.

3. Sobre o **descrição geral** , reveja o nome de servidor completamente qualificado junto a **nome do servidor de** para uma base de dados ou o servidor completamente qualificado nome junto a **anfitrião** para um instância. Para copiar o nome do servidor ou o nome de anfitrião, coloque o cursor sobre ela e selecione o **cópia** ícone.

## <a name="connect-to-your-database"></a>Ligar à base de dados

No SMSS, ligue ao seu servidor de base de dados do Azure SQL. 

> [!IMPORTANT]
> Um servidor de base de dados do Azure SQL escuta na porta 1433. Para ligar a um servidor de base de dados SQL através de uma firewall empresarial, o firewall tem de ter esta porta abrir.
>

1. Abra o SQL Server Management Studio. A caixa de diálogo **Ligar ao Servidor** é apresentada.

2. Introduza as seguintes informações:

   | Definição      | Valor sugerido    | Descrição | 
   | ------------ | ------------------ | ----------- | 
   | **Tipo de servidor** | Motor de base de dados | Valor obrigatório. |
   | **Nome do servidor** | O nome de servidor completamente qualificado | Algo como: **mynewserver20170313.database.windows.net**. |
   | **Autenticação** | Autenticação do SQL Server | Este tutorial utiliza a autenticação do SQL. |
   | **Início de sessão** | ID de utilizador da conta de administrador de servidor | O ID de utilizador da conta de administrador de servidor utilizado para criar o servidor. |
   | **Palavra-passe** | Senha de conta de administrador do servidor | A palavra-passe da conta de administrador de servidor utilizada para criar o servidor. |
   ||||

   ![ligar ao servidor](./media/sql-database-connect-query-ssms/connect.png)  

3. Selecione **opções** no **ligar ao servidor** caixa de diálogo. Na **ligar à base de dados** menu pendente, selecione **mySampleDatabase**.

   ![ligar à base de dados no servidor](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Selecione **Ligar**. É aberta a janela do Object Explorer. 

5. Para ver os objetos da base de dados, expanda **bases de dados** e, em seguida, expanda **mySampleDatabase**.

   ![ver os objetos de base de dados](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Consultar dados

Executar isso [SELECIONE](https://msdn.microsoft.com/library/ms189499.aspx) código Transact-SQL para consultar os 20 melhores produtos por categoria.

1. No Object Explorer, clique com botão direito **mySampleDatabase** e selecione **nova consulta**. É aberta a janela de consulta nova ligada à base de dados.

2. Na janela da consulta, cole esta consulta SQL.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Na barra de ferramentas, selecione **Execute** para obter dados a partir do `Product` e `ProductCategory` tabelas.

    ![consulta para recuperar dados de duas tabelas](./media/sql-database-connect-query-ssms/query2.png)

## <a name="insert-data"></a>Inserir dados

Executar isso [inserir](https://msdn.microsoft.com/library/ms174335.aspx) código Transact-SQL para criar um novo produto no `SalesLT.Product` tabela.

1. Substitua a consulta anterior este.

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

2. Selecione **Execute** para inserir uma nova linha no `Product` tabela. O **mensagens** painel apresenta **(1 linhas afetadas)**.

## <a name="view-the-result"></a>Ver o resultado

1. Substitua a consulta anterior este.

   ```sql
   SELECT * FROM [SalesLT].[Product] 
   WHERE Name='myNewProduct' 
   ```
   
2. Selecione **Executar**. O resultado seguinte é apresentada. 

   ![Resultado](./media/sql-database-connect-query-ssms/result.png)

 
## <a name="update-data"></a>Atualizar dados

Executar isso [ATUALIZAÇÃO](https://msdn.microsoft.com/library/ms177523.aspx) código Transact-SQL para modificar o seu novo produto.

1. Substitua a consulta anterior este.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Selecione **Execute** para atualizar a linha especificada no `Product` tabela. O **mensagens** painel apresenta **(1 linhas afetadas)**.

## <a name="delete-data"></a>Eliminar dados

Executar isso [eliminar](https://msdn.microsoft.com/library/ms189835.aspx) código Transact-SQL para remover o seu novo produto.

1. Substitua a consulta anterior este.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Selecione **Execute** para eliminar a linha especificada no `Product` tabela. O **mensagens** painel apresenta **(1 linhas afetadas)**.

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre o SSMS, consulte [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
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

