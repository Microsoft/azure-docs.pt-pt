---
title: Logs de servidor no banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como o banco de dados do Azure para PostgreSQL-um único servidor gera logs de consulta e de erro e como a retenção de log é configurada.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 17083029f2377037b99abfa3ce8371661eccb957
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029982"
---
# <a name="server-logs-in-azure-database-for-postgresql---single-server"></a>Logs de servidor no banco de dados do Azure para PostgreSQL-servidor único
A Base de Dados do Azure para PostgreSQL gera registos de consultas e de erros. Os logs de consulta e de erro podem ser usados para identificar, solucionar problemas e reparar erros de configuração e desempenho inferior. (O acesso aos logs de transações não está incluído). 

## <a name="configure-logging"></a>Configurar registro em log 
Você pode configurar o registro em log no servidor usando os parâmetros do servidor de registro em log. Em cada novo servidor, **log_checkpoints** e **log_connections** estão ativados por padrão. Há parâmetros adicionais que você pode ajustar para atender às suas necessidades de registro em log: 

![Banco de dados do Azure para PostgreSQL-parâmetros de log](./media/concepts-server-logs/log-parameters.png)

Para obter mais informações sobre esses parâmetros, consulte o [relatório de erros e](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) a documentação de registro em log do PostgreSQL. Para saber como configurar os parâmetros do banco de dados do Azure para PostgreSQL, consulte a [documentação do portal](howto-configure-server-parameters-using-portal.md) ou a [documentação da CLI](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Acessar logs do servidor por meio do portal ou da CLI
Se você tiver habilitado os logs, poderá acessá-los no armazenamento de log do banco de dados do Azure para PostgreSQL usando as APIs REST [portal do Azure](howto-configure-server-logs-in-portal.md), [CLI do Azure](howto-configure-server-logs-using-cli.md)e Azure. Os ficheiros de registo são executados de hora a hora ou por cada tamanho de 100 MB, o que ocorrer primeiro. Você pode definir o período de retenção para esse armazenamento de log usando o parâmetro **log @ no__t-1retention @ no__t-2period** associado ao servidor. O valor padrão é 3 dias; o valor máximo é de 7 dias. O servidor deve ter armazenamento alocado suficiente para manter os arquivos de log. (Esse parâmetro de retenção não controla os logs de diagnóstico do Azure.)


## <a name="diagnostic-logs"></a>Registos de diagnósticos
O banco de dados do Azure para PostgreSQL é integrado com Azure Monitor logs de diagnóstico. Depois de habilitar os logs no servidor PostgreSQL, você pode optar por que eles sejam emitidos para [Azure monitor logs](../azure-monitor/log-query/log-query-overview.md), hubs de eventos ou armazenamento do Azure. 

> [!IMPORTANT]
> Esse recurso de diagnóstico para logs de servidor só está disponível nos [tipos de preço](concepts-pricing-tiers.md)uso geral e com otimização de memória.

Para habilitar os logs de diagnóstico usando o portal do Azure:

   1. No portal, vá para *configurações de diagnóstico* no menu de navegação do seu servidor do Postgres.
   2. Selecione *Adicionar configuração de diagnóstico*.
   3. Nomeie essa configuração. 
   4. Selecione seu local de downstream preferido (conta de armazenamento, Hub de eventos, log Analytics). 
   5. Selecione os tipos de dados desejados.
   6. Salve sua configuração.

A tabela a seguir descreve o que está em cada log. Dependendo do ponto de extremidade de saída escolhido, os campos incluídos e a ordem na qual eles aparecem podem variar. 

|**Campo** | **Descrição** |
|---|---|
| TenantId | Sua ID de locatário |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Carimbo de data/hora quando o log foi gravado em UTC |
| Type | Tipo do log. Sempre `AzureDiagnostics` |
| SubscriptionId | GUID da assinatura à qual o servidor pertence |
| ResourceGroup | Nome do grupo de recursos ao qual o servidor pertence |
| ResourceProvider | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | URI de recurso |
| Resource | Nome do servidor |
| Category | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Nível de log, exemplo: LOG, ERRO, AVISO |
| Message | Mensagem de log principal | 
| Domain | Versão do servidor, exemplo: Postgres-10 |
| Detalhe | Mensagem de log secundária (se aplicável) |
| ColumnName | Nome da coluna (se aplicável) |
| SchemaName | Nome do esquema (se aplicável) |
| DatatypeName | Nome do tipo de dados (se aplicável) |
| LogicalServerName | Nome do servidor | 
| _ResourceId | URI de recurso |
| Prefix | Prefixo da linha de log |



## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre como acessar logs do [portal do Azure](howto-configure-server-logs-in-portal.md) ou [CLI do Azure](howto-configure-server-logs-using-cli.md).
- Saiba mais sobre [preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).
