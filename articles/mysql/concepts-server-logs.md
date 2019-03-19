---
title: Registos do servidor da base de dados do Azure para MySQL
description: Descreve os registos disponíveis no banco de dados do Azure para MySQL e os parâmetros disponíveis para ativar os níveis de registo diferente.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: b1b5dffed0a82e3e3c91efd4024bafdc64f0d3d2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119042"
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Registos do servidor na base de dados do Azure para MySQL
Na base de dados do Azure para MySQL, o log de consulta lenta está disponível para os utilizadores. Acesso ao registo de transação não é suportado. O log de consulta lenta pode ser utilizado para identificar afunilamentos de desempenho para resolução de problemas. 

Para obter mais informações sobre o registo de consulta lenta MySQL, consulte o manual de referência de MySQL [lento secção de registo de consulta](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-server-logs"></a>Aceder a registos do servidor
Pode listar e transferir a base de dados do Azure para os registos do servidor MySQL com o portal do Azure e a CLI do Azure.

No portal do Azure, selecione a base de dados do Azure para o servidor MySQL. Sob o **monitorização** cabeçalho, selecione a **os registos do servidor** página.

Para obter mais informações sobre a CLI do Azure, consulte [acesso e configurar os registos do servidor com a CLI do Azure](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Retenção do registo
Os registos estão disponíveis até sete dias desde a sua criação. Se o tamanho total dos registos disponíveis exceder 7 GB, em seguida, os mais antigos ficheiros são eliminados até que o espaço está disponível. 

Os registos são revezados cada 24 horas ou 7 GB, o que ocorrer primeiro.


## <a name="configure-logging"></a>Configurar o registo 
Por predefinição, o registo de consulta lenta está desativado. Para ativá-lo, defina slow_query_log on.

Outros parâmetros que pode ajustar incluem:

- **long_query_time**: se uma consulta demora mais tempo do que long_query_time (em segundos) dessa consulta é registada. A predefinição é 10 segundos.
- **log_slow_admin_statements**: se no inclui instruções administrativas como ALTER_TABLE e ANALYZE_TABLE nas instruções escritas para o slow_query_log.
- **log_queries_not_using_indexes**: determina se as consultas que não utilizam os índices são registadas a slow_query_log
- **log_throttle_queries_not_using_indexes**: Este parâmetro limita o número de consultas não índice que podem ser gravados no log de consulta lenta. Este parâmetro entra em vigor quando log_queries_not_using_indexes está definido como ligado.

Consulte o MySQL [lento de documentação do log de consulta](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) para descrições completas dos parâmetros de registo de consulta lenta.

## <a name="diagnostic-logs"></a>Registos de diagnósticos
Base de dados do Azure para MySQL está integrado com os registos de diagnóstico do Azure Monitor. Assim que tiver ativado os registos de consulta lenta no seu servidor MySQL, pode optar por fazê-los emitidos para os registos do Azure Monitor, os Hubs de eventos ou armazenamento do Azure. Para saber mais sobre como ativar os registos de diagnóstico, ver como a secção a [documentação de registos de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md).

> [!IMPORTANT]
> Esta funcionalidade de diagnóstico para os registos do servidor só está disponível nos fins gerais e com otimização de memória [escalões de preço](concepts-pricing-tiers.md).

A tabela seguinte descreve as novidades em cada registo. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

| **Propriedade** | **Descrição** |
|---|---|
| TenantId | O ID de inquilino |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Carimbo de hora quando o registo foi registado em UTC |
| Type | Tipo do registo. Sempre `AzureDiagnostics` |
| SubscriptionId | GUID da subscrição que o servidor pertence a |
| ResourceGroup | Nome do grupo de recursos do servidor pertence a |
| ResourceProvider | Nome do fornecedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| ResourceType | `Servers` |
| ResourceId | URI do recurso |
| Recurso | Nome do servidor |
| Categoria | `MySqlSlowLogs` |
| OperationName | `LogEvent` |
| Logical_server_name_s | Nome do servidor |
| start_time_t [UTC] | Tempo que começou a consulta |
| query_time_s | A consulta demorou a executar o tempo total |
| lock_time_s | Tempo total que a consulta foi bloqueada |
| user_host_s | Nome de utilizador |
| rows_sent_s | Número de linhas enviados |
| rows_examined_s | Número de linhas examinado |
| last_insert_id_s | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| insert_id_s | Inserir id |
| sql_text_s | Consulta completa |
| server_id_s | Id do servidor |
| thread_id_s | Id do thread |
| \_ResourceId | URI do recurso |

## <a name="next-steps"></a>Próximos Passos
- [Como configurar e aceder a registos do servidor a partir da CLI do Azure](howto-configure-server-logs-in-cli.md).
