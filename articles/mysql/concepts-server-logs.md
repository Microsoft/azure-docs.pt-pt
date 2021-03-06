---
title: Registos de consulta lenta - Base de Dados Azure para MySQL
description: Descreve os registos de consulta lenta disponíveis na Base de Dados Azure para o MySQL e os parâmetros disponíveis para permitir diferentes níveis de registo.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 11/6/2020
ms.openlocfilehash: e3342b38f782b718cefd63295ef4d4d26b525058
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259052"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Registos de consultas lentas na Base de Dados do Azure para MySQL
Na Base de Dados Azure para o MySQL, o registo de consulta lenta está disponível para os utilizadores. O acesso ao registo de transações não é suportado. O registo de consulta lenta pode ser usado para identificar estrangulamentos de desempenho para a resolução de problemas.

Para obter mais informações sobre o registo de consulta lenta mySQL, consulte a [secção de registo de consulta lenta](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)do manual de referência mySQL .

Quando [a Loja de Consultas](concepts-query-store.md) estiver ativada no seu servidor, poderá ver as consultas como " `CALL mysql.az_procedure_collect_wait_stats (900, 30);` registadas nos seus registos de consulta lenta. Este comportamento é esperado à medida que a funcionalidade Da Loja de Consultas recolhe estatísticas sobre as suas consultas. 

## <a name="configure-slow-query-logging"></a>Configurar a consulta lenta 
Por predefinição, o registo de consulta lenta é desativado. Para a capacitá-lo, desatado `slow_query_log` para ON. Isto pode ser ativado utilizando o portal Azure ou O Azure CLI. 

Outros parâmetros que pode ajustar incluem:

- **long_query_time:** se uma consulta demorar mais do que long_query_time (em segundos) essa consulta é registada. A predefinição é de 10 segundos.
- **log_slow_admin_statements**: se a ON incluir declarações administrativas como ALTER_TABLE e ANALYZE_TABLE nas declarações escritas à slow_query_log.
- **log_queries_not_using_indexes**: determina se as consultas que não utilizam índices são registadas no slow_query_log
- **log_throttle_queries_not_using_indexes**: Este parâmetro limita o número de consultas não indexados que podem ser escritas para o registo de consulta lenta. Este parâmetro entra em vigor quando log_queries_not_using_indexes está definido para ON.
- **log_output:** se "File", permite que o registo de consulta lenta seja escrito tanto para o armazenamento do servidor local como para os Registos de Diagnóstico do Monitor Azure. Se for “Nenhum”, o registo de consultas lentas só será escrito nos Registos de Diagnósticos do Azure Monitor. 

> [!IMPORTANT]
> Se as suas tabelas não estiverem indexadas, definir os `log_queries_not_using_indexes` parâmetros e os parâmetros para ON pode afetar o `log_throttle_queries_not_using_indexes` desempenho do MySQL, uma vez que todas as consultas que correm contra estas tabelas não indexadas serão escritas para o registo de consulta lenta.<br><br>
> Se planeia registar consultas lentas durante um longo período de tempo, recomenda-se que se ajuste `log_output` a "Nenhuma". Se definidos como "Ficheiro", estes registos são escritos para o armazenamento do servidor local e podem afetar o desempenho do MySQL. 

Consulte a [documentação](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) de registo de consulta lenta MySQL para obter descrições completas dos parâmetros de registo de consulta lenta.

## <a name="access-slow-query-logs"></a>Aceder a registos de consulta lenta
Existem duas opções para aceder a registos de consulta lenta na Base de Dados Azure para o MySQL: armazenamento de servidor local ou Registos de Diagnóstico do Monitor Azure. Isto é definido usando o `log_output` parâmetro.

Para o armazenamento de servidores locais, pode listar e descarregar registos de consulta lenta utilizando o portal Azure ou o CLI Azure. No portal Azure, navegue para o seu servidor no portal Azure. No título **de Monitorização,** selecione a página **'Registos do Servidor'.** Para obter mais informações sobre o Azure CLI, consulte [configurar e aceder a registos de consultas lentas utilizando O Azure CLI](howto-configure-server-logs-in-cli.md). 

Os Registos de Diagnóstico do Monitor Azure permitem-lhe canalizar registos de consulta lenta para registos de monitores Azure (Log Analytics), Azure Storage ou Event Hubs. Veja [abaixo](concepts-server-logs.md#diagnostic-logs) mais informações.

## <a name="local-server-storage-log-retention"></a>Retenção de registos de armazenamento de servidores locais
Ao iniciar sessão no armazenamento local do servidor, os registos estão disponíveis até sete dias após a sua criação. Se o tamanho total dos registos disponíveis exceder 7 GB, então os ficheiros mais antigos são eliminados até que o espaço esteja disponível. O limite de armazenamento de 7 GB para os registos do servidor está disponível livre de custos e não pode ser prolongado. 

Os troncos são rodados a cada 24 horas ou 7 GB, o que vier primeiro.

> [!Note]
> A retenção de registos acima não se aplica aos registos que são canalizados utilizando registos de diagnóstico do Monitor Azure. Pode alterar o período de retenção para os sumidouros de dados a que são emitidos (ex. Armazenamento Azure).

## <a name="diagnostic-logs"></a>Registos de diagnósticos
A Base de Dados Azure para o MySQL está integrada com registos de diagnóstico do Monitor Azure. Uma vez que tenha ativado registos de consulta lenta no seu servidor MySQL, pode optar por esemitir os registos do Azure Monitor, Dos Centros de Eventos ou do Azure Storage. Para saber mais sobre como ativar registos de diagnóstico, consulte a secção da documentação dos [registos](../azure-monitor/essentials/platform-logs-overview.md)de diagnóstico .

A tabela seguinte descreve o que está em cada registo. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

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
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Inserir ID |
| `sql_text_s` | Consulta completa |
| `server_id_s` | ID do servidor |
| `thread_id_s` | ID de fio |
| `\_ResourceId` | URI de recursos |

> [!Note]
> Para `sql_text` , log será truncado se exceder os caracteres de 2048.

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
    
## <a name="next-steps"></a>Passos Seguintes
- [Como configurar registos de consulta lenta a partir do portal Azure](howto-configure-server-logs-in-portal.md)
- [Como configurar registos de consulta lenta do Azure CLI](howto-configure-server-logs-in-cli.md)
