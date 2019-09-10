---
title: Solução de problemas do Azure SQL Data Warehouse | Microsoft Docs
description: Solução de problemas do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: ebdeaf21253e89a9a14e3a56ca7be0f6e8adceb0
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859228"
---
# <a name="troubleshooting-connectivity-issues"></a>Solucionando problemas de conectividade

Este artigo lista técnicas comuns de solução de problemas em relação à conexão ao seu SQL Data Warehouse.
- [Verificar a disponibilidade do serviço](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Verificar a operação em pausa ou em escala](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Verifique as configurações do firewall](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Verifique as configurações de ponto de extremidade de serviço/VNet](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Verificar os drivers mais recentes](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Verifique a cadeia de conexão](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Problemas de conexão intermitente](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Mensagens de erro comuns](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Verificar a disponibilidade do serviço

Verifique se o serviço está disponível. Na portal do Azure, vá para o SQL Data Warehouse que você está tentando conectar. No painel de Sumário à esquerda, clique em **diagnosticar e resolver problemas**.

![Selecionar Resource Health](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

O status do seu SQL Data Warehouse será mostrado aqui. Se o serviço não estiver sendo exibido como **disponível**, verifique outras etapas.

![Serviço disponível](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Se o seu Resource Health mostrar que o data warehouse está em pausa ou em escala, siga as orientações para retomar o data warehouse.

![O serviço está](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) em pausa informações adicionais sobre Resource Health podem ser encontradas aqui.

## <a name="check-for-paused-or-scaling-operation"></a>Verificar a operação em pausa ou em escala

Verifique o portal para ver se o SQL Data Warehouse está em pausa ou em escala.

![Serviço em pausa](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Se você vir que o serviço está em pausa ou em escala, verifique se ele não está durante o agendamento de manutenção. No portal para sua *visão geral*do SQL data warehouse, você verá o agendamento de manutenção escolhido.

![Agendamento de manutenção de visão geral](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Caso contrário, verifique com seu administrador de ti para verificar se essa manutenção não é um evento agendado. Para retomar o SQL Data Warehouse, siga as etapas descritas [aqui](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Verifique as configurações do firewall

O SQL Data Warehouse comunica através da porta 1433.   Se você estiver tentando se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 1433 pode não ser permitido pelo firewall da sua rede. Nesse caso, você não pode se conectar ao servidor do banco de dados SQL do Azure, a menos que o departamento de ti Abra a porta 1433. Informações adicionais sobre as configurações de firewall podem ser encontradas [aqui](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Verifique as configurações de ponto de extremidade de serviço/VNet

Se você estiver recebendo os erros 40914 e 40615, consulte [Descrição e resolução do erro aqui](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Verificar os drivers mais recentes

### <a name="software"></a>Software

Verifique se você está usando as ferramentas mais recentes para se conectar ao seu SQL Data Warehouse:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Controladores

Verifique se você está usando as versões de driver mais recentes.  O uso de uma versão mais antiga dos drivers pode resultar em comportamentos inesperados, pois os drivers mais antigos podem não dar suporte a novos recursos.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Verifique a cadeia de conexão

Verifique se as cadeias de conexão estão definidas corretamente.  Veja abaixo alguns exemplos.  Você pode encontrar informações adicionais sobre [cadeias de conexão aqui](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

Cadeia de ligação de ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Cadeia de conexão ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Cadeia de conexão PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Cadeia de ligação JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Problemas de conexão intermitente

Verifique se você está com uma carga pesada no servidor com um grande número de solicitações enfileiradas. Talvez seja necessário escalar verticalmente seus data warehouse para obter recursos adicionais.

## <a name="common-error-messages"></a>Mensagens de erro comuns

Erros 40914 e 40615, consulte a [Descrição e a resolução do erro aqui](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Ainda tem problemas de conectividade?
Crie um [tíquete de suporte](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) para que a equipe de engenharia possa dar suporte a você.
