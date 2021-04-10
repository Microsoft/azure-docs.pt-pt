---
title: Registos de auditoria - Base de Dados Azure para MariaDB
description: Descreve os registos de auditoria disponíveis na Base de Dados Azure para MariaDB e os parâmetros disponíveis para permitir níveis de registo.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/24/2020
ms.openlocfilehash: e66eaf62b54759222421b17ebc7e33b82e018338
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663762"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Registos de auditoria na Base de Dados Azure para MariaDB

Na Base de Dados Azure para MariaDB, o registo de auditoria está disponível para os utilizadores. O registo de auditoria pode ser usado para rastrear a atividade de nível de base de dados e é comumente utilizado para o cumprimento.

## <a name="configure-audit-logging"></a>Configurar a exploração de auditoria

>[!IMPORTANT]
> Recomenda-se apenas registar os tipos de eventos e utilizadores necessários para os seus fins de auditoria para garantir que o desempenho do seu servidor não seja fortemente impactado.

Por predefinição, o registo de auditoria é desativado. Para a capacitá-lo, desatado `audit_log_enabled` para ON.

Outros parâmetros que pode ajustar incluem:

- `audit_log_events`: controla os eventos a registar. Consulte a tabela abaixo para eventos de auditoria específicos.
- `audit_log_include_users`: Utilizadores MariaDB a incluir para a exploração madeireira. O valor predefinido para este parâmetro está vazio, o que incluirá todos os utilizadores para registar. Isto tem maior prioridade sobre `audit_log_exclude_users` . O comprimento máximo do parâmetro é de 512 caracteres.
- `audit_log_exclude_users`: Utilizadores MariaDB excluídos da exploração madeireira. Permite no máximo quatro utilizadores. O comprimento máximo do parâmetro é de 256 caracteres.

> [!Note]
> `audit_log_include_users` tem maior prioridade sobre `audit_log_exclude_users` . Por exemplo, se `audit_log_include_users`  =  `demouser` e , o `audit_log_exclude_users`  =  `demouser` utilizador será incluído nos registos de auditoria porque `audit_log_include_users` tem maior prioridade.

| **Evento** | **Descrição** |
|---|---|
| `CONNECTION` | - Iniciação de ligação (bem sucedida ou mal sucedida) <br> - Reautorse do utilizador com diferentes palavras de utilizador/senha durante a sessão <br> - Rescisão de ligação |
| `DML_SELECT`| SELECIONE consultas |
| `DML_NONSELECT` | INSIEM/APAGAR/ATUALIZAR CONSULTAS |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Consultas como "DROP DATABASE" |
| `DCL` | Consultas como "GRANT PERMISSION" |
| `ADMIN` | Consultas como "SHOW STATUS" |
| `GENERAL` | Tudo em DML_SELECT, DML_NONSELECT, DML, DDL, DCL e ADMIN |

## <a name="access-audit-logs"></a>Aceder aos registos de auditoria

Os registos de auditoria estão integrados nos Registos de Diagnósticos do Azure Monitor. Depois de ter ativado os registos de auditoria no servidor MariaDB, pode emiti-los para os registos do Azure Monitor, os Hubs de Eventos ou o Armazenamento Azure. Para saber mais sobre como ativar registos de diagnóstico no portal Azure, consulte o artigo do [portal de registo de auditoria](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Esquemas de Registos de Diagnóstico

As secções seguintes descrevem o que éprodução por registos de auditoria MariaDB com base no tipo de evento. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

### <a name="connection"></a>Ligação

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua iD do seu inquilino |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de tempo quando o registo foi gravado na UTC |
| `Type` | Tipo de tronco. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a subscrição a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos a que o servidor pertence |
| `ResourceProvider` | Nome do fornecedor de recursos. Sempre `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recursos |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | ID de ligação única gerado por MariaDB |
| `host_s` | Vazio |
| `ip_s` | Endereço IP do cliente que liga a MariaDB |
| `user_s` | Nome do utilizador que executa a consulta |
| `db_s` | Nome da base de dados ligada a |
| `\_ResourceId` | URI de recursos |

### <a name="general"></a>Geral

O esquema abaixo aplica-se aos tipos de eventos GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL e ADMIN.

> [!NOTE]
> Para `sql_text` , log será truncado se exceder os caracteres de 2048.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua iD do seu inquilino |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de tempo quando o registo foi gravado na UTC |
| `Type` | Tipo de tronco. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a subscrição a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos a que o servidor pertence |
| `ResourceProvider` | Nome do fornecedor de recursos. Sempre `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recursos |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Consulta iniciar segundos em semda do tempo da UNIX |
| `error_code_d` | Código de erro se a consulta falhar. `0` significa que não há erro |
| `thread_id_d` | ID de fio que executou a consulta |
| `host_s` | Vazio |
| `ip_s` | Endereço IP do cliente que liga a MariaDB |
| `user_s` | Nome do utilizador que executa a consulta |
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

- Liste eventos auditados em todos os servidores MariaDB com Registos de Diagnóstico habilitados para registos de auditoria

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Passos seguintes

- [Como configurar registos de auditoria no portal Azure](howto-configure-audit-logs-portal.md)