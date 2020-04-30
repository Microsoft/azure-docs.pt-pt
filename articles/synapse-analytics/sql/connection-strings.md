---
title: Cordas de ligação para Synapse SQL (pré-visualização)
description: Cordas de ligação para Synapse SQL (pré-visualização)
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 8c1f13449c8ee5152e4e5b48af87f41004e599dc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424560"
---
# <a name="connection-strings-for-synapse-sql-preview"></a>Cordas de ligação para Synapse SQL (pré-visualização)

Pode ligar-se ao Synapse SQL (pré-visualização) com vários protocolos de aplicação diferentes, tais como, [ADO.NET,](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx) [ODBC,](https://msdn.microsoft.com/library/jj730314.aspx) [PHP](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396)e [JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx). Abaixo estão alguns exemplos de cordas de ligações para cada protocolo. 

Também pode utilizar o portal Azure para construir a sua cadeia de ligação.  Para construir a sua cadeia de ligação utilizando o portal Azure, navegue para a sua lâmina de base de dados, em Essentials clique em *cadeias* de *ligação*de base de dados Show .

## <a name="sample-adonet-connection-string"></a>Fio de conexão ADO.NET da amostra

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Fio de ligação ODBC da amostra

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Fio de ligação PHP da amostra

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Fio de ligação JDBC da amostra

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

> [!NOTE]
> Considere ajustar o tempo de ligação para 300 segundos para permitir que a ligação sobreviva a curtos períodos de indisponibilidade.

## <a name="recommendations"></a>Recomendações

Para executar consultas a pedido da **SQL,** as ferramentas recomendadas são [o Azure Data Studio](get-started-azure-data-studio.md) e o Azure Synapse Studio.

## <a name="next-steps"></a>Passos seguintes

Para começar a consultar a sua análise com o Visual Studio e outras aplicações, consulte [A Query com O Estúdio Visual](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
