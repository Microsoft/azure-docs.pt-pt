---
title: Registos de auditoria - Base de Dados Azure para MariaDB
description: Descreve os registos de auditoria disponíveis na Base de Dados Azure para o MariaDB e os parâmetros disponíveis para permitir os níveis de exploração madeireira.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/19/2020
ms.openlocfilehash: e8d5abd81feb86ba48fc442ee95615cb52230a24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063817"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Registos de Auditoria na Base de Dados Azure para MariaDB

Na Base de Dados Azure para MariaDB, o registo de auditoria está disponível para os utilizadores. O registo de auditoria pode ser utilizado para rastrear a atividade ao nível da base de dados e é comumente utilizado para o cumprimento.

> [!IMPORTANT]
> A funcionalidade de registo de auditoria encontra-se atualmente em pré-visualização.

## <a name="configure-audit-logging"></a>Configure a exploração madeireira de auditoria

Por defeito, o registo de auditoria está desativado. Para o ativar, coloque-o `audit_log_enabled` em ON.

Outros parâmetros que pode ajustar incluem:

- `audit_log_events`: controla os eventos a iniciar. Consulte abaixo a tabela para eventos específicos de auditoria.
- `audit_log_include_users`: Os utilizadores mariaDB a incluir para exploração madeireira. O valor padrão para este parâmetro está vazio, o que incluirá todos os utilizadores para a exploração madeireira. Isto tem uma `audit_log_exclude_users`prioridade maior. O comprimento máximo do parâmetro é de 512 caracteres.
> [!Note]
> `audit_log_include_users`tem maior `audit_log_exclude_users`prioridade sobre . Por exemplo, `audit_log_include_users`  =  `demouser` `audit_log_exclude_users`  =  `demouser`se e , o utilizador será incluído `audit_log_include_users` nos registos de auditoria porque tem maior prioridade.
- `audit_log_exclude_users`: Os utilizadores mariaDB a serem excluídos da exploração madeireira. Permite, no máximo, quatro utilizadores. O comprimento máximo do parâmetro é de 256 caracteres.

| **Evento** | **Descrição** |
|---|---|
| `CONNECTION` | - Iniciação de ligação (bem sucedida ou mal sucedida) <br> - Reautenticação do utilizador com diferentes utilizadores/palavra-passe durante a sessão <br> - Rescisão de ligação |
| `DML_SELECT`| Consultas selecionadas |
| `DML_NONSELECT` | INSERIR/ELIMINAR/ATUALIZAR consultas |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Consultas como "DROP DATABASE" |
| `DCL` | Consultas como "GRANT PERMISSION" |
| `ADMIN` | Consultas como "SHOW STATUS" |
| `GENERAL` | Tudo em DML_SELECT, DML_NONSELECT, DML, DDL, DCL e ADMIN |

## <a name="access-audit-logs"></a>Aceder aos registos de auditoria

Os registos de auditoria estão integrados com registos de diagnóstico do Monitor Azure. Depois de ativar registos de auditoria no seu servidor MariaDB, pode emiti-los para registos do Monitor Azure, Hubs de Eventos ou Armazenamento Azure. Para saber mais sobre como ativar registos de diagnóstico no portal Azure, consulte o artigo do portal de registo de [auditoria](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schemas de Registos de Diagnóstico

As seguintes secções descrevem o que é output pelos registos de auditoria da MariaDB com base no tipo de evento. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

### <a name="connection"></a>Ligação

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua identificação do inquilino |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de tempo quando o registo foi gravado na UTC |
| `Type` | Tipo de tronco. Sempre`AzureDiagnostics` |
| `SubscriptionId` | GUID para a subscrição a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos a que o servidor pertence |
| `ResourceProvider` | Nome do fornecedor de recursos. Sempre`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Recurso URI |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | ID de ligação única gerado pela MariaDB |
| `host_s` | Vazio |
| `ip_s` | Endereço IP do cliente que liga ao MariaDB |
| `user_s` | Nome do utilizador que executa a consulta |
| `db_s` | Nome da base de dados ligado |
| `\_ResourceId` | Recurso URI |

### <a name="general"></a>Geral

O schema abaixo aplica-se aos tipos de eventos GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL e ADMIN.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua identificação do inquilino |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de tempo quando o registo foi gravado na UTC |
| `Type` | Tipo de tronco. Sempre`AzureDiagnostics` |
| `SubscriptionId` | GUID para a subscrição a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos a que o servidor pertence |
| `ResourceProvider` | Nome do fornecedor de recursos. Sempre`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Recurso URI |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Consulta inicia segundos no carimbo de tempo UNIX |
| `error_code_d` | Código de erro se a consulta falhar. `0`significa que nenhum erro |
| `thread_id_d` | ID de fio que executou a consulta |
| `host_s` | Vazio |
| `ip_s` | Endereço IP do cliente que liga ao MariaDB |
| `user_s` | Nome do utilizador que executa a consulta |
| `sql_text_s` | Texto de consulta completa |
| `\_ResourceId` | Recurso URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analisar registos em registos do Monitor Azure

Uma vez que os registos de auditoria sejam canalizados para registos do Monitor Azure através de Registos de Diagnóstico, pode efetuar uma análise mais aprofundada dos seus eventos auditados. Abaixo estão algumas consultas de amostra para ajudá-lo a começar. Certifique-se de atualizar o abaixo com o nome do servidor.

- Lista ruma eventos GERAIs num determinado servidor

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Lista de eventos de LIGAÇÃO num determinado servidor

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Resumir eventos auditados num determinado servidor

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- Grafe a distribuição do tipo de evento de auditoria num determinado servidor

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Lista de eventos auditados em todos os servidores da MariaDB com Registos de Diagnóstico habilitados para registos de auditoria

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Passos seguintes

- [Como configurar registos de auditoria no portal Azure](howto-configure-audit-logs-portal.md)