---
title: Ligar ao Azure SQL Data Warehouse | Microsoft Docs
description: Conecte-se ao Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 71f5c8ca56bc188c0664604a78c38a05be3c3b01
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479737"
---
# <a name="connect-to-azure-sql-data-warehouse"></a>Ligar ao Azure SQL Data Warehouse
Conecte-se ao Azure SQL Data Warehouse.

## <a name="find-your-server-name"></a>Encontrar o nome do servidor
O nome do servidor no exemplo a seguir é samplesvr.database.windows.net. Descubra o nome de servidor completamente qualificado:

1. Aceda ao [Portal do Azure][Azure portal].
2. Clique em **Armazéns de dados SQL**.
3. Clique no armazém de dados à qual se quer ligar.
4. Localize o nome do servidor completo.
   
    ![Nome de servidor completo][1]

## <a name="supported-drivers-and-connection-strings"></a>Controladores e cadeias de ligação suportados
O Azure SQL data warehouse dá suporte a [ADO.net][ADO.NET], [ODBC][ODBC], [php][php]e [JDBC][JDBC]. Para encontrar a versão e a documentação mais recentes, clique em um dos drivers anteriores. Para gerar automaticamente a cadeia de conexão para o driver que você está usando na portal do Azure, clique em **Mostrar cadeias de conexão de banco de dados** do exemplo anterior. Seguem-se também alguns exemplos do aspeto de uma cadeia de ligação para cada controlador.

> [!NOTE]
> Considere definir o tempo limite da ligação como 300 segundos, para permitir que a ligação sobreviva a curtos períodos de indisponibilidade.
> 
> 

### <a name="adonet-connection-string-example"></a>Exemplo de cadeia de ligação de ADO.NET
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Exemplo de cadeia de ligação de ODBC
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Exemplo de cadeia de ligação de PHP
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Exemplo de cadeia de ligação de JDBC
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Definições de ligação
O SQL Data Warehouse uniformiza algumas definições durante a ligação e a criação do objeto. Estas definições não podem ser substituídas e incluem:

| Definição de base de dados | Value |
|:--- |:--- |
| [ANSI_NULLS][ANSI_NULLS] |ATIVADO |
| [QUOTED_IDENTIFIERS][QUOTED_IDENTIFIERS] |ATIVADO |
| [DATEFORMAT][DATEFORMAT] |mdy |
| [DATEFIRST][DATEFIRST] |7 |

## <a name="next-steps"></a>Passos Seguintes
Para se conectar e consultar com o Visual Studio, consulte [consultar com o Visual Studio][Query with Visual Studio]. To learn more about authentication options, see [Authentication to Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse].

<!--Articles-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/server-connect.PNG


