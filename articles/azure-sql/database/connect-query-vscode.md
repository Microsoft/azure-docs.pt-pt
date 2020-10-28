---
title: Use código de estúdio visual para ligar e consultar
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Saiba como ligar-se à Base de Dados Azure SQL ou à SQL Managed Instance on Azure utilizando o Código do Estúdio Visual. Em seguida, execute declarações do Transact-SQL (T-SQL) para consultar e editar dados.
keywords: ligar à base de dados SQL
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: f823b6d04a217328fe2e825e64906460cd9cbae9
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92672481"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query"></a>Quickstart: Use o Código do Estúdio Visual para ligar e consultar 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[Visual Studio Code](https://code.visualstudio.com/docs) é um editor de código gráfico para Linux, macOS e Windows. Suporta extensões, incluindo a [extensão de mssql](https://aka.ms/mssql-marketplace) para consulta de uma instância do SQL Server, Base de Dados Azure SQL, uma Instância Gerida Azure SQL e uma base de dados em Azure Synapse Analytics. Neste arranque rápido, utilizará o Código do Estúdio Visual para ligar à Base de Dados Azure SQL ou à Instância Gerida Azure SQL e, em seguida, executará declarações Transact-SQL para consultar, inserir, atualizar e apagar dados.

## <a name="prerequisites"></a>Pré-requisitos

- Uma base de dados na Base de Dados Azure SQL ou na Azure SQL Gerenciada. Pode utilizar um destes quickstarts para criar e, em seguida, configurar uma base de dados na Base de Dados Azure SQL:

  | Ação | Base de Dados SQL do Azure | Instância Gerida do Azure SQL |
  |:--- |:--- |:---|
  | Criar| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) |
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) |
  | Configurar | [Regra de firewall IP de nível do servidor](firewall-create-server-level-portal-quickstart.md))| [Conectividade a partir de uma máquina virtual (VM)](../managed-instance/connect-vm-instance-configure.md)|
  |||[Conectividade a partir de instalações](../managed-instance/point-to-site-p2s-configure.md)
  |Carregar dados|Obras de Aventura carregadas por quickstart|[Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md)
  |||Restaurar ou importar Obras de Aventura a partir de um ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts deste artigo são escritos para usar a base de dados Adventure Works. Com uma SQL Managed Instance, você deve importar a base de dados Adventure Works em uma base de dados de casos ou modificar os scripts deste artigo para usar a base de dados de importadores do mundo amplo.

## <a name="install-visual-studio-code"></a>Instalar o Visual Studio Code

Certifique-se de que instalou o mais recente [Código do Estúdio Visual](https://code.visualstudio.com/Download) e carregou a [extensão mssql](https://aka.ms/mssql-marketplace). Para obter orientações sobre a instalação da extensão mssql, consulte instalar o [Código do Estúdio Visual](/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) e o [mssql para Código de Estúdio Visual ](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql).

## <a name="configure-visual-studio-code"></a>Configurar o Visual Studio Code

### <a name="macos"></a>**macOS**

Para o macOS, é necessário instalar o OpenSSL, que é um pré-requisito para o .NET Core que a extensão mssql utiliza. Abra o terminal e introduza os comandos seguintes para instalar **brew** , **OpenSSL** .

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

## <a name="get-server-connection-information"></a>Obtenha informações de ligação do servidor

Obtenha a informação de ligação necessária para ligar à Base de Dados Azure SQL. Você precisará do nome do servidor totalmente qualificado ou nome de anfitrião, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Navegue para as **bases de dados SQL** ou página **sql Managed Instances.**

3. Na página **'Vista Geral',** reveja o nome do servidor totalmente qualificado ao lado **do nome do Servidor** para base de dados SQL ou o nome do servidor totalmente qualificado ao lado do **Anfitrião** para uma Ocorrência Gerida SQL. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copy.**

## <a name="set-language-mode-to-sql"></a>Definir modo do idioma para SQL

No Código do Estúdio Visual, deite o modo de linguagem para **SQL**  para permitir comandos mssql e T-SQL IntelliSense.

1. Abra uma nova janela do Visual Studio Code.

2. Pressione **Ctrl** + **N** . Abre-se um novo ficheiro de texto simples.

3. Selecione **Texto Simples** no canto inferior direito da barra de estado.

4. No menu suspenso do **modo linguístico Select** que abre, selecione **SQL** .

## <a name="connect-to-your-database"></a>Ligar à sua base de dados

Utilize o Código do Estúdio Visual para estabelecer uma ligação ao seu servidor.

> [!IMPORTANT]
> Antes de continuar, certifique-se de que tem o seu servidor e inscreva-se na informação. Assim que começar a introduzir as informações do perfil de ligação, se alterar o seu foco a partir do Código do Estúdio Visual, terá de reiniciar a criação do perfil.

1. No Código do Estúdio Visual, prima **Ctrl+Shift+P** (ou **F1)** para abrir a Paleta de Comando.

2. Selecione **MS SQL:Conecte-se** e escolha **Entrar.**

3. Selecione **Criar Perfil de Ligação** .

4. Siga as indicações para especificar as propriedades de ligação do novo perfil. Depois de especificar cada valor, escolha **Enter** para continuar.

   | Propriedade       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome do servidor** | O nome de servidor completamente qualificado | Algo como: **mynewserver20170313.database.windows.net.** |
   | **Nome da base de dados** | mySampleDatabase | A base de dados para ligar. |
   | **Autenticação** | Início de sessão do SQL| Este tutorial utiliza a Autenticação SQL. |
   | **Nome de utilizador** | Nome de utilizador | O nome de utilizador da conta de administração do servidor utilizado para criar o servidor. |
   | **Palavra-passe (Início de Sessão do SQL)** | Palavra-passe | A palavra-passe da conta de administração do servidor utilizada para criar o servidor. |
   | **Guardar Palavra-passe?** | Sim ou Não | Selecione **Sim** se não quiser introduzir a palavra-passe de cada vez. |
   | **Introduza um nome para este perfil** | Um nome de perfil, como **mySampleProfile** | Um perfil guardado acelera a sua ligação em logins subsequentes. |

   Se for bem sucedido, aparece uma notificação a dizer que o seu perfil está criado e conectado.

## <a name="query-data"></a>Consultar dados

Execute a seguinte declaração [SELECT](/sql/t-sql/queries/select-transact-sql) Transact-SQL para consultar os 20 melhores produtos por categoria.

1. Na janela do editor, cole a seguinte consulta SQL.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Prima **Ctrl** + **Shift** + **E** para executar a consulta e mostrar os resultados das `Product` tabelas e `ProductCategory` tabelas.

    ![Consulta para recuperar dados de 2 tabelas](./media/connect-query-vscode/query.png)

## <a name="insert-data"></a>Inserir dados

Executar a seguinte declaração [INSERT](/sql/t-sql/statements/insert-transact-sql) Transact-SQL para adicionar um novo produto na `SalesLT.Product` tabela.

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

2. Prima **Ctrl** + **Shift** + **E** para inserir uma nova linha na `Product` tabela.

## <a name="update-data"></a>Atualizar dados

Executar a seguinte [declaração DE UPDATE](/sql/t-sql/queries/update-transact-sql) Transact-SQL para atualizar o produto adicionado.

1. Substitua a consulta anterior por esta:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Prima **Ctrl** + **Shift** + **E** para atualizar a linha especificada na `Product` tabela.

## <a name="delete-data"></a>Eliminar dados

Executar a seguinte declaração [DELETE](/sql/t-sql/statements/delete-transact-sql) Transact-SQL para remover o novo produto.

1. Substitua a consulta anterior por esta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Prima **Ctrl** + **Shift** + **E** para eliminar a linha especificada na `Product` tabela.

## <a name="next-steps"></a>Passos seguintes

- Para ligar e consultar o SQL Server Management Studio, consulte [quickstart: Use SQL Server Management Studio para ligar a uma base de dados na Base de Dados Azure SQL e dados de consulta](connect-query-ssms.md).
- Para ligar e consultar utilizando o portal Azure, consulte [Quickstart: Utilize o editor de consulta SQL no portal Azure para ligar e consultar dados](connect-query-portal.md).
- Para ler um artigo de revista MSDN sobre a utilização do Visual Studio Code, veja [Create a database IDE with MSSQL extension blog post](/archive/msdn-magazine/2017/june/data-points-visual-studio-code-create-a-database-ide-with-mssql-extension) (Criar uma base de dados IDE com a mensagem de blogue de extensão do MSSQL).