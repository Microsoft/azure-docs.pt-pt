---
title: Use PHP para consultar
description: Como utilizar o PHP para criar um programa que se conecta a uma base de dados na Base de Dados Azure SQL ou na Azure SQL Managed Instance, e questioná-lo utilizando declarações T-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: sqldbrb=2
ms.openlocfilehash: e0ad8e9e71a0ee2c23412d535fe79955edb39ba8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576655"
---
# <a name="quickstart-use-php-to-query-a-database-in-azure-sql-database"></a>Quickstart: Use PHP para consultar uma base de dados na Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo demonstra como utilizar [o PHP](https://php.net/manual/en/intro-whatis.php) para ligar a uma base de dados na Base de Dados Azure SQL ou na Azure SQL Managed Instance. Em seguida, pode utilizar declarações T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, precisa de:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma base de dados na Base de Dados Azure SQL ou na Azure SQL Gerenciada. Pode utilizar um destes quickstarts para criar e, em seguida, configurar uma base de dados:

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




  > [!IMPORTANT]
  > Os scripts deste artigo são escritos para usar a base de dados Adventure Works. Com uma SQL Managed Instance, você deve importar a base de dados Adventure Works em uma base de dados de casos ou modificar os scripts deste artigo para usar a base de dados de importadores do mundo amplo.

- Software relacionado com PHP instalado para o seu sistema operativo:

  - **macOS**, instale PHP, o controlador ODBC e, em seguida, instale o controlador PHP para o SQL Server. Ver [passo 1, 2 e 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**, instale o PHP, o controlador ODBC e, em seguida, instale o controlador PHP para o SQL Server. Ver [passo 1, 2 e 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**, instale os controladores PHP e PHP e instale o controlador ODBC e o SQLCMD. Veja os [Passos 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-server-connection-information"></a>Obtenha informações de ligação do servidor

Obtenha a informação de ligação necessária para ligar à base de dados na Base de Dados Azure SQL. Você precisará do nome do servidor totalmente qualificado ou nome de anfitrião, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para a **página SQL Databases** ou **SQL Managed Instances.**

3. Na página **'Vista Geral',** reveja o nome do servidor totalmente qualificado ao lado **do nome do Servidor** para uma base de dados na Base de Dados Azure SQL ou o nome do servidor (ou endereço IP) totalmente qualificado ao lado do **Anfitrião** para uma Instância Gerida SQL Azure ou servidor SQL num VM Azure. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copy.**

> [!NOTE]
> Para obter informações de ligação para O Servidor SQL em Azure VM, consulte [Conecte-se a uma instância do SqL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="add-code-to-query-the-database"></a>Adicionar código para consultar a base de dados

1. No seu editor de texto favorito, crie um novo ficheiro, *sqltest.php*.  

1. Substitua o seu conteúdo pelo seguinte código. Em seguida, adicione os valores adequados para o seu servidor, base de dados, utilizador e senha.

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

1. Na solicitação de comando, execute a aplicação.

   ```bash
   php sqltest.php
   ```

1. Verifique se as 20 linhas mais próximas são devolvidas e feche a janela da aplicação.

## <a name="next-steps"></a>Passos seguintes

- [Desenhe a sua primeira base de dados na Base de Dados Azure SQL](design-first-database-tutorial.md)
- [Controlador Microsoft PHP para SQL Server](https://github.com/Microsoft/msphpsql/)
- [Report issues or ask questions](https://github.com/Microsoft/msphpsql/issues) (Comunicar problemas ou fazer perguntas)
- [Exemplo lógico relemplitado: Conecte-se resilientemente ao Azure SQL com PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
