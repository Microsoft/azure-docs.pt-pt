---
title: Usar PHP para consultar
description: Como usar o PHP para criar um programa que se conecta a um banco de dados SQL do Azure e consultá-lo usando instruções T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 02/12/2019
ms.openlocfilehash: ae119dd23da670f16c0239b14119519c431e6326
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827002"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Início Rápido: Utilizar o PHP para consultar uma base de dados SQL do Azure

Este artigo demonstra como usar o [php](https://php.net/manual/en/intro-whatis.php) para se conectar a um banco de dados SQL do Azure. Você pode usar instruções T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este exemplo, verifique se você tem os seguintes pré-requisitos:

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

- Software relacionado ao PHP instalado para seu sistema operacional:

  - **MacOS**, instale o PHP, o driver ODBC e instale o driver PHP para SQL Server. Consulte a [etapa 1, 2 e 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**, instale o PHP, o driver ODBC e instale o driver php para SQL Server. Consulte a [etapa 1, 2 e 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**, instale o PHP por IIS Express e Chocolatey e, em seguida, instale o driver ODBC e sqlcmd. Veja os [Passos 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao banco de dados SQL do Azure. Você precisará do nome do servidor totalmente qualificado ou nome do host, nome do banco de dados e informações de logon para os próximos procedimentos.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **instâncias gerenciadas do SQL** .

3. Na página **visão geral** , examine o nome do servidor totalmente qualificado ao lado de **nome do servidor** para um único banco de dados ou o nome do servidor totalmente qualificado ao lado de **host** para uma instância gerenciada. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone de **cópia** .

## <a name="add-code-to-query-database"></a>Adicionar código ao banco de dados de consulta

1. No seu editor de texto favorito, crie um novo ficheiro, *sqltest.php*.  

1. Substitua seu conteúdo pelo código a seguir. Em seguida, adicione os valores apropriados para seu servidor, banco de dados, usuário e senha.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute o aplicativo.

   ```bash
   php sqltest.php
   ```

1. Verifique se as 20 primeiras linhas são retornadas e feche a janela do aplicativo.

## <a name="next-steps"></a>Passos seguintes

- [Criar a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md)

- [Controlador Microsoft PHP para SQL Server](https://github.com/Microsoft/msphpsql/)

- [Report issues or ask questions](https://github.com/Microsoft/msphpsql/issues) (Comunicar problemas ou fazer perguntas)

- [Exemplo de lógica de repetição: conectar-se de forma resiliente ao SQL com PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
