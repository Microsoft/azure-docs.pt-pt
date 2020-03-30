---
title: Use PHP para consultar
description: Como utilizar php para criar um programa que se conecta a uma base de dados Azure SQL e questioná-lo usando declarações T-SQL.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "73827002"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Início Rápido: Utilizar o PHP para consultar uma base de dados SQL do Azure

Este artigo demonstra como usar [PHP](https://php.net/manual/en/intro-whatis.php) para ligar a uma base de dados Azure SQL. Em seguida, pode utilizar declarações T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para completar esta amostra, certifique-se de que tem os seguintes pré-requisitos:

- Uma base de dados SQL do Azure. Pode utilizar um destes quickstarts para criar e, em seguida, configurar uma base de dados na Base de Dados Azure SQL:

  || Base de dados individual | Instância gerida |
  |:--- |:--- |:---|
  | Criar| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurar | [Regra de firewall IP ao nível do servidor](sql-database-server-level-firewall-rule.md)| [Conectividade a partir de um VM](sql-database-managed-instance-configure-vm.md)|
  |||[Conectividade a partir do local](sql-database-managed-instance-configure-p2s.md)
  |Carregar dados|Obras de Aventura carregadas por quickstart|[Restaurar importadores mundiais](sql-database-managed-instance-get-started-restore.md)
  |||Restaurar ou importar Obras de Aventura a partir do ficheiro [BACPAC](sql-database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts deste artigo são escritos para usar a base de dados Adventure Works. Com um caso gerido, deve importar a base de dados da Adventure Works numa base de dados de instâncias ou modificar os scripts deste artigo para utilizar a base de dados dos Importadores do Mundo.

- Software relacionado com PHP instalado para o seu sistema operativo:

  - **MacOS,** instale PHP, o controlador ODBC e, em seguida, instale o controlador PHP para o Servidor SQL. Ver [passo 1, 2 e 3.](/sql/connect/php/installation-tutorial-linux-mac)

  - **Linux,** instale PHP, o controlador ODBC e, em seguida, instale o controlador PHP para o Servidor SQL. Ver [passo 1, 2 e 3.](/sql/connect/php/installation-tutorial-linux-mac)

  - **Windows**, instale PHP para IIS Express e Chocolatey, em seguida, instale o controlador ODBC e o SQLCMD. Veja os [Passos 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-sql-server-connection-information"></a>Obtenha informações de ligação ao servidor SQL

Obtenha as informações de ligação que precisa para ligar à base de dados Azure SQL. Necessitará do nome do servidor ou nome do anfitrião totalmente qualificado, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Navegue para as bases de **dados SQL** ou página de **instâncias geridas pela SQL.**

3. Na página **Overview,** reveja o nome do servidor totalmente qualificado ao lado do **nome do Servidor** para uma única base de dados ou o nome de servidor totalmente qualificado ao lado do **Anfitrião** para uma instância gerida. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copiar.**

## <a name="add-code-to-query-database"></a>Adicione código à base de dados de consulta

1. No seu editor de texto favorito, crie um novo ficheiro, *sqltest.php*.  

1. Substitua o seu conteúdo pelo seguinte código. Em seguida, adicione os valores apropriados para o seu servidor, base de dados, utilizador e senha.

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

1. No pedido de comando, executar a aplicação.

   ```bash
   php sqltest.php
   ```

1. Verifique se as 20 linhas superiores são devolvidas e feche a janela da aplicação.

## <a name="next-steps"></a>Passos seguintes

- [Criar a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md)

- [Controlador Microsoft PHP para SQL Server](https://github.com/Microsoft/msphpsql/)

- [Report issues or ask questions](https://github.com/Microsoft/msphpsql/issues) (Comunicar problemas ou fazer perguntas)

- [Exemplo de lógica de repetição: ligar de forma resiliente ao SQL com o PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
