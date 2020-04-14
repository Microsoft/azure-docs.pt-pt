---
title: Registos de consultas lentas - Base de Dados Azure para MariaDB
description: Descreve os registos disponíveis na Base de Dados Azure para o MariaDB e os parâmetros disponíveis para permitir diferentes níveis de exploração madeireira.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: ffd4ab463080001dbab5b0ed9ece69c4b5f91382
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272088"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Registos de consultas lentas na Base de Dados Azure para MariaDB
Na Base de Dados Azure para MariaDB, o registo de consulta lenta está disponível para os utilizadores. O acesso ao registo de transações não é suportado. O registo de consulta lenta pode ser usado para identificar estrangulamentos de desempenho para resolução de problemas.

Para obter mais informações sobre o registo de consultas lentas, consulte a documentação mariaDB para registo de [consultas lentas](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="configure-slow-query-logging"></a>Configure a exploração de consultas lentas
Por defeito, o registo de consulta lenta é desativado. Para o ativar, coloque-o `slow_query_log` em ON. Isto pode ser ativado utilizando o portal Azure ou o Azure CLI. 

Outros parâmetros que pode ajustar incluem:

- **long_query_time:** se uma consulta demorar mais do que long_query_time (em segundos) que a consulta é registada. A predefinição é de 10 segundos.
- **log_slow_admin_statements:** se a ON incluir declarações administrativas como ALTER_TABLE e ANALYZE_TABLE nas declarações escritas à slow_query_log.
- **log_queries_not_using_indexes:** determina se as consultas que não utilizam índices estão registadas na slow_query_log
- **log_throttle_queries_not_using_indexes**: Este parâmetro limita o número de consultas não indexadas que podem ser escritas no registo de consulta lenta. Este parâmetro entra em vigor quando log_queries_not_using_indexes está definido para ON.
- **log_output:** se "File", permitir que o registo de consulta lenta seja escrito tanto no armazenamento do servidor local como nos registos de diagnóstico do Monitor Azure. Se "Nenhum", o registo de consulta lenta só será escrito para registos de diagnóstico do Monitor Azure. 

> [!IMPORTANT]
> Se as suas tabelas não `log_queries_not_using_indexes` `log_throttle_queries_not_using_indexes` estiverem indexadas, a definição dos parâmetros e parâmetros para ON pode afetar o desempenho do MariaDB, uma vez que todas as consultas que correm contra estas tabelas não indexadas serão escritas para o registo de consulta lenta.<br><br>
> Se planeia registar consultas lentas durante um longo período de `log_output` tempo, recomenda-se que se ajuste a "Nenhuma". Se definidos para "File", estes registos são escritos para o armazenamento do servidor local e podem afetar o desempenho de MariaDB. 

Consulte a [documentação](https://mariadb.com/kb/en/library/slow-query-log-overview/) de registo de consulta lenta MariaDB para obter descrições completas dos parâmetros de registo de consultas lentas.

## <a name="access-slow-query-logs"></a>Aceder a registos de consultas lentas
Existem duas opções para aceder a registos de consultas lentas na Base de Dados Azure para MariaDB: armazenamento de servidores locais ou Registos de Diagnóstico do Monitor Azure. Isto é definido `log_output` com o parâmetro.

Para armazenamento de servidorlocal, pode listar e transferir registos de consultas lentas utilizando o portal Azure ou o Azure CLI. No portal Azure, navegue para o seu servidor no portal Azure. Na rubrica **Monitoring,** selecione a página Registos do **Servidor.** Para obter mais informações sobre o Azure CLI, consulte os registos de [configuração e de acesso do servidor utilizando o Azure CLI](howto-configure-server-logs-cli.md). 

Os registos de diagnóstico do Monitor Azure permitem-lhe canalizar registos de consultas lentas para registos de monitores Azure (Log Analytics), Armazenamento Azure ou Centros de Eventos. Veja [abaixo](concepts-server-logs.md#diagnostic-logs) para mais informações.

## <a name="local-server-storage-log-retention"></a>Retenção de registo de armazenamento de servidorlocal
Ao iniciar sessão no armazenamento local do servidor, os registos estão disponíveis até sete dias a partir da sua criação. Se o tamanho total dos registos disponíveis exceder 7 GB, então os ficheiros mais antigos são eliminados até que o espaço esteja disponível.

Os registos são rodados a cada 24 horas ou 7 GB, o que vier primeiro.

> [!Note]
> A retenção de registoacima não se aplica aos registos que são canalizados utilizando registos de diagnóstico do Monitor Azure. Pode alterar o período de retenção para os sumidouros de dados que estão a ser emitidos (ex. Armazenamento Azure).

## <a name="diagnostic-logs"></a>Registos de diagnósticos
A Base de Dados Azure para MariaDB está integrada com registos de diagnóstico do Monitor Azure. Depois de ter ativado registos de consultas lentas no seu servidor MariaDB, pode optar por que sejam emitidos para registos do Monitor Azure, Hubs de Eventos ou Armazenamento Azure. Para saber mais sobre como ativar os registos de diagnóstico, consulte como secção da documentação de [registos](../azure-monitor/platform/platform-logs-overview.md)de diagnóstico .

A tabela seguinte descreve o que está em cada tronco. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua identificação do inquilino |
| `SourceSystem` | `Azure` |
| `TimeGenerated`[UTC] | Carimbo de tempo quando o registo foi gravado na UTC |
| `Type` | Tipo de tronco. Sempre`AzureDiagnostics` |
| `SubscriptionId` | GUID para a subscrição a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos a que o servidor pertence |
| `ResourceProvider` | Nome do fornecedor de recursos. Sempre`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Recurso URI |
| `Resource` | Nome do servidor |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nome do servidor |
| `start_time_t`[UTC] | Hora da consulta começar |
| `query_time_s` | O tempo total que a consulta demorou a executar |
| `lock_time_s` | O tempo total da consulta foi bloqueado |
| `user_host_s` | Nome de utilizador |
| `rows_sent_s` | Número de filas enviadas |
| `rows_examined_s` | Número de linhas examinadas |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Inserir ID |
| `sql_text_s` | Consulta completa |
| `server_id_s` | ID do servidor |
| `thread_id_s` | ID da linha |
| `\_ResourceId` | Recurso URI |

> [!Note]
> Para `sql_text`, o registo será truncado se exceder 2048 caracteres.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analisar registos em registos do Monitor Azure

Uma vez que os seus registos de consulta lenta são canalizados para registos do Monitor Azure através de registos de diagnóstico, pode efetuar uma análise mais aprofundada das suas consultas lentas. Abaixo estão algumas consultas de amostra para ajudá-lo a começar. Certifique-se de atualizar o abaixo com o nome do servidor.

- Consultas superiores a 10 segundos num determinado servidor

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Lista rindo as 5 consultas mais longas num determinado servidor

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Resumir consultas lentas pelo mínimo, máximo, média e tempo padrão de consulta de desvio em um determinado servidor

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Grafe a distribuição de consulta lenta num determinado servidor

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Consultas de exibição superiores a 10 segundos em todos os servidores MariaDB com Registos de Diagnóstico ativados

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Passos Seguintes
- [Como configurar registos de consulta lenta do portal Azure](howto-configure-server-logs-portal.md)
- [Como configurar registos de consulta lenta do Azure CLI](howto-configure-server-logs-cli.md)
