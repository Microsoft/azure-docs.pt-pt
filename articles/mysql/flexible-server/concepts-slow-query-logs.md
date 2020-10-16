---
title: Registos de consulta lenta - Base de Dados Azure para MySQL - Servidor Flexível
description: Descreve os registos de consulta lenta disponíveis na Base de Dados Azure para o MySQL Flexible Server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 512a103f5ac116f1f4eb18631cdc8e4a8b9380c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399215"
---
# <a name="slow-query-logs-in-azure-database-for-mysql-flexible-server-preview"></a>Registos de consulta lenta na Base de Dados Azure para o MySQL Flexible Server (Pré-visualização)

> [!IMPORTANT] 
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Na Base de Dados Azure para o MySQL Flexible Server, o registo de consulta lenta está disponível para os utilizadores configurar e aceder. Os registos de consulta lenta são desativados por padrão e podem ser ativados para ajudar a identificar estrangulamentos de desempenho durante a resolução de problemas.

Para obter mais informações sobre o registo de consulta lenta MySQL, consulte a [secção de registo de consulta lenta](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) na documentação do motor MySQL.

## <a name="configure-slow-query-logging"></a>Configurar a consulta lenta 
Por predefinição, o registo de consulta lenta é desativado. Para ativar os registos, desa um parâmetro do `slow_query_log` servidor para *ON*. Isto pode ser configurado usando o portal Azure ou Azure CLI <!-- add link to server parameter-->. 

Outros parâmetros que pode ajustar para controlar o comportamento de registo de consultas lentas incluem:

- **long_query_time:** faça uma consulta se demorar mais do que `long_query_time` (em segundos) a concluir. A predefinição é de 10 segundos.
- **log_slow_admin_statements:** determina se declarações administrativas (ex. `ALTER_TABLE`, `ANALYZE_TABLE` ) estão registados.
- **log_queries_not_using_indexes:** determina se as consultas que não utilizam índices são registadas.
- **log_throttle_queries_not_using_indexes:** limita o número de consultas não indexadas que podem ser escritas para o registo de consulta lenta. Este parâmetro entra em vigor quando `log_queries_not_using_indexes` é definido para *ON*

> [!IMPORTANT]
> Se as suas tabelas não estiverem indexadas, definir os `log_queries_not_using_indexes` parâmetros e os parâmetros para ON pode afetar o `log_throttle_queries_not_using_indexes` desempenho do MySQL, uma vez que todas as consultas que correm contra estas tabelas não indexadas serão escritas para o registo de consulta lenta. **ON**

Consulte a [documentação](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) de registo de consulta lenta MySQL para obter descrições completas dos parâmetros de registo de consulta lenta.

## <a name="access-slow-query-logs"></a>Aceder a registos de consulta lenta

Os registos de consulta lenta são integrados com as definições de diagnóstico do Azure Monitor. Uma vez ativados os registos de consulta lenta no seu servidor flexível MySQL, pode emitê-los para registos do Azure Monitor, Centros de Eventos ou Armazenamento Azure. Para saber mais sobre as definições de diagnóstico, consulte a documentação dos [registos de diagnóstico](../../azure-monitor/platform/platform-logs-overview.md). Para saber mais sobre como ativar as definições de diagnóstico no portal Azure, consulte o artigo do [portal de registo de consulta lenta](how-to-configure-slow-query-logs-portal.md#set-up-diagnostics).

A tabela seguinte descreve a saída do registo de consulta lenta. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua iD do seu inquilino |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Carimbo de tempo quando o registo foi gravado na UTC |
| `Type` | Tipo de tronco. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a subscrição a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos a que o servidor pertence |
| `ResourceProvider` | Nome do fornecedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recursos |
| `Resource` | Nome do servidor |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nome do servidor |
| `start_time_t` [UTC] | Quando a consulta começou |
| `query_time_s` | Tempo total em segundos a consulta levou a executar |
| `lock_time_s` | Tempo total em segundos a consulta foi bloqueada |
| `user_host_s` | Nome de utilizador |
| `rows_sent_s` | Número de linhas enviadas |
| `rows_examined_s` | Número de linhas examinadas |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/5.7/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Inserir ID |
| `sql_text_s` | Consulta completa |
| `server_id_s` | ID do servidor |
| `thread_id_s` | ID de fio |
| `\_ResourceId` | URI de recursos |

> [!Note]
> Para `sql_text_s` , log será truncado se exceder os caracteres de 2048.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analisar registos em Registos monitores Azure

Uma vez que os seus registos de consulta lenta são canalizados para Registos do Monitor Azure através de Registos de Diagnóstico, pode efetuar uma análise mais aprofundada das suas consultas lentas. Abaixo estão algumas consultas de amostra para ajudá-lo a começar. Certifique-se de atualizar o abaixo com o nome do seu servidor.

- Consultas superiores a 10 segundos num servidor em particular

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Listar as 5 consultas mais longas de um servidor em particular

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Resumir consultas lentas por tempo mínimo, máximo, médio e padrão de desvio num servidor particular

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Graf a distribuição de consulta lenta num servidor particular

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Visualizar consultas com mais de 10 segundos em todos os servidores do MySQL com Registos de Diagnóstico ativados

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [registos de auditoria](concepts-audit-logs.md)
- Configurar registos de consulta lenta do [portal Azure](how-to-configure-slow-query-logs-portal.md)
<!-- - [How to configure slow query logs from the Azure CLI](howto-configure-server-logs-in-cli.md). -->