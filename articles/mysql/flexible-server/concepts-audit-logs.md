---
title: Registos de auditoria - Base de Dados Azure para MySQL - Servidor Flexível
description: Descreve os registos de auditoria disponíveis na Base de Dados Azure para o MySQL Flexible Server.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 5aab78ad99b80ff1d7be92bd36847b01dbc0e33b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542223"
---
# <a name="track-database-activity-with-audit-logs-in-azure-database-for-mysql-flexible-server"></a>Acompanhe a atividade da base de dados com registos de auditoria na base de dados Azure para o MySQL Flexible Server

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server está atualmente em pré-visualização pública

A Azure Database for MySQL Flexible Server fornece aos utilizadores a capacidade de configurar registos de auditoria. Os registos de auditoria podem ser usados para rastrear a atividade de nível de base de dados, incluindo eventos de conexão, administração, DDL e DML. Estes tipos de registos são frequentemente utilizados para fins de conformidade.

## <a name="configure-audit-logging"></a>Configurar a exploração de auditoria

>[!IMPORTANT]
> Recomenda-se apenas registar os tipos de eventos e utilizadores necessários para os seus fins de auditoria para garantir que o desempenho do seu servidor não seja fortemente impactado.

Por predefinição, os registos de auditoria são desativados. Para os ativar, desate o parâmetro do `audit_log_enabled` servidor para *ON*. Isto pode ser configurado usando o portal Azure ou Azure CLI <!-- add link to server parameter-->. 

Outros parâmetros que pode ajustar para controlar o comportamento de registo de auditoria incluem:

- `audit_log_events`: controla os eventos a registar. Consulte a tabela abaixo para eventos de auditoria específicos.
- `audit_log_include_users`: Os utilizadores do MySQL devem ser incluídos para a exploração madeireira. O valor predefinido para este parâmetro está vazio, o que incluirá todos os utilizadores para registar. Isto tem maior prioridade sobre `audit_log_exclude_users` . O comprimento máximo do parâmetro é de 512 caracteres.
- `audit_log_exclude_users`: Os utilizadores do MySQL devem ser excluídos da exploração madeireira. O comprimento máximo do parâmetro é de 512 caracteres.

> [!NOTE]
> `audit_log_include_users` tem maior prioridade sobre `audit_log_exclude_users` . Por exemplo, se `audit_log_include_users`  =  `demouser` e , o `audit_log_exclude_users`  =  `demouser` utilizador será incluído nos registos de auditoria porque `audit_log_include_users` tem maior prioridade.

| **Evento** | **Descrição** |
|---|---|
| `CONNECTION` | - Iniciação de ligação (bem sucedida ou mal sucedida) <br> - Reautensão do utilizador com diferentes palavras-passe de utilizador/senha durante a sessão <br> - Rescisão de ligação |
| `DML_SELECT`| SELECIONE consultas |
| `DML_NONSELECT` | INSIEM/APAGAR/ATUALIZAR CONSULTAS |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Consultas como "DROP DATABASE" |
| `DCL` | Consultas como "GRANT PERMISSION" |
| `ADMIN` | Consultas como "SHOW STATUS" |
| `GENERAL` | Tudo em DML_SELECT, DML_NONSELECT, DML, DDL, DCL e ADMIN |
| `TABLE_ACCESS` | - Disponível apenas para MySQL 5.7 <br> - Declarações de leitura de tabelas, tais como SELECT ou INSERT INTO... SELECIONE <br> - Tabela eliminar declarações, tais como DELETE ou TABELA TRUNCATE <br> - Declarações de inserção de tabelas, tais como INSERT ou REPLACE <br> - Declarações de atualização de tabelas, tais como UPDATE |

## <a name="access-audit-logs"></a>Aceder aos registos de auditoria

Os registos de auditoria são integrados com as definições de diagnóstico do Azure Monitor. Uma vez que tenha ativado os registos de auditoria no seu servidor flexível MySQL, pode emitê-los para registos do Azure Monitor, Centros de Eventos ou Armazenamento Azure. Para saber mais sobre as definições de diagnóstico, consulte a documentação dos [registos de diagnóstico](../../azure-monitor/platform/platform-logs-overview.md). Para saber mais sobre como ativar as definições de diagnóstico no portal Azure, consulte o artigo do [portal de registo de auditoria](how-to-configure-audit-logs-portal.md#set-up-diagnostics).

As secções seguintes descrevem a saída dos registos de auditoria do MySQL com base no tipo de evento. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

### <a name="connection"></a>Ligação

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua iD do seu inquilino |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de tempo quando o registo foi gravado na UTC |
| `Type` | Tipo de tronco. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a subscrição a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos a que o servidor pertence |
| `ResourceProvider` | Nome do fornecedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recursos |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` `CHANGE USER` (apenas disponível para MySQL 5.7) |
| `connection_id_d` | ID de ligação única gerado pelo MySQL |
| `host_s` | Vazio |
| `ip_s` | Endereço IP do cliente que liga ao MySQL |
| `user_s` | Nome do utilizador que executa a consulta |
| `db_s` | Nome da base de dados ligada a |
| `\_ResourceId` | URI de recursos |

### <a name="general"></a>Geral

O esquema abaixo aplica-se aos tipos de eventos GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL e ADMIN.

> [!NOTE]
> Para `sql_text_s` , log será truncado se exceder os caracteres de 2048.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua iD do seu inquilino |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de tempo quando o registo foi gravado na UTC |
| `Type` | Tipo de tronco. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a subscrição a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos a que o servidor pertence |
| `ResourceProvider` | Nome do fornecedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recursos |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR` `RESULT` (apenas disponível para MySQL 5.6) |
| `event_time` | Hora de início de consulta no calendário utc |
| `error_code_d` | Código de erro se a consulta falhar. `0` significa que não há erro |
| `thread_id_d` | ID de fio que executou a consulta |
| `host_s` | Vazio |
| `ip_s` | Endereço IP do cliente que liga ao MySQL |
| `user_s` | Nome do utilizador que executa a consulta |
| `sql_text_s` | Texto de consulta completa |
| `\_ResourceId` | URI de recursos |

### <a name="table-access"></a>Acesso à mesa

> [!NOTE]
> Os registos de acesso à mesa são apenas de saída para o MySQL 5.7.<br>Para `sql_text_s` , log será truncado se exceder os caracteres de 2048.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua iD do seu inquilino |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de tempo quando o registo foi gravado na UTC |
| `Type` | Tipo de tronco. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a subscrição a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos a que o servidor pertence |
| `ResourceProvider` | Nome do fornecedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recursos |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT` `UPDATE` ou `DELETE` |
| `connection_id_d` | ID de ligação única gerado pelo MySQL |
| `db_s` | Nome da base de dados acedida |
| `table_s` | Nome da mesa acedida |
| `sql_text_s` | Texto de consulta completa |
| `\_ResourceId` | URI de recursos |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analisar registos em Registos monitores Azure

Assim que os seus registos de auditoria forem canalizados para Registos do Monitor Azure através de Registos de Diagnóstico, poderá efetuar uma análise mais aprofundada dos seus eventos auditados. Abaixo estão algumas consultas de amostra para ajudá-lo a começar. Certifique-se de atualizar o abaixo com o nome do seu servidor.

- Listar eventos GERAIs num servidor específico

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Liste eventos DE LIGAÇÃO num servidor específico

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Resumir eventos auditados num servidor em particular

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- Gráfico da distribuição do tipo de evento de auditoria em um servidor particular

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Liste eventos auditados em todos os servidores do MySQL com Registos de Diagnóstico habilitados para registos de auditoria

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [registos de consulta lenta](concepts-slow-query-logs.md)
- Configurar registos de consulta de auditoria do [portal Azure](how-to-configure-audit-logs-portal.md)
<!-- - [How to configure audit logs in the Azure portal](howto-configure-audit-logs-portal.md)-->