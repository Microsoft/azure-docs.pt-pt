---
title: 'Portal do Azure: consulta usando o editor de consultas'
description: Saiba como ligar à Base de Dados SQL no portal do Azure através do Editor de Consultas SQL. Em seguida, execute declarações do Transact-SQL (T-SQL) para consultar e editar dados.
keywords: ligar à base de dados sql, portal do azure, portal, editor de consultas
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 10/24/2019
ms.openlocfilehash: 3990d7ec63c312d38168fe76269e1a920f1a6817
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827117"
---
# <a name="quickstart-use-the-azure-portals-sql-query-editor-to-connect-and-query-data"></a>Início rápido: usar o editor de consultas SQL do portal do Azure para se conectar e consultar dados

O editor de consultas SQL é uma ferramenta de navegador de portal do Azure que fornece uma maneira fácil de executar consultas SQL no banco de dados SQL do Azure ou no Azure SQL Data Warehouse. Neste guia de início rápido, você usará o editor de consultas para se conectar a um banco de dados SQL e, em seguida, executar instruções Transact-SQL para consultar, inserir, atualizar e excluir.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

- Uma base de dados SQL do Azure. Você pode usar um desses guias de início rápido para criar e, em seguida, configurar um banco de dados no banco de dados SQL do Azure:

  || Base de dados individual |
  |:--- |:--- |
  | Criar| [Portal](sql-database-single-database-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Configurar | [Regra de firewall de IP de nível de servidor](sql-database-server-level-firewall-rule.md)|
  |||

> [!NOTE]
> O editor de consultas usa as portas 443 e 1443 para se comunicar.  Verifique se você habilitou o tráfego HTTPS de saída nessas portas. Você também precisará adicionar seu endereço IP de saída às regras de firewall permitidas do servidor para acessar seus bancos de dados e data warehouses.

## <a name="sign-in-the-azure-portal"></a>Entrar no portal do Azure

Iniciar sessão no [portal do Azure](https://portal.azure.com/).

## <a name="connect-using-sql-authentication"></a>Conectar usando a autenticação do SQL

1. Vá para a portal do Azure para se conectar a um banco de dados SQL. Pesquise e selecione **bancos de dados SQL**.

    ![Navegue até a lista de banco de dados SQL, portal do Azure](./media/sql-database-connect-query-portal/search-for-sql-databases.png)

2. Selecione seu banco de dados SQL.

    ![Selecione um banco de dados SQL portal do Azure](./media/sql-database-connect-query-portal/select-a-sql-database.png)

3. No menu **banco de dados SQL** , selecione **Editor de consultas (visualização)** .

    ![encontrar editor de consultas](./media/sql-database-connect-query-portal/find-query-editor.PNG)

4. Na página de **logon** , sob o rótulo de **autenticação do SQL Server** , insira a ID de **logon** e a **senha** da conta de administrador do servidor usada para criar o banco de dados. Em seguida, selecione **OK**.

    ![iniciar sessão](./media/sql-database-connect-query-portal/login-menu.png)

## <a name="connect-using-azure-active-directory"></a>Conectar usando Azure Active Directory

Configurar um administrador do Azure Active Directory (AD do Azure) permite que você use uma única identidade para entrar no portal do Azure e no banco de dados SQL. Siga as etapas abaixo para configurar um administrador do Azure AD para o SQL Server.

> [!NOTE]
> * Contas de email (por exemplo, outlook.com, gmail.com, yahoo.com e assim por diante) ainda não têm suporte como administradores do Azure AD. Certifique-se de escolher um usuário criado nativamente no Azure AD ou federado no Azure AD.
> * A entrada de administrador do Azure AD não funciona com contas que têm a autenticação de 2 fatores habilitada.

1. No menu portal do Azure ou na **Home** Page do, selecione **todos os recursos**.

2. Selecione o SQL Server.

3. No menu **SQL Server** , em **configurações**, selecione **Active Directory admin**.

4. Na barra de ferramentas da página do administrador do SQL Server **Active Directory** , selecione **definir administrador** e escolha o usuário ou grupo como administrador do Azure AD.

    ![selecionar o active directory](./media/sql-database-connect-query-portal/select-active-directory.png)

5. Na página **Adicionar Administrador** , na caixa de pesquisa, insira um usuário ou grupo a ser localizado, selecione-o como administrador e, em seguida, escolha o botão **selecionar** .

6. De volta à barra de ferramentas da página do administrador do SQL Server **Active Directory** , selecione **salvar**.

7. No menu **SQL Server** , selecione bancos de dados **SQL**e, em seguida, selecione o banco de dados SQL.

8. No menu **banco de dados SQL** , selecione **Editor de consultas (visualização)** . Na página de **logon** , sob o **Active Directory** rótulo de autenticação, será exibida uma mensagem informando que você foi conectado se você for um administrador do Azure AD. Em seguida, selecione o botão **continuar como** *\<sua ID de usuário ou de grupo >* .

## <a name="view-data"></a>Ver dados

1. Depois que você estiver autenticado, Cole o seguinte SQL no editor de consultas para recuperar os 20 principais produtos por categoria.

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Na barra de ferramentas, selecione **executar** e examine a saída no painel **resultados** .

   ![Resultados do editor de consultas](./media/sql-database-connect-query-portal/query-editor-results.png)

## <a name="insert-data"></a>Inserir dados

Execute a instrução [Insert](https://msdn.microsoft.com/library/ms174335.aspx) TRANSACT-SQL a seguir para adicionar um novo produto na tabela `SalesLT.Product`.

1. Substitua a consulta anterior por esta.

    ```sql
    INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
    VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```


2. Selecione **executar** para inserir uma nova linha na tabela `Product`. O painel **mensagens** exibe **consulta bem-sucedida: linhas afetadas: 1**.


## <a name="update-data"></a>Atualizar dados

Execute a seguinte instrução [Update](https://msdn.microsoft.com/library/ms177523.aspx) do TRANSACT-SQL para modificar o novo produto.

1. Substitua a consulta anterior por esta.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Selecione **executar** para atualizar a linha especificada na tabela `Product`. O painel **mensagens** exibe **consulta bem-sucedida: linhas afetadas: 1**.

## <a name="delete-data"></a>Eliminar dados

Execute a instrução [delete](https://msdn.microsoft.com/library/ms189835.aspx) do TRANSACT-SQL a seguir para remover o novo produto.

1. Substitua a consulta anterior por esta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Selecione **executar** para excluir a linha especificada na tabela `Product`. O painel **mensagens** exibe **consulta bem-sucedida: linhas afetadas: 1**.


## <a name="query-editor-considerations"></a>Considerações sobre o editor de consultas

Há algumas coisas que você precisa saber ao trabalhar com o editor de consultas.

* O editor de consultas usa as portas 443 e 1443 para se comunicar.  Verifique se você habilitou o tráfego HTTPS de saída nessas portas. Você também precisará adicionar seu endereço IP de saída às regras de firewall permitidas do servidor para acessar seus bancos de dados e data warehouses.

* Pressionar F5 atualiza a página do editor de consultas e qualquer consulta que esteja sendo realizada é perdida.

* O editor de consultas não dá suporte à conexão com o banco de dados `master`.

* Há um tempo limite de 5 minutos para a execução da consulta.

* O editor de consultas só dá suporte à Projeção cilíndrica para tipos de dados geography.

* Não há suporte para IntelliSense para tabelas e exibições de banco de dados. No entanto, o editor oferece suporte a AutoCompletar em nomes que já foram digitados.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Transact-SQL com suporte em bancos de dados SQL do Azure, consulte [resolvendo diferenças de Transact-SQL durante a migração para o banco de dados SQL](sql-database-transact-sql-information.md).
