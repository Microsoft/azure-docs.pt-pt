---
title: Logs do servidor para o banco de dados do Azure para MariaDB
description: Descreve os logs disponíveis no banco de dados do Azure para MariaDB e os parâmetros disponíveis para habilitar diferentes níveis de log.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 10dbd4d7fa838ee7f8a3f70b3caadb570877d685
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259974"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Logs de consulta lentos no banco de dados do Azure para MariaDB
No banco de dados do Azure para MariaDB, o log de consultas lentas está disponível para os usuários. Não há suporte para o acesso ao log de transações. O log de consultas lentas pode ser usado para identificar gargalos de desempenho para solução de problemas.

Para obter mais informações sobre o log de consultas lentas, consulte a documentação do MariaDB para o [log de consultas lentas](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-slow-query-logs"></a>Acessar logs de consulta lentos
Você pode listar e baixar logs de consultas lentas do banco de dados do Azure para MariaDB usando o portal do Azure e o CLI do Azure.

Na portal do Azure, selecione o banco de dados do Azure para o servidor MariaDB. No cabeçalho **monitoramento** , selecione a página **logs do servidor** .

Para obter mais informações sobre CLI do Azure, consulte [configurar e acessar logs de servidor usando CLI do Azure](howto-configure-server-logs-cli.md).

## <a name="log-retention"></a>Retenção de log
Os logs estão disponíveis por até sete dias a partir da criação. Se o tamanho total dos logs disponíveis exceder 7 GB, os arquivos mais antigos serão excluídos até que haja espaço disponível.

Os logs são girados a cada 24 horas ou 7 GB, o que ocorrer primeiro.

## <a name="configure-slow-query-logging"></a>Configurar o log de consultas lentas
Por padrão, o log de consultas lentas está desabilitado. Para habilitá-lo, defina slow_query_log como ON.

Outros parâmetros que você pode ajustar incluem:

- **long_query_time**: se uma consulta demorar mais do que long_query_time (em segundos) em que a consulta é registrada. O padrão é 10 segundos.
- **log_slow_admin_statements**: se on inclui instruções administrativas como ALTER_TABLE e ANALYZE_TABLE nas instruções gravadas no slow_query_log.
- **log_queries_not_using_indexes**: determina se as consultas que não usam índices são registradas no slow_query_log
- **log_throttle_queries_not_using_indexes**: Esse parâmetro limita o número de consultas que não são de índice que podem ser gravadas no log de consultas lentas. Esse parâmetro entra em vigor quando log_queries_not_using_indexes é definido como ON.

Consulte a [documentação do log de consulta lenta](https://mariadb.com/kb/en/library/slow-query-log-overview/) do MariaDB para obter descrições completas dos parâmetros de log de consulta lenta.

## <a name="diagnostic-logs"></a>Registos de diagnósticos
O banco de dados do Azure para MariaDB é integrado a logs de diagnóstico Azure Monitor. Depois de habilitar os logs de consulta lentos em seu servidor MariaDB, você pode optar por que eles sejam emitidos para Azure Monitor logs, hubs de eventos ou armazenamento do Azure. Para saber mais sobre como habilitar os logs de diagnóstico, consulte a seção como da [documentação dos logs de diagnóstico](../azure-monitor/platform/resource-logs-overview.md).

> [!IMPORTANT]
> Esse recurso de diagnóstico para logs de servidor só está disponível nos [tipos de preço](concepts-pricing-tiers.md)uso geral e com otimização de memória.

A tabela a seguir descreve o que está em cada log. Dependendo do método de saída, os campos incluídos e a ordem na qual eles aparecem podem variar.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated`HORÁRIO | Carimbo de data/hora quando o log foi gravado em UTC |
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
| `start_time_t`HORÁRIO | Hora em que a consulta começou |
| `query_time_s` | Tempo total que a consulta levou para ser executada |
| `lock_time_s` | Tempo total de bloqueio da consulta |
| `user_host_s` | Nome de utilizador |
| `rows_sent_s` | Número de linhas enviadas |
| `rows_examined_s` | Número de linhas examinadas |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Inserir ID |
| `sql_text_s` | Consulta completa |
| `server_id_s` | ID do servidor |
| `thread_id_s` | ID do thread |
| `\_ResourceId` | URI de recurso |

## <a name="next-steps"></a>Passos seguintes
- [Como configurar e acessar logs de servidor do portal do Azure](howto-configure-server-logs-portal.md).
