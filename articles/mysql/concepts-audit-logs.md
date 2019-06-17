---
title: Registos de auditoria da base de dados do Azure para MySQL
description: Descreve os registos de auditoria disponíveis no banco de dados do Azure para MySQL e os parâmetros disponíveis para ativar os níveis de registo.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 35cbe04380e2113f986d1e7adf3f7fdf89cb9326
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078875"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Registos de auditoria na base de dados do Azure para MySQL

Na base de dados do Azure para MySQL, o registo de auditoria está disponível para os utilizadores. O registo de auditoria pode ser utilizado para controlar a atividade de banco de dados e é geralmente utilizado para conformidade.

> [!IMPORTANT]
> Funcionalidade de registo de auditoria está atualmente em pré-visualização.

## <a name="configure-audit-logging"></a>Configurar o registo de auditoria

Por predefinição, o registo de auditoria está desativado. Para ativá-lo, defina `audit_log_enabled` on.

Outros parâmetros que pode ajustar incluem:

- `audit_log_events`: controla os eventos de ter sessão iniciada. Veja abaixo tabela de eventos de auditoria específicos.
- `audit_log_exclude_users`: Utilizadores MySQL a excluir de registo. Permite um máximo de quatro utilizadores. O comprimento máximo do parâmetro é de 256 carateres.

| **Evento** | **Descrição** |
|---|---|
| `CONNECTION` | -Início de connection (com êxito ou sem êxito) <br> -A reautenticação de utilizador com utilizador/palavra-passe diferente durante a sessão <br> -Terminação de ligação |
| `DML_SELECT`| Consultas SELECT |
| `DML_NONSELECT` | INSERT/DELETE/UPDATE queries |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Consultas como "DROP DATABASE" |
| `DCL` | Consultas como "Conceder permissão" |
| `ADMIN` | Consultas como "Mostrar estado" |
| `GENERAL` | Em DML_SELECT, DML_NONSELECT, DML, DDL, DCL e administração |
| `TABLE_ACCESS` | -Só estão disponíveis para o MySQL 5.7 <br> -Table ler instruções, como SELECT ou INSERT INTO... SELECIONAR <br> -Instruções de eliminação de table, tais como DELETE ou TRUNCATE TABLE <br> -Instruções de inserção de table, como INSERT ou substituição <br> -Instruções de atualização de table, como a ATUALIZAÇÃO |

## <a name="access-audit-logs"></a>Aceder aos registos de auditoria

Registos de auditoria estão integrados com os registos de diagnóstico do Azure Monitor. Assim que tive ativado os registos de auditoria no seu servidor MySQL, pode emiti-los para registos do Azure Monitor, os Hubs de eventos ou armazenamento do Azure. Para saber mais sobre como ativar os registos de diagnóstico no portal do Azure, veja a [artigo de portal de registo de auditoria](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="schemas"></a>Esquemas

As secções seguintes descrevem o que é o resultado por registos de auditoria do MySQL com base no tipo de evento. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

### <a name="connection"></a>Ligação

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | O ID de inquilino |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Carimbo de hora quando o registo foi registado em UTC |
| `Type` | Tipo do registo. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID da subscrição que o servidor pertence a |
| `ResourceGroup` | Nome do grupo de recursos do servidor pertence a |
| `ResourceProvider` | Nome do fornecedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI do recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `connection_log` |
| `event_subclass` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (apenas disponível para o MySQL 5.7) |
| `connection_id` | ID de ligação exclusivo gerado pelo MySQL |
| `host` | Vazio |
| `ip` | Endereço IP do cliente a ligar ao MySQL |
| `user` | Nome de utilizador que executa a consulta |
| `db` | Nome da base de dados ligado a |
| `\_ResourceId` | URI do recurso |

### <a name="general"></a>Geral

Esquema abaixo aplica-se aos tipos de evento de geral, DML_SELECT, DML_NONSELECT, DML, DDL, DCL e administrador.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | O ID de inquilino |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Carimbo de hora quando o registo foi registado em UTC |
| `Type` | Tipo do registo. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID da subscrição que o servidor pertence a |
| `ResourceGroup` | Nome do grupo de recursos do servidor pertence a |
| `ResourceProvider` | Nome do fornecedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI do recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `general_log` |
| `event_subclass` | `LOG`, `ERROR`, `RESULT` (apenas disponível para o MySQL 5.6) |
| `event_time` | Consulta iniciar segundos no UNIX timestamp |
| `error_code` | Código de erro se a consulta falha. `0` significa que nenhum erro |
| `thread_id` | ID do thread que executou a consulta |
| `host` | Vazio |
| `ip` | Endereço IP do cliente a ligar ao MySQL |
| `user` | Nome de utilizador que executa a consulta |
| `sql_text` | Texto da consulta completa |
| `\_ResourceId` | URI do recurso |

### <a name="table-access"></a>Acesso de tabela

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | O ID de inquilino |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Carimbo de hora quando o registo foi registado em UTC |
| `Type` | Tipo do registo. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID da subscrição que o servidor pertence a |
| `ResourceGroup` | Nome do grupo de recursos do servidor pertence a |
| `ResourceProvider` | Nome do fornecedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI do recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `table_access_log` |
| `event_subclass` | `READ`, `INSERT`, `UPDATE`, ou `DELETE` |
| `connection_id` | ID de ligação exclusivo gerado pelo MySQL |
| `db` | Nome da base de dados acedido |
| `table` | Nome da tabela acessada |
| `sql_text` | Texto da consulta completa |
| `\_ResourceId` | URI do recurso |

## <a name="next-steps"></a>Passos Seguintes

- [Como configurar registos de auditoria no portal do Azure](howto-configure-audit-logs-portal.md)