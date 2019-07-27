---
title: 'VS Code: Conectar-se e consultar dados no Azure SQL Database | Microsoft Docs'
description: Saiba como ligar à Base de Dados SQL no Azure com o Visual Studio Code. Em seguida, execute declarações do Transact-SQL (T-SQL) para consultar e editar dados.
keywords: ligar à base de dados SQL
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
ms.openlocfilehash: d8f12e699c17787d897a7f5ed23eccdbf3659921
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569127"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query-an-azure-sql-database"></a>Início rápido: Usar Visual Studio Code para se conectar e consultar um banco de dados SQL do Azure

[Visual Studio Code](https://code.visualstudio.com/docs) é um editor de código gráfico para Linux, macOS e Windows. Ele suporta extensões, incluindo o [extensão mssql](https://aka.ms/mssql-marketplace) para consultar o Microsoft SQL Server, base de dados do Azure SQL e SQL Data Warehouse. Neste guia de início rápido, você usará Visual Studio Code para se conectar a um banco de dados SQL do Azure e, em seguida, executar instruções Transact-SQL para consultar, inserir, atualizar e excluí-los.

## <a name="prerequisites"></a>Pré-requisitos

- Uma base de dados SQL do Azure. Você pode usar um desses guias de início rápido para criar e, em seguida, configurar um banco de dados no banco de dados SQL do Azure:

  || Base de dados individual | Instância gerida |
  |:--- |:--- |:---|
  | Criar| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurar | [Regra de firewall de IP de nível de servidor](sql-database-server-level-firewall-rule.md)| [Conectividade de uma VM](sql-database-managed-instance-configure-vm.md)|
  |||[Conectividade do local](sql-database-managed-instance-configure-p2s.md)
  |Carregar dados|Adventure Works carregado por início rápido|[Restaurar importadores mundiais](sql-database-managed-instance-get-started-restore.md)
  |||Restaurar ou importar o Adventure Works do arquivo [BACPAC](sql-database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts neste artigo são escritos para usar o banco de dados do Adventure Works. Com uma instância gerenciada, você deve importar o banco de dados do Adventure Works para um banco de dados de instância ou modificar os scripts deste artigo para usar o banco de dados de importadores mundiais.

## <a name="install-visual-studio-code"></a>Instalar Visual Studio Code

Certifique-se de que instalou a versão mais recente [Visual Studio Code](https://code.visualstudio.com/Download) e carregar o [extensão mssql](https://aka.ms/mssql-marketplace). Para obter orientações sobre como instalar a extensão do mssql, consulte [instalar o VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) e [mssql para Visual Studio Code ](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql).

## <a name="configure-visual-studio-code"></a>Configurar o Visual Studio Code

### <a name="mac-os"></a>**Mac OS**

Para o macOS, você precisa instalar o OpenSSL, que é um pré-requisito para o .NET Core que a extensão do MSSQL usa. Abra o terminal e introduza os comandos seguintes para instalar **brew**, **OpenSSL**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Nenhuma configuração especial necessária.

### <a name="windows"></a>**Windows**

Nenhuma configuração especial necessária.

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao banco de dados SQL do Azure. Você precisará do nome do servidor totalmente qualificado ou nome do host, nome do banco de dados e informações de logon para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **instâncias gerenciadas do SQL** .

3. Na página **visão geral** , examine o nome do servidor totalmente qualificado ao lado de **nome do servidor** para um único banco de dados ou o nome do servidor totalmente qualificado ao lado de **host** para uma instância gerenciada. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone de **cópia** .

## <a name="set-language-mode-to-sql"></a>Definir modo do idioma para SQL

No Visual Studio Code, defina o modo de idioma para **SQL** para ativar comandos mssql e IntelliSense T-SQL.

1. Abra uma nova janela do Visual Studio Code.

2. Pressione **Ctrl**+**N**. É aberto um novo ficheiro de texto sem formatação.

3. Selecione **texto sem formatação** no canto inferior direito da barra de estado.

4. Na **selecionar modo de idioma** menu pendente que se abre, selecione **SQL**.

## <a name="connect-to-your-database"></a>Ligar à base de dados

Utilize o Visual Studio Code para estabelecer uma ligação ao servidor da Base de Dados SQL do Azure.

> [!IMPORTANT]
> Antes de continuar, verifique se você tem o servidor e as informações de entrada estão prontas. Assim que começar a introduzir as informações de perfil de ligação, se alterar o foco do Visual Studio Code, terá de reiniciar a criação do perfil.

1. No Visual Studio Code, prima **Ctrl + Shift + P** (ou **F1**) para abrir a paleta de comandos.

2. Selecione **MS SQL: conectar** e escolha **Enter**.

3. Selecione **criar perfil de ligação**.

4. Siga as instruções para especificar propriedades de ligação do novo perfil. Depois de especificar cada valor, escolha **Enter** para continuar.

   | Propriedade       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome do servidor** | O nome de servidor completamente qualificado | Algo como: **mynewserver20170313.database.windows.net**. |
   | **Nome da base de dados** | mySampleDatabase | A base de dados para ligar a. |
   | **Autenticação** | Início de sessão do SQL| Este tutorial utiliza a autenticação do SQL. |
   | **Nome de utilizador** | Nome de utilizador | O nome de utilizador da conta de administrador do servidor utilizado para criar o servidor. |
   | **Palavra-passe (Início de Sessão do SQL)** | Palavra-passe | A palavra-passe da conta de administrador do servidor utilizada para criar o servidor. |
   | **Guardar Palavra-passe?** | Sim ou Não | Selecione **Sim** se não pretender que introduzir a palavra-passe de cada vez. |
   | **Introduza um nome para este perfil** | Nome de um perfil, como **mySampleProfile** | Um perfil guardado acelera a sua ligação nos inícios de sessão subsequentes. |

   Se tiver êxito, é apresentada uma notificação a indicar o seu perfil é criado e ligado.

## <a name="query-data"></a>Consultar dados

Execute a instrução [Select](https://msdn.microsoft.com/library/ms189499.aspx) do TRANSACT-SQL a seguir para consultar os 20 principais produtos por categoria.

1. Na janela do editor, cole a seguinte consulta SQL.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Pressione **Ctrl**+ `ProductCategory` **Shift** **E para** executar a consulta e exibir os resultados das tabelas e.`Product` +

    ![consulta para recuperar dados de 2 tabelas](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Inserir dados

Execute a instrução [Insert](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL a seguir para adicionar um novo produto `SalesLT.Product` à tabela.

1. Substitua a consulta anterior este.

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

2. Prima **Ctrl**+**Shift**+**i** para inserir uma nova linha no `Product` tabela.

## <a name="update-data"></a>Atualizar dados

Execute a seguinte instrução [Update](https://msdn.microsoft.com/library/ms177523.aspx) do TRANSACT-SQL para atualizar o produto adicionado.

1. Substitua a consulta anterior este:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Prima **Ctrl**+**Shift**+**i** para atualizar a linha especificada no `Product` tabela.

## <a name="delete-data"></a>Eliminar dados

Execute a instrução [delete](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) do TRANSACT-SQL a seguir para remover o novo produto.

1. Substitua a consulta anterior este:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Prima **Ctrl**+**Shift**+**i** para eliminar a linha especificada no `Product` tabela.

## <a name="next-steps"></a>Passos Seguintes

- Para se conectar e consultar usando SQL Server Management Studio, [consulte início rápido: Use SQL Server Management Studio para conectar-se a um banco de dados SQL](sql-database-connect-query-ssms.md)do Azure e consultar o dado.
- Para se conectar e consultar usando o portal do Azure, [consulte início rápido: Use o editor de consultas SQL no portal do Azure para se conectar e consultar](sql-database-connect-query-portal.md)dados.
- Para ler um artigo de revista MSDN sobre a utilização do Visual Studio Code, veja [Create a database IDE with MSSQL extension blog post](https://msdn.microsoft.com/magazine/mt809115) (Criar uma base de dados IDE com a mensagem de blogue de extensão do MSSQL).
