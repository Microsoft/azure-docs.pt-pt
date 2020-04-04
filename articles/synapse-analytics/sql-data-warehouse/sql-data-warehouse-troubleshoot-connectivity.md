---
title: Conectividade de resolução de problemas
description: Conectividade de resolução de problemas na piscina SYnapse SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 08fb0a6675d18370482abe9b1d7b9a0d9ee5c364
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632994"
---
# <a name="troubleshooting-connectivity-issues"></a>Troubleshooting connectivity issues (Resolver problemas de conectividade)

Este artigo lista técnicas comuns de resolução de problemas em torno da ligação à sua base de dados SQL Analytics.

- [Verifique a disponibilidade do serviço](sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Verificar operação de dimensionamento ou em pausa](sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Verificar as definições de firewall](sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Verificar as definições de Ponto Final de Serviço/VNet](sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Verificar se existem controladores mais recentes](sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Verificar a cadeia de ligação](sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Problemas de ligação intermitentes](sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Mensagens de erro comuns](sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Verifique a disponibilidade do serviço

Verifique se o serviço está disponível. No portal Azure, vá à piscina Synapse SQL que está a tentar ligar. No painel TOC esquerdo, clique em **Diagnosticar e resolver problemas**.

![Selecione saúde de recursos](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

O estado da sua piscina SYnapse SQL será mostrado aqui. Se o serviço não estiver **disponível,** verifique mais passos.

![Serviço Disponível](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Se a sua saúde de Recursos mostrar que a sua instância de piscina Synapse SQL está pausada ou escalonada, siga a orientação para retomar a sua instância.

![Serviço Pausado](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) Informações adicionais sobre saúde de recursos podem ser encontradas aqui.

## <a name="check-for-paused-or-scaling-operation"></a>Verificar operação de dimensionamento ou em pausa

Verifique o portal para ver se a sua instância de piscina Synapse SQL está pausada ou escalonada.

![Serviço Pausado](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Se verificar que o seu serviço está pausado ou escalonado, verifique se não é durante o seu horário de manutenção. No portal para a sua *visão*geral da piscina Synapse SQL, você verá o horário de manutenção eleito.

![Horário de manutenção de visão geral](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Caso contrário, consulte o seu administrador de TI para verificar se esta manutenção não é um evento agendado. Para retomar a instância SQL Analytics, siga [estes passos](pause-and-resume-compute-portal.md).

## <a name="check-your-firewall-settings"></a>Verificar as definições de firewall

A base de dados SQL Analytics comunica através da porta 1433.Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao servidor da Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433. Informações adicionais sobre configurações de firewall podem ser encontradas [aqui](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Verificar as definições de Ponto Final de Serviço/VNet

Se estiver a receber erros 40914 e 40615, consulte [a descrição do erro e](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)a resolução aqui .

## <a name="check-for-the-latest-drivers"></a>Verificar se existem controladores mais recentes

### <a name="software"></a>Software

Verifique se está a utilizar as mais recentes ferramentas para se ligar à sua piscina SYnapse SQL:

- SSMS
- Azure Data Studio
- Ferramentas de dados do Servidor SQL (Estúdio Visual)

### <a name="drivers"></a>Controladores

Verifique se está a utilizar as versões mais recentes do condutor.A utilização de uma versão mais antiga dos condutores pode resultar em comportamentos inesperados, uma vez que os condutores mais velhos podem não suportar novas funcionalidades.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server)
- [PHP](/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Verificar a cadeia de ligação

Verifique se as cadeias de ligação estão definidas corretamente.  Abaixo estão algumas amostras.  Pode encontrar informações adicionais sobre [cadeias de ligação aqui](/sql-data-warehouse/sql-data-warehouse-connection-strings.md).

Cadeia de ligação de ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Cadeia de ligação ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Fio php ligação

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Cadeia de ligação JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Problemas de ligação intermitentes

Verifique se o servidor está sobrecarregado com um grande número de pedidos em fila. Você pode precisar de escalar o seu pool SQL Synapse para obter recursos adicionais.

## <a name="common-error-messages"></a>Mensagens de erro comuns

Erros 40914 e 40615, consulte a [descrição e resolução](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)do erro aqui .

## <a name="still-having-connectivity-issues"></a>Ainda com problemas de conectividade?

Crie um bilhete de [apoio](/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md) para que a equipa de engenharia possa apoiá-lo.
