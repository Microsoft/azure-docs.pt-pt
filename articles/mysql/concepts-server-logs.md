---
title: Logs de consulta lentos-banco de dados do Azure para MySQL
description: Descreve os logs de consulta lento disponíveis no banco de dados do Azure para MySQL e os parâmetros disponíveis para habilitar diferentes níveis de log.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 9a3a58cab2d9673a4660967e3a11d7f88900e718
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844960"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Logs de consulta lentos no banco de dados do Azure para MySQL
No banco de dados do Azure para MySQL, o log de consultas lentas está disponível para os usuários. Não há suporte para o acesso ao log de transações. O log de consultas lentas pode ser usado para identificar gargalos de desempenho para solução de problemas.

Para obter mais informações sobre o log de consultas lentas do MySQL, consulte a [seção log de consulta lenta](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)do manual de referência do MySQL.

## <a name="access-slow-query-logs"></a>Acessar logs de consulta lentos
Você pode listar e baixar logs de consulta lenta do banco de dados do Azure para MySQL usando o portal do Azure e o CLI do Azure.

No portal do Azure, selecione a base de dados do Azure para o servidor MySQL. No cabeçalho **monitoramento** , selecione a página **logs do servidor** .

Para obter mais informações sobre CLI do Azure, consulte [configurar e acessar logs de consulta lentos usando CLI do Azure](howto-configure-server-logs-in-cli.md).

Da mesma forma, você pode canalizar os logs para Azure Monitor usando os logs de diagnóstico. Consulte [abaixo](concepts-server-logs.md#diagnostic-logs) para obter mais informações.

## <a name="log-retention"></a>Retenção de log
Os logs estão disponíveis por até sete dias a partir da criação. Se o tamanho total dos logs disponíveis exceder 7 GB, os arquivos mais antigos serão excluídos até que haja espaço disponível. 

Os logs são girados a cada 24 horas ou 7 GB, o que ocorrer primeiro.

## <a name="configure-slow-query-logging"></a>Configurar o log de consultas lentas 
Por padrão, o log de consultas lentas está desabilitado. Para o ativar, coloque slow_query_log para ON.

Outros parâmetros que você pode ajustar incluem:

- **long_query_time:** se uma consulta demorar mais do que long_query_time (em segundos) que a consulta é registada. O padrão é de 10 segundos.
- **log_slow_admin_statements:** se a ON incluir declarações administrativas como ALTER_TABLE e ANALYZE_TABLE nas declarações escritas à slow_query_log.
- **log_queries_not_using_indexes:** determina se as consultas que não utilizam índices estão registadas na slow_query_log
- **log_throttle_queries_not_using_indexes**: Este parâmetro limita o número de consultas não indexadas que podem ser escritas no registo de consulta lenta. Este parâmetro entra em vigor quando log_queries_not_using_indexes está definido para ON.
- **log_output:** se "File", permitir que o registo de consulta lenta seja escrito tanto no armazenamento do servidor local como nos registos de diagnóstico do Monitor Azure. Se "None", o log de consultas lentas só será gravado em Azure Monitor logs de diagnóstico. 

> [!IMPORTANT]
> Se as suas tabelas não estiverem indexadas, a definição dos parâmetros `log_queries_not_using_indexes` e `log_throttle_queries_not_using_indexes` para ON pode afetar o desempenho do MySQL, uma vez que todas as consultas que correm contra estas tabelas não indexadas serão escritas para o registo de consulta lenta.<br><br>
> Se planeia registar consultas lentas durante um longo período de tempo, recomenda-se definir `log_output` para "Nenhuma". Se definido como "File", esses logs são gravados no armazenamento do servidor local e podem afetar o desempenho do MySQL. 

Consulte a [documentação do log de consultas lentas](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) do MySQL para obter descrições completas dos parâmetros de log de consulta lenta.

## <a name="diagnostic-logs"></a>Registos de diagnósticos
O banco de dados do Azure para MySQL é integrado com Azure Monitor logs de diagnóstico. Depois de habilitar os logs de consulta lentos em seu servidor MySQL, você pode optar por que eles sejam emitidos para Azure Monitor logs, hubs de eventos ou armazenamento do Azure. Para saber mais sobre como habilitar os logs de diagnóstico, consulte a seção como da [documentação dos logs de diagnóstico](../azure-monitor/platform/platform-logs-overview.md).

A tabela a seguir descreve o que está em cada log. Dependendo do método de saída, os campos incluídos e a ordem na qual eles aparecem podem variar.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Carimbo de data/hora quando o log foi gravado em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID da assinatura à qual o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nome do servidor |
| `start_time_t` [UTC] | Hora em que a consulta começou |
| `query_time_s` | Tempo total em segundos que a consulta levou para ser executada |
| `lock_time_s` | Tempo total em segundos em que a consulta foi bloqueada |
| `user_host_s` | Nome de utilizador |
| `rows_sent_s` | Número de linhas enviadas |
| `rows_examined_s` | Número de linhas examinadas |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Inserir ID |
| `sql_text_s` | Consulta completa |
| `server_id_s` | A ID do servidor |
| `thread_id_s` | ID da linha |
| `\_ResourceId` | URI de recurso |

> [!Note]
> Para `sql_text`, o registo será truncado se exceder 2048 caracteres.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analisar logs em logs de Azure Monitor

Depois que os logs de consulta lentos são canalizados para Azure Monitor logs por meio de logs de diagnóstico, você pode executar uma análise adicional de suas consultas lentas. Abaixo estão algumas consultas de exemplo para ajudá-lo a começar. Certifique-se de atualizar o abaixo com o nome do servidor.

- Consultas com mais de 10 segundos em um servidor específico

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Listar as 5 principais consultas mais longas em um servidor específico

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Resumir consultas lentas pelo mínimo, máximo, média e tempo de consulta de desvio padrão em um servidor específico

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Grafar a distribuição de consulta lenta em um servidor específico

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Exibir consultas com mais de 10 segundos em todos os servidores MySQL com logs de diagnóstico habilitados

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Próximos Passos
- [Como configurar logs de consulta lentos no portal do Azure](howto-configure-server-logs-in-portal.md)
- [Como configurar logs de consulta lentos no CLI do Azure](howto-configure-server-logs-in-cli.md).
