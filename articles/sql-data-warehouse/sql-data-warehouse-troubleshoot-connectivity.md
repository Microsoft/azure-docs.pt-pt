---
title: Resolução de problemas de armazém de dados SQL do Azure | Documentos da Microsoft
description: Resolução de problemas do armazém de dados SQL do Azure.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 3f25d2ab5b2b988725d8f4fdf942854746f404d1
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754841"
---
# <a name="troubleshooting-connectivity-issues"></a>Resolução de problemas de conectividade

Este artigo apresenta uma lista de técnicas de resolução de problemas comuns em torno de ligar ao seu SQL Data Warehouse.
- [Verificar a disponibilidade de serviço](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Verifique a existência de operação em pausa ou dimensionamento](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Verifique as definições de firewall](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Verifique as definições de ponto final de serviço/VNet](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Verificação para os controladores mais recentes](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Verifique a cadeia de ligação](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Problemas de ligação intermitentes](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Mensagens de erro comuns](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Verificar a disponibilidade de serviço

Verifique se o serviço está disponível. No portal do Azure, vá para o SQL data warehouse que está a tentar ligar. No painel de índice esquerdo, clique em **diagnosticar e resolver problemas**.

![Selecione o estado de funcionamento do recurso](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

O estado do seu armazém de dados SQL será apresentado aqui. Se o serviço não está visível como **disponível**, verifique ainda mais passos.

![Serviço disponível](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Se o estado de funcionamento do recurso mostra que o armazém de dados está em pausa ou dimensionar, siga as orientações para retomar o seu armazém de dados.

![O serviço em pausa](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) informações adicionais sobre o estado de funcionamento do recurso podem ser encontradas aqui.

## <a name="check-for-paused-or-scaling-operation"></a>Verifique a existência de operação em pausa ou dimensionamento

Verifique o portal para ver se o SQL data warehouse está em pausa ou dimensionamento.

![O serviço em pausa](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Se vir que o seu serviço está em pausa ou dimensionamento, certifique-se para ver não é durante a sua agenda de manutenção. No portal para o SQL data warehouse *descrição geral*, verá que a agenda de manutenção selecionados.

![Agenda de manutenção de descrição geral](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Caso contrário, contacte o administrador de TI para verificar que esta manutenção não é um evento agendado. Para retomar o armazém de dados SQL, siga os passos descritos [aqui](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Verifique as definições de firewall

O SQL Data Warehouse comunica através da porta 1433.   Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Nesse caso, não é possível ligar ao seu servidor de base de dados do Azure SQL, a menos que o departamento de TI abra a porta 1433. Pode encontrar informações adicionais sobre as configurações de firewall [aqui](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#manage-server-level-ip-firewall-rules-using-the-azure-portal).

## <a name="check-your-vnetservice-endpoint-settings"></a>Verifique as definições de ponto final de serviço/VNet

Se estiver a receber erros 40914 e 40615, consulte [descrição do erro e resolução aqui](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Verificação para os controladores mais recentes

### <a name="software"></a>Software

Verifique se que está a utilizar as ferramentas mais recentes para ligar ao seu armazém de dados SQL:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Controladores

Verifique para se certificar de que está a utilizar as versões mais recentes do driver.  Utilizar uma versão antiga dos controladores pode resultar em comportamentos inesperados, como os drivers mais antigos podem não suportar novos recursos.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Verifique a cadeia de ligação

Verifique se que as cadeias de ligação estão definidas corretamente.  Seguem-se alguns exemplos.  Pode encontrar informações adicionais em torno [cadeias de ligação aqui](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

Cadeia de ligação de ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Cadeia de ligação de ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Cadeia de ligação de PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Cadeia de ligação JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Problemas de ligação intermitentes

Verifique se estiver com muita carga no servidor com um elevado número de pedidos em fila. Terá de aumentar verticalmente o armazém de dados para obter recursos adicionais.

## <a name="common-error-messages"></a>Mensagens de erro comuns

Erros 40914 e 40615, consulte a [descrição do erro e resolução aqui](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Continua a ter problemas de conectividade?
Criar uma [pedido de suporte](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) para que a equipa de engenharia pode oferecer suporte a.
