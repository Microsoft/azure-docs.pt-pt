---
title: Registos de auditoria da base de dados do Azure para MariaDB
description: Descreve os registos de auditoria disponíveis no banco de dados do Azure para MariaDB e os parâmetros disponíveis para ativar os níveis de registo.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 13ea60c62283db35ce4bf9fde6c3b36ba7f88013
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439205"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Registos de auditoria na base de dados do Azure para MariaDB

Na base de dados do Azure para MariaDB, o registo de auditoria está disponível para os utilizadores. O registo de auditoria pode ser utilizado para controlar a atividade de banco de dados e é geralmente utilizado para conformidade.

> [!IMPORTANT]
> Funcionalidade de registo de auditoria está atualmente em pré-visualização.

## <a name="configure-audit-logging"></a>Configurar o registo de auditoria

Por predefinição, o registo de auditoria está desativado. Para ativá-lo, defina `audit_log_enabled` on.

Outros parâmetros que pode ajustar incluem:

- `audit_log_events`: controla os eventos de ter sessão iniciada. Veja abaixo tabela de eventos de auditoria específicos.
- `audit_log_exclude_users`: MariaDB utilizadores a excluir de registo. Permite um máximo de quatro utilizadores. O comprimento máximo do parâmetro é de 256 carateres.

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

## <a name="access-audit-logs"></a>Aceder aos registos de auditoria

Registos de auditoria estão integrados com os registos de diagnóstico do Azure Monitor. Assim que tiver ativado os registos de auditoria no seu servidor MariaDB, pode emiti-los para registos do Azure Monitor, os Hubs de eventos ou armazenamento do Azure. Para saber mais sobre como ativar os registos de diagnóstico no portal do Azure, veja a [artigo de portal de registo de auditoria](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Esquemas de registos de diagnóstico

As secções seguintes descrevem o que é o resultado por registos de auditoria da MariaDB com base no tipo de evento. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

### <a name="connection"></a>Ligação

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | O ID de inquilino |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de hora quando o registo foi registado em UTC |
| `Type` | Tipo do registo. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID da subscrição que o servidor pertence a |
| `ResourceGroup` | Nome do grupo de recursos do servidor pertence a |
| `ResourceProvider` | Nome do fornecedor de recursos. Sempre `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI do recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | ID de ligação exclusivo gerado pelo MariaDB |
| `host_s` | Vazio |
| `ip_s` | Endereço IP do cliente a ligar ao MariaDB |
| `user_s` | Nome de utilizador que executa a consulta |
| `db_s` | Nome da base de dados ligado a |
| `\_ResourceId` | URI do recurso |

### <a name="general"></a>Geral

Esquema abaixo aplica-se aos tipos de evento de geral, DML_SELECT, DML_NONSELECT, DML, DDL, DCL e administrador.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | O ID de inquilino |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de hora quando o registo foi registado em UTC |
| `Type` | Tipo do registo. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID da subscrição que o servidor pertence a |
| `ResourceGroup` | Nome do grupo de recursos do servidor pertence a |
| `ResourceProvider` | Nome do fornecedor de recursos. Sempre `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI do recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Consulta iniciar segundos no UNIX timestamp |
| `error_code_d` | Código de erro se a consulta falha. `0` significa que nenhum erro |
| `thread_id_d` | ID do thread que executou a consulta |
| `host_s` | Vazio |
| `ip_s` | Endereço IP do cliente a ligar ao MariaDB |
| `user_s` | Nome de utilizador que executa a consulta |
| `sql_text_s` | Texto da consulta completa |
| `\_ResourceId` | URI do recurso |

### <a name="table-access"></a>Acesso de tabela

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | O ID de inquilino |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de hora quando o registo foi registado em UTC |
| `Type` | Tipo do registo. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID da subscrição que o servidor pertence a |
| `ResourceGroup` | Nome do grupo de recursos do servidor pertence a |
| `ResourceProvider` | Nome do fornecedor de recursos. Sempre `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI do recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE`, ou `DELETE` |
| `connection_id_d` | ID de ligação exclusivo gerado pelo MariaDB |
| `db_s` | Nome da base de dados acedido |
| `table_s` | Nome da tabela acessada |
| `sql_text_s` | Texto da consulta completa |
| `\_ResourceId` | URI do recurso |

## <a name="next-steps"></a>Passos Seguintes

- [Como configurar registos de auditoria no portal do Azure](howto-configure-audit-logs-portal.md)