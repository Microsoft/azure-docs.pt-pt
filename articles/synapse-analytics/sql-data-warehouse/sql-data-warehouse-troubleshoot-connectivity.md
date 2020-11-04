---
title: Conectividade de resolução de problemas
description: Conectividade de resolução de problemas em piscina SQL dedicada.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse, devx-track-csharp
ms.openlocfilehash: 82b9f988ef4a7f4a53cd0b451da28642b53bcb65
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308373"
---
# <a name="troubleshooting-connectivity-issues-in-dedicated-sql-pool"></a>Problemas de conectividade na piscina de SQL dedicada

Este artigo lista técnicas comuns de resolução de problemas em torno da ligação à sua base de dados dedicada à piscina SQL.

## <a name="check-service-availability"></a>Ver disponibilidade de serviço

Verifique se o serviço está disponível. No portal Azure, vá à piscina SQL dedicada que está a tentar ligar. No painel TOC esquerdo, clique no **Diagnóstico e resolva problemas**.

![Selecione a saúde dos recursos](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

O estado da sua piscina SQL dedicada será mostrado aqui. Se o serviço não estiver **disponível,** verifique mais passos.

![Serviço Disponível](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Se a sua saúde de recursos mostrar que a sua instância dedicada da piscina SQL está em pausa ou em escala, siga as orientações para retomar a sua instância.

![A screenshot mostra uma instância do armazém de dados SQL que é pausado ou escalonado.](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png)
Informações adicionais sobre a Saúde dos Recursos podem ser encontradas aqui.

## <a name="check-for-paused-or-scaling-operation"></a>Verificar operação de dimensionamento ou em pausa

Verifique o portal para ver se a sua caixa de piscina SQL dedicada está em pausa ou em escalonamento.

![A screenshot mostra como verificar se um armazém de dados é pausado.](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Se vir que o seu serviço está em pausa ou em escalonamento, verifique se não é durante o seu horário de manutenção. No portal para a sua *visão geral* dedicada da piscina SQL, você verá o horário de manutenção eleito.

![Horário de manutenção da visão geral](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Caso contrário, consulte o seu administrador de TI para verificar se esta manutenção não é um evento agendado. Para retomar a instância dedicada da piscina SQL, siga [estes passos.](pause-and-resume-compute-portal.md)

## <a name="check-your-firewall-settings"></a>Verificar as definições de firewall

A base de dados dedicada da piscina SQL comunica sobre a porta 1433.Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Nesse caso, não pode ligar-se ao seu [servidor lógico](../../azure-sql/database/logical-servers.md) a menos que o seu departamento de TI abra a porta 1433. Informações adicionais sobre configurações de firewall podem ser encontradas [aqui.](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules)

## <a name="check-your-vnetservice-endpoint-settings"></a>Verificar as definições de Ponto Final de Serviço/VNet

Se estiver a receber os Erros 40914 e 40615, consulte [a descrição do erro e a resolução aqui.](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)

## <a name="check-for-the-latest-drivers"></a>Verificar se existem controladores mais recentes

### <a name="software"></a>Software

Verifique se está a usar as ferramentas mais recentes para ligar à sua piscina SQL dedicada:

- SSMS
- Azure Data Studio
- Ferramentas de dados do servidor SQL (Estúdio Visual)

### <a name="drivers"></a>Controladores

Verifique se está a utilizar as versões mais recentes do controlador.A utilização de uma versão mais antiga dos condutores pode resultar em comportamentos inesperados, uma vez que os condutores mais velhos podem não suportar novas funcionalidades.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>Verificar a cadeia de ligação

Verifique se as cadeias de ligação estão definidas corretamente.  Abaixo estão algumas amostras.  Pode encontrar informações adicionais sobre [cadeias de ligação aqui](sql-data-warehouse-connection-strings.md).

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

## <a name="intermittent-connection-issues"></a>Problemas de ligação intermitentes

Verifique se o servidor está sobrecarregado com um grande número de pedidos em fila. Poderá ter de aumentar a sua piscina DE SQL dedicada para obter recursos adicionais.

## <a name="common-error-messages"></a>Mensagens de erro comuns

Erros 40914 e 40615, consulte aqui a [descrição e a resolução de erros.](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)

## <a name="still-having-connectivity-issues"></a>Ainda tem problemas de conectividade?

Crie um [bilhete de apoio](sql-data-warehouse-get-started-create-support-ticket.md) para que a equipa de engenharia possa apoiá-lo.
