---
title: Utilize Node.js para consultar uma base de dados
description: Como utilizar Node.js criar um programa que se conecta a uma base de dados na Base de Dados Azure SQL ou na Azure SQL Managed Instance, e questioná-lo utilizando declarações T-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: seo-javascript-september2019, seo-javascript-october2019, sqldbrb=2, devx-track-js
ms.openlocfilehash: 44530577972839aacb803d1722fa97716088fa0d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91325441"
---
# <a name="quickstart-use-nodejs-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Quickstart: Utilize Node.js para consultar uma base de dados na Base de Dados Azure SQL ou na Azure SQL Gerenciada Instância
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Neste arranque rápido, utiliza-se Node.js para ligar a uma base de dados e consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, precisa de:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

  | Ação | SQL Database | Instância Gerida do SQL | SQL Server numa VM do Azure |
  |:--- |:--- |:---|:---|
  | Criar| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configurar | [Regra de firewall IP de nível de servidor](firewall-create-server-level-portal-quickstart.md)| [Conectividade de um VM](../managed-instance/connect-vm-instance-configure.md)|
  |||[Conectividade a partir de instalações](../managed-instance/point-to-site-p2s-configure.md) | [Ligue-se a uma instância do Servidor SQL](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Carregar dados|Obras de Aventura carregadas por quickstart|[Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md) | [Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md) |
  |||Restaurar ou importar Obras de Aventura a partir de um ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Restaurar ou importar Obras de Aventura a partir de um ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||


- [Node.js-relacionado ](https://nodejs.org)com o software

  # <a name="macos"></a>[macOS](#tab/macos)

  Instale o Homebrew e o Node.js e, em seguida, instale o controlador ODBC e o SQLCMD utilizando os passos **1.2** e **1.3** em [Apps Create Node.js utilizando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Instale Node.js e, em seguida, instale o controlador ODBC e o SQLCMD utilizando os passos **1.2** e **1.3** em [aplicações Create Node.js utilizando o SQL Server em Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Instale chocolate e Node.js e, em seguida, instale o controlador ODBC e o SQLCMD utilizando os passos **1.2** e **1.3** em [Aplicações Create Node.js utilizando](https://www.microsoft.com/sql-server/developer-get-started/node/windows/)o SQL Server no Windows .

  ---

> [!IMPORTANT]
> Os scripts deste artigo são escritos para usar a base de dados **Adventure Works.**

> [!NOTE]
> Pode optar opcionalmente por utilizar uma Instância Gerida Azure SQL.
>
> Para criar e configurar, utilize o [portal Azure](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md), ou [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), e, em seguida, instale [no local](../managed-instance/point-to-site-p2s-configure.md) ou conectividade [VM.](../managed-instance/connect-vm-instance-configure.md)
>
> Para carregar dados, consulte [restaurar com BACPAC](database-import.md) com o ficheiro [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) ou ver restaurar a base [de dados de importadores do Mundo Largo.](../managed-instance/restore-sample-database-quickstart.md)

## <a name="get-server-connection-information"></a>Obtenha informações de ligação do servidor

Obtenha a informação de ligação necessária para ligar à base de dados na Base de Dados Azure SQL. Você precisará do nome do servidor totalmente qualificado ou nome de anfitrião, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Aceda à página **SQL Databases** ou **SQL Managed Instances.**

3. Na página **'Vista Geral',** reveja o nome do servidor totalmente qualificado ao lado **do nome do Servidor** para uma base de dados na Base de Dados Azure SQL ou o nome do servidor (ou endereço IP) totalmente qualificado ao lado do **Anfitrião** para uma Instância Gerida SQL Azure ou servidor SQL em Azure VM. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copy.**

> [!NOTE]
> Para obter informações de ligação para O Servidor SQL em Azure VM, consulte [Connect to SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-the-project"></a>Criar o projeto

Abra uma linha de comandos e crie uma pasta com o nome *sqltest*. Abra a pasta que criou e executar o seguinte comando:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-the-database"></a>Adicionar código para consultar a base de dados

1. No seu editor de texto favorito, crie um novo ficheiro, *sqltest.js*.

1. Substitua o seu conteúdo pelo seguinte código. Em seguida, adicione os valores adequados para o seu servidor, base de dados, utilizador e senha.

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
> O exemplo de código utiliza a base de dados de **amostras AdventureWorksLT** na Base de Dados Azure SQL.

## <a name="run-the-code"></a>Executar o código

1. Na origem do comando, executar o programa.

    ```bash
    node sqltest.js
    ```

1. Verifique se as 20 linhas superiores são devolvidas e feche a janela de aplicação.

## <a name="next-steps"></a>Passos seguintes

- [Controlador Microsoft Node.js para SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Conecte e questione windows/Linux/macOS com [.NET core](connect-query-dotnet-core.md), [Visual Studio Code,](connect-query-vscode.md)ou [SSMS](connect-query-ssms.md) (apenas Windows)

- [Comece com .NET Core no Windows/Linux/macOS utilizando a linha de comando](/dotnet/core/tutorials/using-with-xplat-cli)

- Desenhe a sua primeira base de dados na Base de Dados Azure SQL utilizando [.NET](design-first-database-csharp-tutorial.md) ou [SSMS](design-first-database-tutorial.md)
