---
title: Logs de consulta lentos-banco de dados do Azure para MariaDB
description: Descreve os logs disponíveis no banco de dados do Azure para MariaDB e os parâmetros disponíveis para habilitar diferentes níveis de log.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: b38838c20e4ab18b64cabcb2749ec39163f1b52d
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76515059"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Logs de consulta lentos no banco de dados do Azure para MariaDB
No banco de dados do Azure para MariaDB, o log de consultas lentas está disponível para os usuários. Não há suporte para o acesso ao log de transações. O log de consultas lentas pode ser usado para identificar gargalos de desempenho para solução de problemas.

Para obter mais informações sobre o log de consultas lentas, consulte a documentação do MariaDB para o [log de consultas lentas](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-slow-query-logs"></a>Acessar logs de consulta lentos
Você pode listar e baixar logs de consultas lentas do banco de dados do Azure para MariaDB usando o portal do Azure e o CLI do Azure.

Na portal do Azure, selecione o banco de dados do Azure para o servidor MariaDB. No cabeçalho **monitoramento** , selecione a página **logs do servidor** .

Para obter mais informações sobre CLI do Azure, consulte [configurar e acessar logs de servidor usando CLI do Azure](howto-configure-server-logs-cli.md).

Da mesma forma, você pode canalizar os logs para Azure Monitor usando os logs de diagnóstico. Consulte [abaixo](concepts-server-logs.md#diagnostic-logs) para obter mais informações.

## <a name="log-retention"></a>Retenção de log
Os logs estão disponíveis por até sete dias a partir da criação. Se o tamanho total dos logs disponíveis exceder 7 GB, os arquivos mais antigos serão excluídos até que haja espaço disponível.

Os logs são girados a cada 24 horas ou 7 GB, o que ocorrer primeiro.

## <a name="configure-slow-query-logging"></a>Configurar o log de consultas lentas
Por padrão, o log de consultas lentas está desabilitado. Para habilitá-lo, defina slow_query_log como ativado.

Outros parâmetros que você pode ajustar incluem:

- **long_query_time**: se uma consulta demorar mais do que long_query_time (em segundos) em que a consulta é registrada. A predefinição é 10 segundos.
- **log_slow_admin_statements**: se on inclui instruções administrativas como ALTER_TABLE e ANALYZE_TABLE nas instruções gravadas no slow_query_log.
- **log_queries_not_using_indexes**: determina se as consultas que não usam índices são registradas no slow_query_log
- **log_throttle_queries_not_using_indexes**: esse parâmetro limita o número de consultas que não são de índice que podem ser gravadas no log de consultas lentas. Esse parâmetro entra em vigor quando log_queries_not_using_indexes é definido como ON.
- **log_output**: se "File", permite que o log de consultas lentas seja gravado no armazenamento do servidor local e em Azure monitor logs de diagnóstico. Se "None", o log de consultas lentas só será gravado em Azure Monitor logs de diagnóstico. 

> [!IMPORTANT]
> Se as tabelas não estiverem indexadas, definir os parâmetros `log_queries_not_using_indexes` e `log_throttle_queries_not_using_indexes` como ON poderá afetar o desempenho do MariaDB, pois todas as consultas em execução nessas tabelas não indexadas serão gravadas no log de consultas lentas.<br><br>
> Se você planeja registrar em log consultas lentas por um longo período de tempo, é recomendável definir `log_output` como "None". Se definido como "File", esses logs são gravados no armazenamento do servidor local e podem afetar o desempenho do MariaDB. 

Consulte a [documentação do log de consulta lenta](https://mariadb.com/kb/en/library/slow-query-log-overview/) do MariaDB para obter descrições completas dos parâmetros de log de consulta lenta.

## <a name="diagnostic-logs"></a>Registos de diagnósticos
O banco de dados do Azure para MariaDB é integrado a logs de diagnóstico Azure Monitor. Depois de habilitar os logs de consulta lentos em seu servidor MariaDB, você pode optar por que eles sejam emitidos para Azure Monitor logs, hubs de eventos ou armazenamento do Azure. Para saber mais sobre como habilitar os logs de diagnóstico, consulte a seção como da [documentação dos logs de diagnóstico](../azure-monitor/platform/platform-logs-overview.md).

> [!IMPORTANT]
> Esse recurso de diagnóstico para logs de servidor só está disponível nos [tipos de preço](concepts-pricing-tiers.md)uso geral e com otimização de memória.

A tabela a seguir descreve o que está em cada log. Dependendo do método de saída, os campos incluídos e a ordem na qual eles aparecem podem variar.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Carimbo de data/hora quando o log foi gravado em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID da assinatura à qual o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nome do servidor |
| `start_time_t` [UTC] | Hora em que a consulta começou |
| `query_time_s` | Tempo total que a consulta levou para ser executada |
| `lock_time_s` | Tempo total de bloqueio da consulta |
| `user_host_s` | Nome de utilizador |
| `rows_sent_s` | Número de linhas enviadas |
| `rows_examined_s` | Número de linhas examinadas |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Inserir ID |
| `sql_text_s` | Consulta completa |
| `server_id_s` | ID do servidor |
| `thread_id_s` | ID do Thread |
| `\_ResourceId` | URI de recurso |

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

- Exibir consultas com mais de 10 segundos em todos os servidores MariaDB com logs de diagnóstico habilitados

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Próximos Passos
- [Como configurar logs de consulta lentos no portal do Azure](howto-configure-server-logs-portal.md)
- [Como configurar logs de consulta lentos no CLI do Azure](howto-configure-server-logs-cli.md)
