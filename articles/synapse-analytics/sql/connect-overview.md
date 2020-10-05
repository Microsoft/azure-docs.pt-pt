---
title: Ligue-se ao SQL da Sinapse
description: Adecte-se ao Sinaapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: bbd82f856213bb36e71625eabc8bce9999ccd53f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91289400"
---
# <a name="connect-to-synapse-sql"></a>Ligue-se ao SQL da Sinapse
Esteja ligado à capacidade Sinapse SQL em Azure Synapse Analytics.

## <a name="supported-tools-for-sql-on-demand-preview"></a>Ferramentas suportadas para SQL on-demand (pré-visualização)

[O Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) está totalmente suportado a partir da versão 1.18.0. O SSMS é parcialmente suportado a partir da versão 18.5, pode usá-lo apenas para ligar e consultar.

> [!NOTE]
> Se um login AAD tiver uma ligação aberta por mais de 1 hora no momento da execução da consulta, qualquer consulta que dependa do AAD falhará. Isto inclui consulta de armazenamento utilizando o transitário AAD e declarações que interagem com a AAD (como CREATE EXTERNAL PROVIDER). Isto afeta todas as ferramentas que mantêm as ligações abertas, como no editor de consultas em SSMS e ADS. As ferramentas que abrem novas ligações para executar uma consulta, como o Synapse Studio, não são afetadas.

> Pode reiniciar sSMS ou ligar e desligar em ADS para mitigar este problema. 

## <a name="find-your-server-name"></a>Encontrar o nome do servidor

O nome do servidor para piscina SQL no seguinte exemplo é: showdemoweu.sql.azuresynapse.net.
O nome do servidor para SQL a pedido no seguinte exemplo é: showdemoweu-ondemand.sql.azuresynapse.net.

Descubra o nome de servidor completamente qualificado:

1. Aceda ao [portal do Azure](https://portal.azure.com).
2. Selecione em **espaços de trabalho synapse**.
3. Selecione no espaço de trabalho a que pretende ligar.
4. Vai para uma visão geral.
5. Localize o nome do servidor completo.

## <a name="sql-pool"></a>**Conjunto de SQL**

![Nome de servidor completo](./media/connect-overview/server-connect-example.png)

## <a name="sql-on-demand"></a>**SQL a pedido**

![Nome completo do servidor SQL a pedido](./media/connect-overview/server-connect-example-sqlod.png)

## <a name="supported-drivers-and-connection-strings"></a>Controladores e cadeias de ligação suportados
O Sinaapse SQL suporta [ADO.NET,](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx) [ODBC,](https://msdn.microsoft.com/library/jj730314.aspx) [PHP](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396)e [JDBC.](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx) Para encontrar a versão e documentação mais recentes, selecione um dos controladores anteriores. Para gerar automaticamente a cadeia de ligação para o controlador que está a utilizar a partir do portal Azure, selecione **Série de cadeias** de ligação de base de dados a partir do exemplo anterior. Seguem-se também alguns exemplos do aspeto de uma cadeia de ligação para cada controlador.

> [!NOTE]
> Considere definir o tempo limite da ligação como 300 segundos, para permitir que a ligação sobreviva a curtos períodos de indisponibilidade.

### <a name="adonet-connection-string-example"></a>Exemplo de cadeia de ligação de ADO.NET

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Exemplo de cadeia de ligação de ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Exemplo de cadeia de ligação de PHP

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Exemplo de cadeia de ligação de JDBC

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Definições de ligação
O SQL de Sinapse normaliza algumas definições durante a ligação e criação de objetos. Estas definições não podem ser ultrapassadas e incluem:

| Definição de base de dados | Valor |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |ON |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |ON |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |mdy |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |7 |

## <a name="recommendations"></a>Recomendações

Para a execução de consultas **a pedido do SQL,** as ferramentas recomendadas são [o Azure Data Studio](get-started-azure-data-studio.md) e o Azure Synapse Studio.

## <a name="next-steps"></a>Passos seguintes
Para ligar e consultar com o Visual Studio, veja [Query with Visual Studio (Consulta com o Visual Studio)](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Para saber mais sobre opções de autenticação, consulte [autenticação para Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).