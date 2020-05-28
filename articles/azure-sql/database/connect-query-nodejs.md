---
title: Use Node.js para consultar uma base de dados
description: Como usar o Node.js para criar um programa que se conecta a uma base de dados em Azure SQL Database e consulta-lo usando declarações T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019, sqldbrb=2 
ms.openlocfilehash: 5f53d6b3e8b477d7b93eb1063679126a9533ef03
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054480"
---
# <a name="quickstart-use-nodejs-to-query-a-microsoft-azure-sql-database"></a>Quickstart: Use Node.js para consultar uma base de dados Microsoft Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste arranque rápido, utiliza o Node.js para se ligar a uma base de dados Azure SQL e utilizar declarações T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, necessita de:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

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


- Software relacionado com [node.js](https://nodejs.org)

  # <a name="macos"></a>[macOS](#tab/macos)

  Instale homebrew e Node.js, em seguida, instale o controlador ODBC e o SQLCMD utilizando os passos **1.2** e **1.3** nas [aplicações Create Node.js utilizando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Instale o Node.js e, em seguida, instale o controlador ODBC e o SQLCMD utilizando os passos **1.2** e **1.3** nas [aplicações Create Node.js utilizando o SQL Server em Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Instale as aplicações Chocolatey e Node.js e, em seguida, instale o controlador ODBC e o SQLCMD utilizando os passos **1.2** e **1.3** nas [aplicações Create Node.js utilizando o Servidor SQL no Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> Os scripts deste artigo são escritos para usar a base de dados **Adventure Works.**

> [!NOTE]
> Pode optar opcionalmente por utilizar uma Instância Gerida Azure SQL.
>
> Para criar e configurar, utilize o [portal Azure,](../managed-instance/instance-create-quickstart.md) [PowerShell,](../managed-instance/scripts/create-configure-managed-instance-powershell.md)ou [CLI,](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)em seguida, configurar [no local](../managed-instance/point-to-site-p2s-configure.md) ou [conectividade VM.](../managed-instance/connect-vm-instance-configure.md)
>
> Para carregar dados, consulte restaurar com o [FICHEIRO BACPAC](database-import.md) com o ficheiro [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) ou ver restaurar a base de dados dos [Importadores](../managed-instance/restore-sample-database-quickstart.md)do Mundo Wide .

## <a name="get-sql-server-connection-information"></a>Obtenha informações de ligação ao servidor SQL

Obtenha as informações de ligação que precisa para ligar à Base de Dados Azure SQL. Necessitará do nome do servidor ou nome do anfitrião totalmente qualificado, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Vá para as Bases de **Dados SQL** ou **página SQL Managed Instances.**

3. Na página **Overview,** reveja o nome do servidor totalmente qualificado ao lado do **nome do Servidor** para uma Base de Dados SQL Azure ou o nome de servidor totalmente qualificado (ou endereço IP) ao lado do **Host** para um Caso Gerido Azure SQL ou Servidor SQL num VM Azure. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copiar.**

> [!NOTE]
> Para obter informações de ligação para o Servidor SQL num VM Azure, consulte [Connect to SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="create-the-project"></a>Criar o projeto

Abra uma linha de comandos e crie uma pasta com o nome *sqltest*. Abra a pasta que criou e execute o seguinte comando:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>Adicione código à base de dados de consulta

1. No seu editor de texto favorito, crie um novo ficheiro, *sqltest.js*.

1. Substitua o seu conteúdo pelo seguinte código. Em seguida, adicione os valores apropriados para o seu servidor, base de dados, utilizador e senha.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
          console.log("%s\t%s", column.metadata.colName, column.value);
        });
      });
      
      connection.execSql(request);
    }
    ```

> [!NOTE]
> O exemplo de código utiliza a base de dados da amostra **AdventureWorksLT** para o Azure SQL.

## <a name="run-the-code"></a>Executar o código

1. No pedido de comando, executar o programa.

    ```bash
    node sqltest.js
    ```

1. Verifique se as 20 linhas superiores são devolvidas e feche a janela de aplicação.

## <a name="next-steps"></a>Próximos passos

- [Controlador Microsoft Node.js para SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Conecte e consulta no Windows/Linux/macOS com [.NET core,](connect-query-dotnet-core.md) [Visual Studio Code](connect-query-vscode.md), ou [SSMS](connect-query-ssms.md) (apenas windows)

- [Inicie com .NET Core no Windows/Linux/macOS utilizando a linha de comando](/dotnet/core/tutorials/using-with-xplat-cli)

- Desenhe a sua primeira Base de Dados Azure SQL utilizando [.NET](design-first-database-csharp-tutorial.md) ou [SSMS](design-first-database-tutorial.md)
